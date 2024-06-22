---
title: "eBPF와 Go를 사용한 투명 프록시 구현 방법"
description: ""
coverImage: "/assets/img/2024-06-23-TransparentProxyImplementationusingeBPFandGo_0.png"
date: 2024-06-23 01:04
ogImage: 
  url: /assets/img/2024-06-23-TransparentProxyImplementationusingeBPFandGo_0.png
tag: Tech
originalTitle: "Transparent Proxy Implementation using eBPF and Go"
link: "https://medium.com/gitconnected/building-a-transparent-proxy-with-ebpf-50a012237e76"
---


## eBPF를 활용한 투명 프록시 구축

투명 프록시는 인라인 프록시로도 알려져 있으며, 클라이언트 요청을 가로채어 수정없이 리디렉션하는 기술입니다. 사용자에게는 보이지 않고 네트워크 설정을 조정할 필요도 없이 작동하므로 사용자들이 그 존재를 알 수 없습니다. 이 기술은 네트워크 관리, 보안 정책 강제, 트래픽 모니터링, 최적화 등에 매우 유용합니다. 투명 프록시는 콘텐츠 필터링, 캐시 가속, 트래픽 제어, 로드 밸런싱 등 다양한 기능을 실행할 수 있습니다. 투명 프록시를 구현하는 데 자주 사용되는 기술로는 TPROXY, NAT 등이 있습니다.

이 블로그 게시물에서는 eBPF를 사용하여 투명 프록시의 구현을 설명하겠습니다. 구체적으로는 ebpf-go 패키지와 함께 Golang을 활용할 것입니다.

<img src="/assets/img/2024-06-23-TransparentProxyImplementationusingeBPFandGo_0.png" />

<div class="content-ad"></div>

# 투명 프록시 구현에서의 eBPF

Extended Berkeley Packet Filter (eBPF)는 Linux 커널에서 샌드박스화된 프로그램을 실행할 수 있는 뛰어난 도구로, 투명 프록시를 구현하는 데 강력한 기능을 제공합니다. 이 능력은 사용자 공간과 커널 공간 간의 컨텍스트 전환 오버헤드 없이 고성능 패킷 처리와 실시간 트래픽 조작이 가능하도록 합니다.

eBPF를 사용하여 투명 프록시를 구현하는 과정에는 네트워크 가로채기와 전달의 다른 측면을 담당하는 세 가지 서로 다른 eBPF 프로그램이 포함됩니다:

- 연결 설정 시 주소 교체: 첫 번째 eBPF 프로그램인 cgroup/connect4은 connect 시스템 콜에 연결됩니다. 클라이언트가 대상 서버에 연결을 시도할 때 이 프로그램은 연결 시도를 가로채어 대상의 IP 주소와 포트를 로컬 투명 프록시의 것으로 교체합니다. 이 리디렉션은 클라이언트에게 완전히 투명합니다. 동시에 원래 대상 주소와 포트는 map_socks eBPF 맵 내에 저장되어 있어 다른 eBPF 프로그램이 나중에 이 정보를 참조할 수 있도록 합니다.
- 연결 후 소스 주소 기록: 두 번째 eBPF 프로그램인 sockops는 프록시와 프록시 간의 연결이 성공적으로 설정된 후에 실행됩니다. 주요 기능은 연결의 소스 주소와 포트를 기록하는 것입니다. 이 정보는 map_socks eBPF 맵의 해당 항목에 업데이트됩니다. 추가적으로, 소스 포트와 소켓의 쿠키 (고유 식별자)는 map_ports eBPF 맵에 매핑되어 모든 필요한 연결 세부 정보가 다른 eBPF 프로그램에서 사용 가능하도록 보장됩니다. 이 단계는 네트워크 연결의 상태를 유지하기 위해 중요합니다.
- 원래 대상 정보를 기반으로 전달: 세 번째 eBPF 프로그램인 cgroup/getsockopt은 Pipy 프록시가 getsockopt 호출을 사용하여 원래 대상 정보를 조회할 때 활성화됩니다. 이 프로그램은 source port를 사용하여 map_ports에서 원래 소켓의 쿠키를 검색한 다음 map_socks에 저장된 원래 대상 정보에 액세스합니다. 이 정보를 사용하여 원래 대상 서버와의 연결을 설정하고 클라이언트 요청을 전달합니다. 이로써 트래픽이 프록시에서 처리된 후 의도한 대상으로 투명하게 리디렉션되도록 보장됩니다.

