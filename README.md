## 减低微信页面下拉露底（橡皮筋效果）的几率

当我们开发H5页面的时候，因为 webView 的原因，页面在滚动到页面顶部或底部的时候，容易触发全局的 “橡皮筋效果”。

当页面内部还有滚动的时候，这个效果就会影响到 H5 自己的一些交互效果了。但是因为这个是 native 的特征，所以没法在第三方平台给去掉（比如：微信）。

不过，我们可以通过前端的方式来降低这一效果被触发的几率。

<img src="http://o4a7cbihz.qnssl.com/cover/0bf594ed-2bee-4df3-90f9-8de68787b1ba" width = "300" alt="橡皮筋效果" align=center />


### 原理
当页面的滚动条不在顶部或者底部的时候，就不会触发 “橡皮筋效果”。那我们就尽量让页面的滚动条在滑动的时候不在起始或末了位置进行 move 即可。

### 源码

```javascript
var preventViewScroll = function (classes) {
  var prevent = function (ele) {
    ele.addEventListener('touchstart', function () {
      var o = ele.scrollTop
      var i = ele.scrollHeight
      var t = o + ele.offsetHeight
      if (o === 0) { // 当滚动条在顶部的时候，强制置为1
        ele.scrollTop = 1
      } else if (t === i) { // 当滚动条在底部的时候，强制置为 o - 1
        ele.scrollTop = o - 1
      }
    })
    
  // 如果滚动条不在顶部，且不在底部，则不作处理；否则，阻止 move.
    ele.addEventListener('touchmove', function (e) {
      ele.offsetHeight < ele.scrollHeight && (e._isScroller = !0)
    })
  }

  var dom = document.querySelector(classes)
  dom && prevent(dom)

  document.body.addEventListener('touchmove', function (e) {
    var target = e.target || e.currentTarget
    if (target.className.indexOf('prevent-view-scroll') !== -1) { // 对于 class 包含 prevent-view-scroll 的元素，不做处理
      return
    }
    e._isScroller || e.preventDefault()
  })
}

```

### demo
请在微信中打开： <http://lzwai.me/preventViewScroll/example.html>


