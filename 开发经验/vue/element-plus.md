# element-plus 相关问题

## dialogue 抖动

将页面整体 overflow 设置为 hidden，在需要滚动的地方设置内部滚动

``` css
html,body{
  width: 100% !important;
  height: 100%;
  overflow: hidden;
  margin: 0;
}
#app{
  width: 100%;
  height: 100%;
  overflow-y: auto;
  max-width: none !important;
}
```

``` CSS
container {
    height: 200px;
    overflow: scroll;
}
```

