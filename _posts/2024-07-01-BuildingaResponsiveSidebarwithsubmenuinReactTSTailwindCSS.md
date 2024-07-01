---
title: "ReactTS와 Tailwind CSS로 서브 메뉴가 있는 반응형 사이드바 만들기"
description: ""
coverImage: "/assets/img/2024-07-01-BuildingaResponsiveSidebarwithsubmenuinReactTSTailwindCSS_0.png"
date: 2024-07-01 16:27
ogImage: 
  url: /assets/img/2024-07-01-BuildingaResponsiveSidebarwithsubmenuinReactTSTailwindCSS_0.png
tag: Tech
originalTitle: "Building a Responsive Sidebar with sub menu in ReactTS , Tailwind CSS"
link: "https://medium.com/@muhammadbinkashif7/building-a-responsive-sidebar-with-typescript-reactjs-and-tailwind-css-084b647ced9d"
---


<img src="/assets/img/2024-07-01-BuildingaResponsiveSidebarwithsubmenuinReactTSTailwindCSS_0.png" />

소개:

현재의 웹 개발 환경에서는 사용자 친화적인 인터페이스를 만드는 것이 매우 중요합니다. 직관적인 사용자 경험을 제공하기 위해 많은 웹 애플리케이션에서 발견되는 일반적인 UI 요소 중 하나가 사이드바입니다. 이 튜토리얼에서는 TypeScript, ReactJS, 그리고 Tailwind CSS를 사용하여 반응형 사이드바를 디자인하는 방법을 살펴보겠습니다. 우리는 코드를 단계별로 분해하고, 각 구성 요소가 사이드바의 기능에 어떻게 기여하는지 설명할 것입니다.

의존성:

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

- 히어로아이콘을 사용 중이지만 원하는 아이콘 라이브러리를 사용할 수 있어요.
- 이미 ReactTS, Tailwind 프로젝트 설정이 되어 있다고 가정할게요.

Tailwind 개념:

코드를 살펴보기 전에 Tailwind에 대해 몇 가지 개념을 알아두는 것이 중요해요 (그리고 제가 마주한 몇 가지 함정을 피할 수 있을 거에요 😅).

- Tailwind는 완전한, 중간에 끊어지지 않은 문자열로 발생하는 클래스 네임만을 컴파일해요. 이에 대해 더 읽어보고 싶다면 https://tailwindcss.com/docs/content-configuration#dynamic-class-names 여기를 확인해 보세요.
- 즉, 우리가 실행 중에 결정되는 조건부 변수를 전달하려고 한다면
```div class=”text-'' error ? ‘red’ : ‘green’ ''-600"```/div
원하는대로 렌더링되지 않아요.

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

Tailwind 및 CSS 클래스 사용:

- 화면에 고정된 inset-0을 사용하여 항상 보이도록 설정하고 화면에 고정시킵니다.
- transition-all 유틸리티 클래스는 모든 CSS 속성에 전환 또는 애니메이션 효과를 적용하는 데 사용됩니다.
- transition-all을 적용한 후에 우리가 애니메이션을 적용하고자 하는 속성을 제공합니다. 이 경우에는 w-32에서 w-0까지이며, 이는 너비에 대한 모든 변경 사항이 일정 기간 동안 부드럽게 이루어집니다.
- -z-10은 음수 10 z-인덱스를 부여하는 데 사용됩니다.
- sm: md: lg:는 화면 분기점으로, css에서 @media (최소 너비: [어떤 너비])의 동등한 것입니다. 분기점이 적용된 모든 스타일은 모든 화면 크기에 적용된다는 점을 기억하는 것이 중요합니다 (예: sm: w-30은 md 및 lg에도 적용됩니다).
- w-5/6은 너비가 83.33%가됨을 의미하며, 이는 모바일 뷰에서만 표시됩니다.
- 하위 메뉴 항목에 대한 높이 애니메이션을 위해 사용자 정의 css 전환: 높이 300ms;

이제 코드로 이동합시다:

사이드바 컴포넌트:

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

import {
  ArrowRightIcon,
  ArrowLeftIcon,
  HomeIcon,
  CogIcon,
  UserIcon,
  EllipsisVerticalIcon,
} from '@heroicons/react/24/outline';
import { useState } from 'react';
import SidebarItem from './SidebarItem';

