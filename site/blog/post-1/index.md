---
date: 2020-02-21
meta_description: 最近在看一些網頁作品時，發現有許多網站都使用了block reveal當作圖文的進場特效，感覺就像魔術師手一揮把消失的東西變出來一樣，效果相當不錯。這篇文章我會教大家如和做出這種有趣的效果，並且使用滑鼠滾輪觸發特效。
title: 一起來實作block reveal滾輪觸發進場特效
sub_heading: 最近在看一些網頁作品時，發現有許多網站都使用了block reveal當作圖文的進場特效，感覺就像魔術師手一揮把消失的東西變出來一樣，效果相當不錯。這篇文章我會教大家如和做出這種有趣的效果，並且使用滑鼠滾輪觸發特效。
post_image: /images/posts/1/post-1.gif
image_alt: block reveal圖文的進場特效
tags:
    - blog
    - frontend
---

要達成block reveal的效果，我們需要準備3樣東西:

* 文字內容`.reveal-item`
* 用於遮蔽文字的方塊`.reveal-block`
* 與將他們包在一起的容器`.reveal-animate`

首先建立用於遮蔽文字的方塊`.reveal-block`，並且使用一個容器`.reveal-animate`將文字和`.reveal-block`包起來。完成後的程式碼如下:

```html
<div class="reveal-animate">
    <div class="reveal-block"></div>
    <h1 class="reveal-item">測試文字</h1>
</div>
```

再來處理樣式的部分，我們先對容器`.reveal-animate`做一些調整:

* 將定位設為relative，方便容器內的元素可以相對於自己定位
* 使用inline-block顯示，否則預設是block會讓整個物件占滿整個版面顯得過長
* 隱藏超過容器範圍的東西

`.reveal-block`使用絕對定位，才不會影響到文字的排版。並將長寬設成和容器一樣的大小，最後再給它一個你自己喜歡的顏色。此外，記得將`z-index`設成1，文字才會被壓在下面。然後我們把`.reveal-block`的位置放在左側，作為動畫的初始位置。

```css
.reveal-animate{
    display: inline-block;
    position: relative;
    overflow: hidden;
}
.reveal-block {
    position: absolute;
    top: 0;
    left: -100%;
    z-index: 1;
    width: 100%;
    height: 101%;
    background-color: #0082ff;
}
```

處理好基本的要素之後，我們就能開始製作動畫了。整個block reveal的動畫原理如下:

一開始先將`.reveal-block`放在左邊，隱藏文字。接著讓`.reveal-block`滑入短暫停留，利用這短暫的時間我們可以將文字變出來。最後再讓`.reveal-block`滑出，將遮蔽的文字顯現出來就完成了一次動畫。

| ![animation](/images/posts/1/post-1-1.png) |
|:--:|
| block-reveal動畫原理 |

我們不難發現整個block reveal動畫是由兩個子動畫組成的，一個是遮蔽方塊`.reveal-block`的位移動畫，另一個是文字的顯現動畫。因此這次我們採用css製作2個keyframes動畫。

一個是套用在`.reveal-block`上的動畫`blockAnimation`，它會在0~40%時從左側移到中間，並在40%~60%做短暫停留，最後在60%~100%移到右側。另一個則是套用在文字上面的動畫`itemAnimation`，它在0~40%時都是透明的，40%~60%時才會顯示出來。

```css
@keyframes blockAnimation {
    0% { left: -100%; }
    40% { left: 0; }
    60% { left: 0; }
    100% { left: 100%; }
}
@keyframes itemAnimation {
    0% { opacity: 0; }
    40% { opacity: 0; }
    60% { opacity: 1; }
}
```

接著幫元素加上動畫我們就能看到目前的成果

```css
.reveal-block {
    animation: blockAnimation 1s ease;
}
.item{
    animation: itemAnimation 1s ease;
}
```

實際的情況下，這樣的動畫通常會套用在多個網頁元素上面。如果每次要使用時，都要在該元素內部加上`.reveal-block`想必是有點麻煩，此時我們可以使用`:before`來幫助我們。

操作非常簡單，只要將html裡的`.reveal-block`刪掉，將css裡的`.reveal-block`改為`.reveal-animate:before`，最後記得加上content(瀏覽器才會將偽元素顯示出來)，這樣就完成了

```html
<div class="reveal-animate">
  <h1 class="reveal-item">測試文字</h1>
</div>
```

```css
/*將.reveal-block改為.reveal-animate:before*/
.reveal-animate:before {
    content: "";
    display: block;
    position: absolute;
    top: 0;
    left: -100%;
    z-index: 1;
    width: 100%;
    height: 101%;
    background-color: #0082ff;
}
.reveal-animate:before {
    animation: blockAnimation 1s ease;
}
```

最後再教大家如何使用滑鼠滾輪觸發動畫。可是現在只有一個網頁元素好像有點無聊，我們先多加一些圖片和文字吧!

```html
<div class="reveal-animate">
  <h1 class="reveal-item">測試文字</h1>
</div>

<div class="reveal-animate"> 
  <img class="reveal-item" src="https://images.unsplash.com/photo-1581827114934-aa471ac2fec5?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1950&q=80" alt="">
</div>

<div class="reveal-animate">
  <p class="reveal-item">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
</div>

<div class="reveal-animate">
  <p class="reveal-item">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
</div>

<div class="reveal-animate">
  <h1 class="reveal-item">測試文字</h1>
</div>

<div class="reveal-animate"> 
  <img class="reveal-item" src="https://images.unsplash.com/photo-1581827114934-aa471ac2fec5?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1950&q=80" alt="">
</div>

<div class="reveal-animate">
  <p class="reveal-item">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
</div>

<div class="reveal-animate">
  <p class="reveal-item">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
</div>
```

觸發的機制是當頁面滾到元素上方400px處時，會幫`.reveal-animate`加上show的class，因此而觸發動畫。

![當視窗上方距離元素400px時觸發動畫](/images/posts/1/post-1-2.png)

程式碼如下:

```javascript
$(function(){
    var items = $(".reveal-animate").toArray()
    console.log(items)
    $(window).scroll(function(){
            items.forEach(function(item){
                if($(this).scrollTop() == $(item).offset().top - 400){
                $(item).addClass("show")
            }
        })
    })
    items.forEach(function(item){
        if($(this).scrollTop() == $(item).offset().top - 400){
            $(item).addClass("show")
        }
    })
})
```

最後再調整一下樣式，並且把動畫移到`.show`裡。讓`.reveal-animate`在出現`.show`後才會觸發動畫

```css
.reveal-item{
    opacity: 0;
    max-width: 100%;
}
.show:before {
    animation: blockAnimation 1s ease;
}
.show .reveal-item{
    opacity: 1;
    animation: itemAnimation 1s ease;
}
```

最後的練習成果:

<p class="codepen" data-height="500" data-default-tab="html,result" data-slug-hash="rNVMmVp" data-user="phatros" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/phatros/pen/rNVMmVp">
  Untitled</a> by CChuan (<a href="https://codepen.io/phatros">@phatros</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>