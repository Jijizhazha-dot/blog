---
title: CSS Grid 与容器查询：少一点媒体查询，多一点自适应
date: 2026-02-03
time: 15:56
tag: 前端
minutes: 9
---

布局方案的选择，本质上是「谁来适应谁」的问题。媒体查询让布局去适应屏幕宽度，但真正决定卡片排几列的，是卡片所在容器的宽度。

## 断点思维的问题

按视口宽度写断点，等于假设了「容器总是和视口一样宽」。一旦这些卡片被放进侧边栏或者两栏布局的右半边，断点就全错了。

用 `auto-fill` 加 `minmax` 可以让网格自己决定：

```css
.cards {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 20px;
}
```

一行媒体查询都不用写，容器多宽就排几列。

## 什么时候还需要容器查询

- 卡片在侧边栏里时，容器比视口窄得多
- 同一套组件在首页是两列、在详情页是一列

```css
@container (min-width: 420px) {
  .card { grid-template-columns: auto 1fr; }
}
```

> 提醒：容器查询要求父元素显式声明 `container-type`，否则规则不会生效。

顺序上我现在的习惯是：先让网格自适应，再补容器查询，最后才是媒体查询兜底。