<div class="content-ad"></div>

세 프로그램 모두 특정 cgroup에 연결되어 있습니다. 우리의 경우에는 루트 cgroup이지만 다른 것일 수도 있습니다. 이는 해당 그룹 내의 프로세스가 지정된 시스템 호출 (syscalls)을 실행할 때에만 활성화됨을 보장합니다. 현재, 우리의 구성은 TCP IPv4 연결만 프록시하고 있지만 다른 프로토콜에 대해 적응 가능합니다.

이론적으로는 여기까지입니다. 이제 코드를 살펴봅시다.

## 사용자 공간 코드

```js
패키지 main

//go:generate go run github.com/cilium/ebpf/cmd/bpf2go -type Config proxy proxy.c

import (
 "fmt"
 "io"
 "log"
 "net"
 "os"
 "syscall"
 "time"
 "unsafe"

 "github.com/cilium/ebpf"
 "github.com/cilium/ebpf/link"
 "github.com/cilium/ebpf/rlimit"
)

const (
 CGROUP_PATH = "/sys/fs/cgroup" // 루트 cgroup 경로
 PROXY_PORT      = 18000 // 프록시 서버가 수신 대기하는 포트
 SO_ORIGINAL_DST = 80 // 원본 대상 주소를 가져오기 위한 소켓 옵션
)

// SockAddrIn은 SO_ORIGINAL_DST에 의해 "검색된" IPv4 sockaddr_in 구조체를 보관하는 구조체입니다.
type SockAddrIn struct {
 SinFamily uint16
 SinPort   [2]byte
 SinAddr   [4]byte
 // sockaddr_in의 크기와 일치하도록 패딩 처리
 Pad [8]byte
}

// getsockopt에 대한 도우미 함수
func getsockopt(s int, level int, optname int, optval unsafe.Pointer, optlen *uint32) (err error) {
 _, _, e := syscall.Syscall6(syscall.SYS_GETSOCKOPT, uintptr(s), uintptr(level), uintptr(optname), uintptr(optval), uintptr(unsafe.Pointer(optlen)), 0)
 if e != 0 {
  return e
 }
 return
}

// HTTP 프록시 요청 핸들러
func handleConnection(conn net.Conn) {
 defer conn.Close()

 // RawConn을 사용하면 Go에서 기본 소켓 파일 기술자에 대한 저수준 작업을 수행하는 것이 필요합니다.
 // 이를 통해 소켓이 설정한 원본 대상 주소를 getsockopt을 사용하여 가져올 수 있습니다.
 rawConn, err := conn.(*net.TCPConn).SyscallConn()
 if err != nil {
  log.Printf("Raw 연결을 가져오지 못했습니다: %v", err)
  return
 }

 var originalDst SockAddrIn
 // Control이 nil이 아닌 경우, 네트워크 연결을 만든 후 운영 체제에 바인딩하기 전에 호출됩니다.
 rawConn.Control(func(fd uintptr) {
  optlen := uint32(unsafe.Sizeof(originalDst))
  // SO_ORIGINAL_DST 옵션을 사용하여 시스템 호출을 수행하여 원본 대상 주소를 가져옵니다.
  err = getsockopt(int(fd), syscall.SOL_IP, SO_ORIGINAL_DST, unsafe.Pointer(&originalDst), &optlen)
  if err != nil {
   log.Printf("getsockopt SO_ORIGINAL_DST 실패: %v", err)
  }
 })

 targetAddr := net.IPv4(originalDst.SinAddr[0], originalDst.SinAddr[1], originalDst.SinAddr[2], originalDst.SinAddr[3]).String()
 targetPort := (uint16(originalDst.SinPort[0]) << 8) | uint16(originalDst.SinPort[1])

 fmt.Printf("원본 대상 주소: %s:%d\n", targetAddr, targetPort)

 // 원본 대상 주소가 프록시에서 접근 가능한지 확인합니다.
 targetConn, err := net.DialTimeout("tcp", fmt.Sprintf("%s:%d", targetAddr, targetPort), 5*time.Second)
 if err != nil {
  log.Printf("원본 대상에 연결할 수 없습니다: %v", err)
  return
 }
 defer targetConn.Close()

 fmt.Printf("%s에서 %s로 연결을 프록시합니다\n", conn.RemoteAddr(), targetConn.RemoteAddr())

 // 다음 코드는 두 데이터 전송 채널을 생성합니다:
  // - 클라이언트에서 타겟 서버로 (별도의 고루틴에서 처리).
  // - 타겟 서버에서 클라이언트로 (주요 고루틴에서 처리).
 go func() {
  _, err = io.Copy(targetConn, conn)
  if err != nil {
   log.Printf("타겟으로 데이터 복사 실패: %v", err)
  }
 }()
 _, err = io.Copy(conn, targetConn)
 if err != nil {
  log.Printf("타겟으로부터 데이터 복사 실패: %v", err)
 }
}

func main() {
 // 커널 버전이 5.11 미만인 경우 리소스 제한을 제거합니다.
 if err := rlimit.RemoveMemlock(); err != nil { 
  log.Print("Memlock 제거 중 오류 발생:", err)
 }

 // 컴파일된 eBPF ELF를 로드하고 커널로 로드
 // 참고: eBPF 프로그램을 고정시킬 수도 있습니다.
 var objs proxyObjects
 if err := loadProxyObjects(&objs, nil); err != nil {
   log.Print("eBPF 오브젝트 로드 오류:", err)
 }
 defer objs.Close()

 // eBPF 프로그램을 루트 cgroup에 연결
 connect4Link, err := link.AttachCgroup(link.CgroupOptions{
  Path:    CGROUP_PATH, 
  Attach:  ebpf.AttachCGroupInet4Connect,
  Program: objs.CgConnect4,
 })
 if err != nil {
   log.Print("CgConnect4 프로그램을 Cgroup에 연결 중 오류 발생:", err)
 }
 defer connect4Link.Close() 

 sockopsLink, err := link.AttachCgroup(link.CgroupOptions{
  Path:    CGROUP_PATH,
  Attach:  ebpf.AttachCGroupSockOps,
  Program: objs.CgSockOps,
 })
 if err != nil {
   log.Print("CgSockOps 프로그램을 Cgroup에 연결 중 오류 발생:", err)
 }
 defer sockopsLink.Close() 

 sockoptLink, err := link.AttachCgroup(link.CgroupOptions{
  Path:    CGROUP_PATH,
  Attach:  ebpf.AttachCGroupGetsockopt,
  Program: objs.CgSockOpt,
 })
 if err != nil {
   log.Print("CgSockOpt 프로그램을 Cgroup에 연결 중 오류 발생:", err)
 }
 defer sockoptLink.Close() 

 // 로컬호스트에 프록시 서버 시작합니다
 // 이 예제에서는 IPv4만을 보여주지만 IPv6에도 동일한 방법을 사용할 수 있습니다.
 proxyAddr := fmt.Sprintf("127.0.0.1:%d", PROXY_PORT)
 listener, err := net.Listen("tcp", proxyAddr)
 if err != nil {
  log.Fatalf("프록시 서버 시작 실패: %v", err)
 }
 defer listener.Close()

 // 프록시 서버 구성으로 proxyMaps 맵 업데이트합니다. 
 // 이는 프록시 서버 PID가 필요하기 때문에 필요합니다.
 var key uint32 = 0
 config := proxyConfig{
  ProxyPort: PROXY_PORT,
  ProxyPid: uint64(os.Getpid()),
 }
 err = objs.proxyMaps.MapConfig.Update(&key, &config, ebpf.UpdateAny)
 if err != nil {
  log.Fatalf("프록시맵 업데이트 실패: %v", err)
 }

 log.Printf("PID %d의 프록시 서버가 %s에서 수신 대기 중", os.Getpid(), proxyAddr)
 for {
  conn, err := listener.Accept()
  if err != nil {
   log.Printf("연결 수락 실패: %v", err)
   continue
  }

  go handleConnection(conn)
 }
}
```