// 이 사이드바 컴포넌트는 모바일과 데스크톱 모두를 위한 것입니다.
function Sidebar({ children, expanded, setExpanded }: any) {
  return (
    <div className="relative">
      {/* 
        이 div는 사이드바가 확장될 때 배경 오버레이를 만드는 데 사용됩니다.
        이는 모바일 화면에서만 표시됩니다.
      */}
      <div
        className={`fixed inset-0 -z-10 block bg-gray-400  ${expanded ? 'block sm:hidden' : 'hidden'}`}
      />
      <aside
        className={`box-border h-screen transition-all ${expanded ? 'w-5/6 sm:w-64' : 'w-0 sm:w-20'}`}
      >
        <nav className="flex h-full flex-col border-r bg-white shadow-sm">
          <div className="flex items-center justify-between p-4 pb-2">
            <img
              src="https://img.logoipsum.com/243.svg"
              className={`overflow-hidden transition-all ${
                expanded ? 'w-32' : 'w-0'
              }`}
              alt=""
            />
            <div className={`${expanded ? '' : 'hidden sm:block'}`}>
              <button
                onClick={() => setExpanded((curr: boolean) => !curr)}
                className="rounded-lg bg-gray-50 p-1.5 hover:bg-gray-100"
              >
                {expanded ? (
                  <ArrowRightIcon className="h-6 w-6" />
                ) : (
                  <ArrowLeftIcon className="h-6 w-6" />
                )}
              </button>
            </div>
          </div>
          <ul className="flex-1 px-3">{children}</ul>
          <div className="flex border-t p-3">
            <img
              src="https://ui-avatars.com/api/?background=c7d2fe&color=3730a3&bold=true&name=Mark+Ruffalo"
              alt=""
              className="h-10 w-10 rounded-md"
            />
            <div
              className={`
              flex items-center justify-between
              overflow-hidden transition-all ${expanded ? 'ml-3 w-52' : 'w-0'}
          `}
            >
              <div className="leading-4">
                <h4 className="font-semibold">Mark Ruffalo</h4>
                <span className="text-xs text-gray-600">mark@gmail.com</span>
              </div>
              <EllipsisVerticalIcon className="h-6 w-6" />
            </div>
          </div>
        </nav>
      </aside>
    </div>
  );
}

export default function MakeSidebar() {
  const [expanded, setExpanded] = useState(true);
  const navBarItems = [
    {
      icon: <HomeIcon />,
      text: 'Home',
      active: true,
    },
    {
      icon: <UserIcon />,
      subMenu: [
        {
          icon: <UserIcon />,
          text: 'Profile',
        },
        {
          icon: <CogIcon />,
          text: 'Settings',
        },
      ],
      text: 'Profile',
    },
    {
      icon: <CogIcon />,
      text: 'Settings',
    },
  ];

  // 데스크톱 사이드바
  return (
    <Sidebar expanded={expanded} setExpanded={setExpanded}>
      {navBarItems.map((item, index) => (
        <SidebarItem key={index} expanded={expanded} {...item} />
      ))}
    </Sidebar>
  );
}

이제 코드를 각각의 부분으로 나누어보겠습니다. MakeSidebar 래퍼 함수는 확장 여부를 제어하는 expanded: boolean 상태를 정의하는 동시에 사이드바를 생성합니다. 여기서는 간단한 React 상태를 사용했지만 애플리케이션에 따라 프롭 드릴링을 피하기 위해 Redux/전역 상태를 사용할 수도 있습니다.

위의 예시에서 설명했듯이 메뉴 항목 텍스트와 헤더 아이콘의 애니메이션을 transition-all 클래스로 제어하고 있습니다.

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

모바일 화면에서만 표시되는 오버레이인 이 div는 모바일 화면에서 메뉴가 열렸을 때 볼 수 있는 배경을 어둡게 바꾸는 효과를 제공합니다.

저희 `aside` 요소에 `w-5/6`을 적용했음을 주목해주세요. 이것이 모바일 화면을 제어하는 부분입니다.

사이드바 아이템 컴포넌트:

import { ChevronRightIcon } from '@heroicons/react/24/outline';
import { useEffect, useState } from 'react';

interface SidebarItemProps {
  active?: boolean;
  icon: React.ReactNode;
  text: string;
  expanded: boolean;
  subMenu?: SubMenuItemProps[] | null;
}

// 서브메뉴 항목들은 추가적인 서브메뉴 항목을 가질 수 없으므로 확장될 수 없다고 가정합니다.
interface SubMenuItemProps extends Omit<SidebarItemProps, 'expanded'> {
  expanded?: never;
  subMenu?: never;
}

