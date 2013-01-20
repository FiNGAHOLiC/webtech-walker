---
layout: posts
title: jQueryのイベントハンドラでreturn falseするとイベントのバブリングが止まる
tags:
  - JavaScript
  - jQuery
---

例えばjQueryでスムーズスクロール的なのを実装したとして、a要素のデフォルトの機能を止めるために以下のように`return false`ってするとイベントのバブリングまでとまるよという話。

{% highlight javascript %}
$(function() {
  $('a[href*=#]').click(function() {
    // スムーズスクロースの処理
    ...

    return false;
  });
});
{% endhighlight %}

つまり

{% highlight javascript %}
$(function() {
  $('a[href*=#]').click(function(ev) {
    ev.preventDefault();
    ev.stopPropagation();

    // スムーズスクロースの処理
    ...
  });
});
{% endhighlight %}

としたときと同じ挙動になる。`return false`してるコードは、デフォルトの挙動を止めるのは意図してるけど、イベントのバブリングまで止めるのは意図してない場合が多いんじゃなかろうかと思う。意図してるなら特に問題ない。

それでイベントのバブリングがとまると何が一番困るかというdelegateが使えなくなること。

具体的にはこれが動かない。

{% highlight javascript %}
$(function() {
  $('a[href*=#]').click(function() {
    // スムーズスクロースの処理
    ...

    return false;
  });

  $(document).delegate('a', 'click', function() {
    alert('clicked!!');
  });
});
{% endhighlight %}

delegateはイベントのバブリングを利用してイベントを捕まえるので最初のスムーズスクロールで`return false`しちゃうとそこでイベントのバブリングが止まるのでdelegateのイベントが実行されない。なのでイベントのデフォルトの動作を止めたいだけなら、できるだけ

{% highlight javascript %}
$(function() {
  $('a[href*=#]').click(function(ev) {
    ev.preventDefault();

    // スムーズスクロースの処理
    ...
  });
});
{% endhighlight %}

のように`preventDefault()`を使うようにしたほうがいいよねというお話でした。