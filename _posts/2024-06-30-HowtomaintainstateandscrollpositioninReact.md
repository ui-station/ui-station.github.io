---
title: "React에서 상태 및 스크롤 위치를 유지하는 방법"
description: ""
coverImage: "/assets/img/2024-06-30-HowtomaintainstateandscrollpositioninReact_0.png"
date: 2024-06-30 22:40
ogImage: 
  url: /assets/img/2024-06-30-HowtomaintainstateandscrollpositioninReact_0.png
tag: Tech
originalTitle: "How to maintain state and scroll position in React"
link: "https://medium.com/@lingash/how-to-maintain-state-and-scroll-position-in-react-4baa5dea0ce"
---


가끔 뉴스 목록에서 뉴스 세부 페이지로 전환한 후 다시 돌아오면 상태와 스크롤 위치가 손실될 수 있습니다. 이를 방지하기 위해 페이지 간 이동 시 react-router-dom을 사용하여 값을 전달할 수 있습니다.

따라서 세부 페이지로 이동할 때 다음과 같이 상태 및 스크롤 위치를 전달하세요.

```js
navigate('/userDetails', {state: {userLists: data, scrollPosition, userInfo: info}})
```

`div` 요소에서 스크롤 이벤트를 캡처하고 값을 업데이트하는 방법:

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

```js
 function handleScroll(event) {
    setScrollPosition(event.target.scrollTop)
 }

<div style={{overflowY: "scroll",height: '100vh'}} ref={scrollRef} onScroll={handleScroll}>
```

유저가 뒤로 돌아갈 때, 상태를 다시 뉴스 페이지로 변경하세요:

```js
navigate('/news', { state: location.state })
```

뉴스 컴포넌트에서 아래와 같이 데이터에 접근하고 업데이트하세요.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

```js
useEffect(()=> {
   if(location.state){
      const {userLists,scrollPosition} = location.state;  
      setData(userLists)
      scrollRef.current.style.backgroundColor = 'red'
      setTimeout(() => {
        scrollRef.current.scrollTop = scrollPosition
      }, (10));
   }else{
      getDataFromApi();
   }
},[])
```

기억하세요, setTimeout 내부의 코드는 잠시 지난 후에 실행되며 스크롤 위치를 조정하기 전에 DOM이 안정화됩니다.

그리고 다음은 이 기능을 적용하는 방법의 예시입니다.

```js
import './App.css';
import { useEffect, useRef, useState } from 'react';
import axios from 'axios';
import { BrowserRouter, Route, Routes, useLocation, useNavigate } from 'react-router-dom';

function App() {

  return (
    <BrowserRouter>
     <Routes>
       <Route path="/news" element={<UserLists />}/>
       <Route path="/userDetails" element={<UserDetails />}/>
     </Routes>
    </BrowserRouter>
  );
}

const UserLists = () => {
  const [data,setData] = useState([]);
  const [scrollPosition,setScrollPosition] = useState();
  const navigate = useNavigate();
  const location = useLocation();
  const scrollRef = useRef();

  useEffect(()=> {
    if(location.state){
      const {userLists,scrollPosition} = location.state;  
      setData(userLists)
      scrollRef.current.style.backgroundColor = 'red'
      setTimeout(() => {
        scrollRef.current.scrollTop = scrollPosition
      }, (10));
    }else{
      getDataFromApi();
    }
  },[])


  const getDataFromApi = async () => {
    try{
      const res = await axios.get('https://reqres.in/api/users');
      setData(res.data.data);
    }catch(err){
      console.log("error",err)
    }     
  }

  const handleScroll = (event) => {
    setScrollPosition(event.target.scrollTop)
  }

  return (
    <div className="App">
      <div style={{overflowY: "scroll",height: '100vh'}} ref={scrollRef} onScroll={handleScroll}>
        {data.map((info) => {
          return (
            <div
              style={{backgroundColor: "orange"}}
              onClick={() => {
                navigate('/userDetails',{state: {userLists: data,scrollPosition,userInfo: info}})
              }
            >
              <h2>{info.email}</h2>
              <h2>{info.first_name}</h2>
              <h2>{info.last_name}</h2>
              <img src={info.avatar} />
            </div>
          )
        })}
      </div>
    </div>
  )
}

const UserDetails = () => {
  const [userInfo,setUserInfo] = useState();
  const navigate = useNavigate();
  const location = useLocation();

  useEffect(()=> {
    if(location.state){
      const {userInfo} = location.state;
      setUserInfo(userInfo)
    }
  })

  return (
    <div>
      <button
        onClick={() => {
          navigate('/news',{state: location.state})
        }
      >
        Go back to user lists
      </button>
      <div
        style={{backgroundColor: "skyblue"}}
      >
        <h2>{userInfo?.email}</h2>
        <h2>{userInfo?.first_name}</h2>
        <h2>{userInfo?.last_name}</h2>
        <img src={userInfo?.avatar} />
      </div>
    </div>
  )
}

export default App;
```

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

그게 전부에요...

위의 방법은 사용자가 브라우저의 뒤로 가기 버튼을 누를 때 작동하지 않습니다. 이러한 경우에 작동하려면 로컬 스토리지에 상태와 스크롤 위치를 저장하고, 첫 렌더링 중에 useEffect에서 사용해야 합니다.

질문이 있으시면 응답 섹션에서 물어봐 주세요.