// 이 컴포넌트는 마우스를 갖다대었을 때 서브메뉴 항목들을 렌더링하는 데 사용됩니다.
function HoveredSubMenuItem({ icon, text, active }: SubMenuItemProps) {
  return (
    <div
      className={`my-2 rounded-md p-2 ${
        active ? 'bg-gray-300' : ' hover:bg-indigo-50'
      }`}
    >
      <div className="flex items-center justify-center ">
        <span className="text-primary-500 h-6 w-6 ">{icon}</span>
        <span className="text-primary-500 ml-3 w-28 text-start">{text}</span>
        <div className="bg-primary-200 h-1" />
      </div>
    </div>
  );
}

export default function SidebarItem({
  icon,
  active = false,
  text,
  expanded = false,
  subMenu = null,
}: SidebarItemProps) {
  const [expandSubMenu, setExpandSubMenu] = useState(false);

  useEffect(() => {
    if (!expanded) {
      setExpandSubMenu(false);
    }
  }, [expanded]);

  // 각 항목이 40px 높이라고 가정하고 서브메뉴의 높이를 계산합니다.
  const subMenuHeight = expandSubMenu
    ? `${((subMenu?.length || 0) * 40 + (subMenu! && 15)).toString()}px`
    : 0;

  return (
    <>
      <li>
        <button
          className={`
         group relative my-1 flex w-full cursor-pointer
         items-center rounded-md px-3
         py-2 font-medium transition-colors
         ${
           active && !subMenu
             ? 'text-primary-500 bg-gradient-to-tr from-indigo-200 to-indigo-100'
             : 'text-gray-600 hover:bg-indigo-50'
         }
         ${!expanded && 'hidden sm:flex'}
     `}
          onClick={() => setExpandSubMenu((curr) => expanded && !curr)}
        >
          <span className="h-6 w-6">{icon}</span>

          <span
            className={`overflow-hidden text-start transition-all ${
              expanded ? 'ml-3 w-44' : 'w-0'
            }`}
          >
            {text}
          </span>
          {subMenu && (
            <div
              className={`absolute right-2 h-4 w-4${expanded ? '' : 'top-2'} transition-all ${expandSubMenu ? 'rotate-90' : 'rotate-0'}`}
            >
              <ChevronRightIcon />
            </div>
          )}

          {/* 
            마우스를 갖다댔을 때 항목 텍스트 또는 서브메뉴 항목들을 표시합니다.
          */}
          {!expanded && (
            <div
              className={`
            text-primary-500 invisible absolute left-full ml-6 -translate-x-3
            rounded-md bg-indigo-100 px-2
            py-1 text-sm opacity-20 transition-all
            group-hover:visible group-hover:translate-x-0 group-hover:opacity-100
        `}
            >
              {/* 
                만약 갖다댔을 때 항목에 서브메뉴가 없다면 텍스트를 표시하고
                그렇지 않다면 서브메뉴 항목들을 표시합니다.
              */}
              {!subMenu
                ? text
                : subMenu.map((item, index) => (
                    <HoveredSubMenuItem
                      key={index}
                      text={item.text}
                      icon={item.icon}
                    />
                  ))}
            </div>
          )}
        </button>
      </li>
      <ul
        className="sub-menu pl-6"
        style={{ height: subMenuHeight }}
      >
        {/* 
          만약 항목이 서브메뉴를 가지고 있다면 서브메뉴 항목들을 렌더링합니다.
          서브메뉴 항목들은 SidebarItem 컴포넌트로 렌더링됩니다.
        */}
        {expanded &&
          subMenu?.map((item, index) => (
            <SidebarItem key={index} {...item} expanded={expanded} />
          ))}
      </ul>
    </>
  );
}

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

여기에는 Tailwind에서 작동하지 않는 높이 속성에 대한 애니메이션을 적용하기 위해 사용되는 sub-menu이라는 사용자 정의 css 클래스가 포함되어 있습니다. 따라서 다음과 같이 index.css 파일에서 사용자 정의 css 클래스를 정의하세요.

.sub-menu {
  overflow: hidden;
  transition: height 300ms;
}

결론:

이 튜토리얼에서는 TypeScript, ReactTS 및 Tailwind CSS를 사용하여 반응형 사이드바를 디자인하는 방법을 살펴보았습니다. Sidebar 및 SidebarItem 구성 요소의 코드를 분해하여 각 부분이 기능적인 사이드바 인터페이스를 만드는 데 어떤 역할을 하는지 설명했습니다. 이러한 단계를 따르고 기본 개념을 이해하면 다양한 화면 크기에 적응하는 직관적인 탐색 요소로 웹 애플리케이션을 향상시킬 수 있습니다. 즐거운 코딩하세요!