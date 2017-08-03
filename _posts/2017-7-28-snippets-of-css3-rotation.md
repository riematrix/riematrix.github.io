---
layout: post
title: Some snippet of css3 rotation
description: Code snippet of 2d and 3d transform effect.
---
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    extensions: ["tex2jax.js"],
    jax: ["input/TeX","output/HTML-CSS"],
    tex2jax: {inlineMath: [["$","$"],["\\(","\\)"]]}
  });
</script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js">
</script>

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

<div markdown="0">
        <div class="sector-container">
            <div class="lottery-sector full f1">
                <div class="c1"><span style="outline: 1px solid #000"></span></div>
                <p class="pc">C</p>
                <p class="pd">D</p>
                <p class="po">O</p>
                <p class="pa">A</p>
                <p class="pb">B</p>
                <p class="pe">E</p>
                <p class="theta">\begin{align}\theta\end{align}</p>
                <p class="alpha">\begin{align}\alpha\end{align}</p>
            </div>
        </div>
        <p>Suppose we need to divide one circle into \(n\) sectors with the same angle.
            Let \(\begin{align}\theta\end{align}\) be the half of that angle.
            We can get the skew angle on both x and y axis. Let \(\begin{align}\alpha\end{align}\)
            be the skew angle, we have:
            \begin{align}\theta + \alpha = \frac\pi{4}\end{align}
        </p>
        <p>
            In order to position the content square in each sector, we need to calculate all related
            parameters after transform, and make reverse derivation to retrieve the css rules before
            transform.
            <br>
            Set \(C\) as the center of the circle, \(O\) as the center of the square, ad \(D\)  as
            the intersection point of segment \(CD\) and the square edge \(AB\).
            Set the edge length of the content square as \(s\), the length of \(CD\) as \(d\), and
            the radius of the container circle as \(r\), we have the relation between \(s\), \(d\)
            and already known variables \(r\) and \(\begin{align}\theta\end{align}\):
            \begin{align}
            tan(\theta) = \frac{\frac{s}{2}}{d} \\
            (s+d)^2 + (\frac{s}{2})^2 = r^2
            \end{align}
            Solving this system we have the value of \(s\) and \(d\):
            \begin{align}
            d & = \frac{r}{\sqrt{(2 tan(\theta) + 1)^2 + tan^2(\theta)}} \\\\
            & = \frac{r}{\sqrt{5 tan^2(\theta) + 4 tan(\theta) + 1}} \\
            \\
            s & = 2 tan(\theta) d \\\\
            & = \frac{2 tan(\theta) r}{\sqrt{5 tan^2(\theta) + 4 tan(\theta) + 1}} \\
            \end{align}
        </p>
        <div class="sector-container">
            <div class="lottery-sector full f1">
                <div class="c2"><span style="outline: 1px solid #000"></span></div>
            </div>
        </div>
        <div class="sector-container">
            <div class="lottery-sector full f1">
                <div class="c3"><span style="outline: 1px solid #000"></span></div>
            </div>
        </div>
        <div class="sector-container">
            <div class="lottery-sector full f1">
                <div class="c4"><span style="outline: 1px solid #000"></span></div>
            </div>
        </div>
</div>

(To be continued...)

iii. Result   

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


