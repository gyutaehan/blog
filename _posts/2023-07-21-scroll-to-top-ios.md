---
layout: post
comments: true
title: 탑 페이지로 스크롤 올리는 법. iOS 넌 진짜(2)
date: 2023-07-21 13:15:00
categories: dev
---

### 방법1 

```javascript
window.scrollTo(0,0)
```

window의 scrollTo(0,0)를 주면 보통은 해결이 된다.

### **iOS에서 안 먹힐때**

```css
document.getElementById("root").scrollTo(0, 0);
```

방법1과 같이 하면 iOS에서 안될때가 있다(??)
될 때도 있고 안 될때도 있다는 말.
그럴 때 해결법은 위 코드 처럼 window를 사용하지않고 element를 지정해서 스크롤을 올리면 된다. 