<div class="content-ad"></div>

## 커널 스페이스 코드

```js
//go:build ignore
#include <stddef.h>
#include <linux/bpf.h>
#include <linux/netfilter_ipv4.h>
#include <linux/in.h>
#include <sys/socket.h>

#include "bpf-builtin.h"
#include "bpf-utils.h"

#undef bpf_printk
#define bpf_printk(fmt, ...)                            \
({                                                      \
        static const char ____fmt[] = fmt;              \
        bpf_trace_printk(____fmt, sizeof(____fmt),      \
                         ##__VA_ARGS__);                \
})

#define MAX_CONNECTIONS 20000

struct Config {
  __u16 proxy_port;
  __u64 proxy_pid;
};

struct Socket {
  __u32 src_addr;
  __u16 src_port;
  __u32 dst_addr;
  __u16 dst_port;
};

struct {
  int (*type)[BPF_MAP_TYPE_ARRAY];
  int (*max_entries)[1];
  __u32 *key;
  struct Config *value;
} map_config SEC(".maps");

struct {
  int (*type)[BPF_MAP_TYPE_HASH];
  int (*max_entries)[MAX_CONNECTIONS];
  __u64 *key;
  struct Socket *value;
} map_socks SEC(".maps");

struct {
  int (*type)[BPF_MAP_TYPE_HASH];
  int (*max_entries)[MAX_CONNECTIONS];
  __u16 *key;
  __u64 *value;
} map_ports SEC(".maps");

// 이 후킹은 프로세스가 connect() 시스템 호출 시 호출됩니다.
// 연결을 투명한 프록시로 리디렉션하지만 원래 대상 주소와 포트를 map_socks에 저장합니다.
SEC("cgroup/connect4")
int cg_connect4(struct bpf_sock_addr *ctx) {
  // IPv4 TCP 연결만 전달
  if (ctx->user_family != AF_INET) return 1;
  if (ctx->protocol != IPPROTO_TCP) return 1;

  // 프록시 자체를 프록시하지 않도록 함
  __u32 key = 0;
  struct Config *conf = bpf_map_lookup_elem(&map_config, &key);
  if (!conf) return 1;
  if ((bpf_get_current_pid_tgid() >> 32) == conf->proxy_pid) return 1;

  // 이 필드에는 connect() 시스템 호출에 전달된 IPv4 주소가 포함됩니다.
  // 즉, 이 소켓 대상 주소 및 포트에 연결
  __u32 dst_addr = ntohl(ctx->user_ip4);
  // 이 필드에는 connect() 시스템 호출에 전달된 포트 번호가 포함됩니다.
  __u16 dst_port = ntohl(ctx->user_port) >> 16;
  // 대상 소켓의 고유 식별자
  __u64 cookie = bpf_get_socket_cookie(ctx);

  // 대상 소켓을 쿠키 키 아래 저장
  struct Socket sock;
  __builtin_memset(&sock, 0, sizeof(sock));
  sock.dst_addr = dst_addr;
  sock.dst_port = dst_port;
  bpf_map_update_elem(&map_socks, &cookie, &sock, 0);

  // 연결을 프록시로 리디렉션
  ctx->user_ip4 = htonl(0x7f000001); // 127.0.0.1 == 프록시 IP
  ctx->user_port = htonl(conf->proxy_port << 16); // 프록시 포트

  bpf_printk("클라이언트 연결을 프록시로 리디렉션 중\n");

  return 1;
}

// 해당 cgroup(다시 전송 시간 초과, 연결 설정 등)에 소켓 작업이 발생할 때 호출되는 프로그램입니다.
// 이는 클라이언트 출처 주소와 포트를 기록하기 위한 것입니다.
SEC("sockops")
int cg_sock_ops(struct bpf_sock_ops *ctx) {
  // IPv4 연결만 전달
  if (ctx->family != AF_INET) return 0;

  // Active socket with an established connection
  if (ctx->op == BPF_SOCK_OPS_ACTIVE_ESTABLISHED_CB) {
    __u64 cookie = bpf_get_socket_cookie(ctx);

    // 해당 cookie에 대한 맵에서 소켓 조회
    // 소켓이 있는 경우 소스 포트와 소켓 매핑 저장
    struct Socket *sock = bpf_map_lookup_elem(&map_socks, &cookie);
    if (sock) {
      __u16 src_port = ctx->local_port;
      bpf_map_update_elem(&map_ports, &src_port, &cookie, 0);
    }
  }

  bpf_printk("sockops 후킹 성공\n");

  return 0;
}

// 이는 프록시가 getsockopt SO_ORIGINAL_DST를 통해 원래 대상 정보를 쿼리할 때 트리거됩니다.
// 이 프로그램은 클라이언트의 소스 포트를 map_ports에서 소켓의 쿠키를 검색하고,
// 그런 다음 map_socks에서 원래 대상 정보를 가져와 클라이언트 요청을 전달합니다.
SEC("cgroup/getsockopt")
int cg_sock_opt(struct bpf_sockopt *ctx) {
  // SO_ORIGINAL_DST 소켓 옵션은 네트워크 주소 변환(NAT) 및 투명 프록시를 주로 위한 특수화된 옵션입니다.
  // 전형적인 NAT 또는 투명 프록시 설정에서, 들어오는 패킷은 원래 대상에서 프록시 서버로 리디렉션됩니다.
  // 패킷을 수신한 프록시 서버는 종종 트래픽을 적절하게 처리하기 위해 원래 대상 주소를 알아야 합니다.
  // 이것이 SO_ORIGINAL_DST의 역할입니다.
  if (ctx->optname != SO_ORIGINAL_DST) return 1;
  // IPv4 TCP 연결만 전달
  if (ctx->sk->family != AF_INET) return 1;
  if (ctx->sk->protocol != IPPROTO_TCP) return 1;

  // 클라이언트의 소스 포트 가져오기
  // 실제로 sk->dst_port인데, SO_ORIGINAL_DST 소켓 옵션으로 getsockopt() 시스템 호출이
  // 클라이언트의 원래 대상 포트를 가져오기 때문에 클라이언트의 대상 포트를 "쿼리"합니다.
  __u16 src_port = ntohs(ctx->sk->dst_port);

  // 클라이언트 src_port를 사용하여 소켓 쿠키 검색
  __u64 *cookie = bpf_map_lookup_elem(&map_ports, &src_port);
  if (!cookie) return 1;

  // 쿠키(소켓 식별자)를 사용하여 map_socks에서 원래 소켓(대상으로 클라이언트 연결)을 가져옵니다.
  struct Socket *sock = bpf_map_lookup_elem(&map_socks, cookie);
  if (!sock) return 1;

  struct sockaddr_in *sa = ctx->optval;
  if ((void*)(sa + 1) > ctx->optval_end) return 1;

  // 원래 대상 대상과의 연결 설정
  ctx->optlen = sizeof(*sa);
  sa->sin_family = ctx->sk->family; // 주소 패밀리
  sa->sin_addr.s_addr = htonl(sock->dst_addr); // 대상 주소
  sa->sin_port = htons(sock->dst_port); // 대상 포트
  ctx->retval = 0;

  bpf_printk("원래 대상으로 연결 리디렉션 중\n");

  return 1;
}

char __LICENSE[] SEC("license") = "GPL";
```

