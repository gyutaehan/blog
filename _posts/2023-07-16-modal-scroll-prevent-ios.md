---
layout: post
comments: true
title: Modal 스크롤 막는 방법, iOS 넌 진짜
description: 
- ios에서 modal이 열여있을때 스크롤을 막는방법. 
date: 2023-07-16 16:39:00
categories: dev
author: Gyutae Han
---

```javascript
<div class="modal">
  <div class="modal-content">
    <h1>내용</h1>
  </div>
</div>
```

### **아주 간단한 방법1 하지만...**

```css
body{
  overflow:hidden;
}
```

body에다 **overflow:hidden**을 주면 된다.

**단,  iOS는 적용되지않는다**



### **iOS도 해결되지만, 완전하지 않은 방법2**

```css
body {
  position: fixed;
}
```

방법1과 같이 body에다 **position: fixed**를 준다. 

이 방법은 iOS도 문제없이 동작한다. 하지만, position: fixed를 주면 스크롤값이 초기화가 되어 버리기 때문에,

**스크롤이 맨 위로 올라가버린다.** 



### **1과 2를 개선한 방법3**

```javascript
let scrollPosition;
const userAgent = window.navigator.userAgent.toLowerCase();
const isIos = userAgent.indexOf('iphone') > -1 || userAgent.indexOf('ipad') > -1 && 'ontouchend' in document;
const body = document.querySelector('body');

function bodyFixedOn() {
    if(isIos){
        scrollPosition = window.pageYOffset;
        body.style.position = 'fixed';
        body.style.top = `-${scrollPosition}px`;
    }else {
        body.style.overflow = 'hidden';
    }
}

function bodyFixedOff() {
    if(isIos){
        body.style.removeProperty('position');
        body.style.removeProperty('top');
        window.scrollTo(0, scrollPosition);
    }else {
        body.style.removeProperty('overflow');
    }
}
```

iOS가 아니라면, **overflow:hidden**, iOS라면 **position: fixed**를 준다. 단 **position: fixed**의 경우, 앞서 말했듯이, 스크롤 자체가 없어지기 때문에, 당시 페이지를 보여주기 위해서 top에다 현재 페이지의 위치만큼 top에다 빼준다 . 그리고 모달을 닫았을때는 fixed를 해제하여, 다시 스크롤을 살리고, top을 0으로 초기화를 한다음, 저장해두었던 위치값만큼, **window.scrollTo**에 값을 지정해주면 된다.



### **번외 방법4(iOS16이상만 가능)**

참고) https://developer.mozilla.org/ja/docs/Web/CSS/overscroll-behavior

iOS16이상의 디바이스라면 **overscroll-behavior**가 사용가능 하다고 한다.

하지만, 지금 현재는 iOS16미만의 디바이스도 많이 사용하고 있을 거라고 생각하기 때문에 아직은 이르지만, 나중엔 이 방법이 편해보인다.

