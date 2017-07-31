---
layout: post
title: Some snippet of css3 rotation
description: Code snippet of 2d and 3d transform effect.
---
**1. A turntable with square content box**
In this section we will build a turntable with squares in each sector unit using SCSS.  
There are many similar examples such as [rotating-menu](http://www.inserthtml.com/2012/09/rotating-menu/) and [Pie-Menu](http://www.jqueryscript.net/demo/Material-Design-Styled-Pie-Menu-with-jQuery-CSS3/#). We will focus on discussing a more general idea of creating such turntables.   

i. Styling a sector area  
Our turntable consists of sectors with the same size. Suppose you have a div as turntable container and it's children as sectors in it. To get one sector with an arbitrary angel less than 180deg we use css3 transform skew property, which is a general way shown in other posts. Some may use shew with a given angel on one asix only. For convenience we apply skew on both axis with half the angel. I prefer to use the right-bottom as transform origin. So if we position the right-bottom to the center of the container and apply border-radius & overflow to the container, we will get one sector. And after applying rotate on each sector with increment angel, a turntable is very close.   

<div markdown="0">
    <div class="lottery-sector">
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
    </div>
</div>

ii. Square content area in each sector  
If float content sizing & positioning is important, we need some precise controls.  
Imagine there is a sector with arbitrary angel less than 180deg. Now you have images with different size and need to put them all in to this turntable with approximative size, and as large as possiable. Thus finding out the largest inscribed rectangle parallel with the symmetry axis of the sector seems to be a good solution.  
We can draw this geometric approach intuitivly on a white paper. Moreover, we can strictly prove the largest inscribed rectangle is a square. In the dom structure, those contents are children of each sector. Because the content has been skewed after the sector construction, we need to skew it back to make it display properly. But things becomes more complex because the skew transform is not scale-invariant. So in general, we need to calculate the inscribed square of the sector, and deal with the scale variety of affine transformation at the same time.

(Detailed mathematical derivation to be continued...)

<div markdown="0">
    <div class="lottery-sector">
        <div><span>1</span></div>
        <div><span>2</span></div>
        <div><span>3</span></div>
        <div><span>4</span></div>
        <div><span>5</span></div>
        <div><span>6</span></div>
        <div><span>7</span></div>
    </div>
</div>