자세한 내용은 아래 GitHub 저장소 링크에서 확인하세요:

# 성능 평가

<div class="content-ad"></div>

마무리하며, 저는 호스트 서버의 영향을 평가하기 위해 eBPF 프로그램이 트래픽을 가로챌 때 발생하는 지연 시간과 CPU 부하에 초점을 맞춘 기본 성능 테스트를 실시했습니다. 이 테스트는 1만 개의 요청을 기준으로 평균 지연 시간을 측정하는 것을 포함했습니다.

![이미지](/assets/img/2024-06-23-TransparentProxyImplementationusingeBPFandGo_1.png)

우리의 결과는 eBPF 프로그램이 평균적으로 약 1밀리초의 고정 eBPF 오버헤드를 추가한다는 것을 나타냅니다. 또한 각 후크에 의해 도입된 평균 CPU 부하는 다음과 같습니다: sockops의 경우 0.4%, cgroup/connect4의 경우 0.1%, cgroup/getsockopt의 경우 0.09%입니다. 요컨대, 사실상 아무것도 아닙니다.

이 연구 결과는 eBPF 프로그램에 의한 추가 지연 시간과 CPU 부하와 트래픽 가로채기의 이점 사이의 균형을 다루고 있습니다.

<div class="content-ad"></div>

# 결론

결론적으로, eBPF를 활용하여 투명 프록시를 구현하는 것은 네트워크 가로채기와 전달에 강력한 해결책을 제시합니다. eBPF의 높은 성능 패킷 처리 및 리눅스 커널 내에서 실시간 트래픽 조작과 같은 기능을 활용함으로써, 투명 프록시는 보안 강화, 트래픽 모니터링 및 최적화를 포함한 다양한 목적으로 네트워크 트래픽을 효율적으로 관리할 수 있습니다. 제공된 예제는 Go와 eBPF의 통합을 보여주며, 서로 다른 eBPF 프로그램이 원활하게 협력하여 투명 프록시 기능을 달성하는 방법을 보여줍니다. 이 접근 방식은 최소한의 오버헤드를 보장할 뿐만 아니라 TCP IPv4 연결을 넘어 추가 프로토콜을 지원하기 위해 프록시 기능을 확장하는 유연성을 제공합니다.