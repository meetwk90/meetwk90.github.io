---
layout: post
title: "jQuery 实现菜单跟随鼠标滚动/静止"
date: 2016-03-02
excerpt: "废话不多说，上代码。"
tag：
- jQuery
- Code
comments: true
---

{% highlight javascript %}
jQuery(function($) {
​
      var $elm = $("#navbar");
      var startPos = $elm.offset().top; //获得菜单到浏览器顶部的距离

      $(window).scroll(function() {

        var p = $(window).scrollTop(); //追踪屏幕滚动距离浏览器顶部的距离
​
        if (p > startPos) {
          $elm.css({
            "position": "fixed",
            "top": 0,
            "width": "100%",
            "margin-left": "auto",
            "margin-right": "auto",
            "z-index": 5555
            });

        } else {
          $elm.css({
            "position": "relative"
          });
        }
      });
});
{% endhighlight %}
