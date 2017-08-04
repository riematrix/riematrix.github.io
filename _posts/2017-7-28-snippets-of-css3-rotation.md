---
layout: post
title: An adaptive css3 turntable with square content box
description: Code snippet of 2d and 3d transform effect that build a css3 turntable.
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

In this article we will build a turntable with squares in each sector unit using SCSS.
There are many fantastic examples such as 
[rotating-menu](http://www.inserthtml.com/2012/09/rotating-menu/) 
and 
[Pie-Menu](http://www.jqueryscript.net/demo/Material-Design-Styled-Pie-Menu-with-jQuery-CSS3/#). 
We will focus on creating such turntables step by step.   

**1. A turntable with arbitrary number of sectors**   

i. Building a circle   

Consider the turntable's outer container as a circle. You can create the circle with div/css3.
Either fixed or float sizing is OK for the container. 
In this post I use float sizing method to make the style more adaptive.
Below is an example scss snippet of basic rules that 
styling such a container with class name 'lottery-sector'.

    <div class="lottery-sector"></div>

	  $container-scale: 60%;
	.lottery-sector {
	  width: $container-scale;
	  padding-top: $container-scale;
	  position: relative;
	  overflow: hidden;
	  border-radius: 50%;
	  }

The variable $container-scale can be adjusted to any size in any units if you need.
You may also apply vw/vh rules to both width and height to build this circle.

ii. Styling a single sector area   

The turntable has arbitrary number of child sectors that uniform the circle. 
To get a single sector with one arbitrary arc angel less than 180deg we use css3 transform 
skew property, which is a general approach shown in above posts.   

Some may use shew with a given angel on one axis only. 
We apply skew on both x and y axis with half the skew angel 
so that the result will be a diamond rather than a parallelogram.
The convenience of this strategy will be discussed in later sections.
 
<p markdown="0">Suppose we need to divide one circle into \(n\) sectors with the same arc angle.
        Let \(\begin{align}\theta\end{align}\) be half of the arc angle.
        We can get the skew angle on both x and y axis. Let \(\begin{align}\alpha\end{align}\)
        be the that skew angle, we have:
        \begin{align}\theta + \alpha = \frac\pi{4}\end{align}
</p>

iii. Positioning of sectors in circle   

 In order to position a sector easily in one circle, 
 we choose one of the sector's corner as transform origin.
 In this post, for example, we use the right-bottom corner
 and make it coincide with the circle center.
 
    .lottery-sector >div {
        right: 50%;
        bottom: 50%;
        width: 50%;
        height: 50%;
        position: absolute;
        transform-origin: 100% 100%;
        }

iv. Fill sectors into circle      
 
 Now we duplicate other sectors and position them around the circle. 
 Below is the basic code for 7 sectors in total.
 
    $list: 7;
    $skew-angle: 45deg-180deg/$list;
    @for $idx from 1 through $list {
        .lottery-sector >div:nth-child(#{$idx}) {
          transform: rotate(($idx)*360deg/$list) skew($skew-angle, $skew-angle);
        }
      }

 And after applying rotate on each sector with increment angel, 
 we get an adaptive turntable container.  
 
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

**2. Square content area in each sector, sizing and positioning**   
i. Square content area in each sector   
If float content sizing & positioning is important, we need some precise controls.
Imagine you need to put content with different size into a 
turntable, and the content area should be as large as possible.
Thus finding out the largest inscribed rectangle parallel with the symmetry 
axis of the sector seems to be a good start.  

    <div class="lottery-sector">
        <div data-role="sector">
            <span data-role="content">content</span>
        </div>
        ...
    </div>

In the dom structure, the only child of each sector is the content dom.
We can draw this geometric approach intuitivly on a white paper as shown in
figure 1. 
Moreover, we can strictly prove that the largest inscribed rectangle is a square.
 Below is the derivation of fetching necessary parameters for style rules.

<div markdown="0">
    <div class="sector-container">
        <div class="lottery-sector full f1">
            <div class="c1"><span style="outline: 1px solid #000"></span></div>
            <p class="pc">C</p>
            <p class="pd">D</p>
            <p class="po">O</p>
            <p class="pa">A</p>
            <p class="pb">B</p>
            <p class="theta">\begin{align}\theta\end{align}</p>
            <p class="alpha">\begin{align}\alpha\end{align}</p>
        </div>
        <p class="figure-label">Figure 1</p>
    </div>
    <p>
        Set \(C\) as the center of the circle, \(O\) as the center of the square, ad \(D\)  as
        the intersection point of segment \(CD\) and the square edge \(AB\).
        Set the edge length of the content square as \(s\), the length of \(CD\) as \(d\), and
        the radius of the container circle as \(r\).According to pythagorean theorem  
        we have the relation between \(s\), \(d\)
        and already known variables \(r\), \(\begin{align}\theta\end{align}\):
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
</div>

ii. Retrieve the style property values   

The above calculated value cannot be applied as css property values directly 
because the skew transform is not scale-invariant. 
In order to positioning/sizing the content square in each sector, 
we need to make reverse derivation to retrieve css rules for sectors before transform.

<div markdown="0">
    <div class="sector-container">
        <div class="lottery-sector full f2">
            <div class="c4"><span style="outline: 1px solid #000"></span></div>
            <p class="pc">C</p>
            <p class="po">O<sub>0</sub></p>
            <p class="pe">E</p>
            <p class="pf">F</p>
        </div>
    </div>
    <p class="figure-label">Figure 2</p>
    <p>&nbsp;</p>
</div>

The above figure 2 shows the original status of a single sector and it's 
content child before transform. The major process form figure 2 to 
figure 1 can be described as follows:

     step1. skew <sector> which is the parent of content (transform origin is left bottom);
     step2. skew <content> back to correct the parent's skew effect (transform origin is center);
     step3. rotate <content> to make the content square parallel with 
            the symmetry axis of the sector;
     
<div markdown="0">
    <p>
    Set \((x,y)\) as any vector on the sector plane. The skew process in step 1 will transform it to
            another vector \((x',y')\). The relation between them is:
    
            \begin{equation*}
            \begin{bmatrix} x' \\ y' \end{bmatrix} =
            \begin{bmatrix} 1 & tan(\alpha) \\ tan(\alpha) & 1 \end{bmatrix}
            \begin{bmatrix} x \\ y \end{bmatrix}
            \\=
            \begin{bmatrix} (1 + tan(\alpha))x \\ (1 + tan(\alpha))y \end{bmatrix}
            \end{equation*}
    
            For any segment on the original sector, we can calculate the scale variation ratio
            of the transform:
            \begin{align}(1+tan(\alpha))\end{align}\
        </p>
        <p>&nbsp;</p>
        <p>
            Similarly, for any vector on the inner square plane, the skew back process in step 2
            will transform it to another vector \((x'',y'')\). The relation between them is:
    
            \begin{equation*}
            \begin{bmatrix} x'' \\ y'' \end{bmatrix} =
            \begin{bmatrix} 1 & tan(-\alpha) \\ tan(-\alpha) & 1 \end{bmatrix}
            \begin{bmatrix} x' \\ y' \end{bmatrix}
            \\=
            \begin{bmatrix} (1 + tan(-\alpha))x' \\ (1 + tan(-\alpha))y' \end{bmatrix}
            \end{equation*}
    
            The scale variation ratio of the transform is:
            \begin{align}(1+tan(-\alpha))\end{align}\
        </p>
        <p><b> NOTE: </b>Although these two transforms is of different coordinate system,
            we can still calculate the right scale variation ratio without an uniform
            coordinate representation.
        </p>
        <p>&nbsp;</p>
        <p>
            Now we will retrieve the css rules of the inner square.<br>
    
            Sizing of the square is decided by width/height property in our case.
            The original width/height has changed twice during step 1 and step 2.
            Set the original width/height as \(\mathbf{s_0}\). According to upper
            derivation we have:
    
            \begin{align}
            \mathbf{s_0} = \frac{s}{(1+tan(-\alpha)) (1+tan(\alpha))}
            \end{align}
    
            <br>
            Positioning of the square is decided by top/right/bottom/left property.
            In our case, right+bottom is enough, aligned to it's parent. As shown in
            figure 2, the original edge distance is the length of \(EF\).
    
            \begin{align}
            |\vec{\mathbf{EF}}| = |\vec{\mathbf{O_0E}}| - |\vec{\mathbf{O_0F}}| \\
            |\vec{\mathbf{O_0C}}| = \sqrt{2}|\vec{\mathbf{O_0E}}|
            \end{align}
    
            Set the edge distance \(\vec{\mathbf{EF}}\) as \(e\).
            The length of \(\vec{\mathbf{O_0F}}\) is half the square edge length,
            i.e. \(\begin{align}\frac{s_0}{2}\end{align}\). Thus the above two equations
            can be simplified as:
    
            \begin{align}
            e = \frac{|\vec{\mathbf{O_0C}}|}{\sqrt{2}} - \frac{s_0}{2}
            \end{align}
    
            In step 1, the center of the square is transformed to O in figure 1,
            and the length of \(\vec{\mathbf{O_0C}}\) has changed
            \(\begin{align}(1+tan(\alpha))\end{align}\) times.
            Step 2 doesn't affect the position of O because the
            transform origin is the square center. Thus we have:
    
            \begin{align}
            (1+tan(\alpha)) |\vec{\mathbf{O_0C}}| = |\vec{\mathbf{OC}}|
            \end{align}
    
            where \(\vec{\mathbf{OC}}\) in figure 1 is transformed vector of
            \(\vec{\mathbf{O_0C}}\) in figure 2.<br>
            The module of \(\vec{\mathbf{OC}}\) can be calculated as:
    
            \begin{align}
            |\vec{\mathbf{OC}}| = d + \frac{s}{2}
            \end{align}
    
            After all we have the edge distance value:
    
            \begin{align}
            e & = \frac{|\vec{\mathbf{O_0C}}|}{\sqrt{2}} - \frac{s_0}{2} \\\\
            & = \frac{|\vec{\mathbf{OC}}|}{\sqrt{2} (1+tan(\alpha))} - \frac{s_0}{2} \\\\
            & = \frac{d + \frac{s}{2}}{\sqrt{2} (1+tan(\alpha))} - \frac{s_0}{2} \\\\
            \end{align}
        </p>
</div>
   
iii. Result    

Below is some examples of turntables with different amount of sectors.

<div markdown="0">
    <div class="lottery-sector s3">
            <div><span>1</span></div>
            <div><span>2</span></div>
            <div><span>3</span></div>
        </div>
        <div class="lottery-sector s6">
            <div><span>1</span></div>
            <div><span>2</span></div>
            <div><span>3</span></div>
            <div><span>4</span></div>
            <div><span>5</span></div>
            <div><span>6</span></div>
        </div>
        <div class="lottery-sector s7">
            <div><span>1</span></div>
            <div><span>2</span></div>
            <div><span>3</span></div>
            <div><span>4</span></div>
            <div><span>5</span></div>
            <div><span>6</span></div>
            <div><span>7</span></div>
        </div>
</div>

I will paste the core SCSS code as the end.

    $container-scale: 60%;
    $list: 7;
    $rotate-offset: 45deg-180deg/$list;
    $skew-angle: 45deg-180deg/$list;
    
    .lottery-sector {
      list-style: none;
      width: $container-scale;
      margin: 30px auto;
      padding-top: $container-scale;
      position: relative;
      overflow: hidden;
      border-radius: 50%;
      box-shadow: 0 0 10rem rgba(255,255,255,0.3);
      border: 1px solid #000;
      background: #eee;
    
      /**/
      $radius: 100%;
      $theta : pi() / $list;
      $alpha : pi() / 4 - $theta;
    
      $center-distance: $radius / sqrt(5 * pow(tan($theta), 2) + 4 * tan($theta) + 1);
      $transformed-edge-length: 2 * tan($theta) * $center-distance;
    
      $skew-scale-ratio-1: (1 + tan($alpha));
      $skew-scale-ratio-2: (1 + tan(-$alpha));
      $skew-scale-ratio: $skew-scale-ratio-1 * $skew-scale-ratio-2;
    
      $initial-edge-length: $transformed-edge-length / $skew-scale-ratio;
    
      $edge-distance: -1/2 * $initial-edge-length + ($center-distance + $transformed-edge-length / 2)/ $skew-scale-ratio-1 / sqrt(2);
    
      >div {
        right: 50%;
        bottom: 50%;
        width: 50%;
        height: 50%;
        position: absolute;
        transform-origin: 100% 100%;
        span{
          width: $initial-edge-length ;
          height: $initial-edge-length;
          display: flex;
          align-items:center;
          text-align: center;
          justify-content:center;
          vertical-align: bottom;
          position: absolute;
          color: #1875E7;
          font-weight: bolder;
          bottom: $edge-distance;
          right: $edge-distance;
          transform: skew(-$skew-angle, -$skew-angle) rotate(-45deg);
          z-index: 10;
          font-size: 5vw;
          //border: 1px solid #000;
        }
      }
      @for $idx from 1 through $list {
        >div:nth-child(#{$idx}) {
          transform: rotate(($idx)*360deg/$list + $rotate-offset) skew($skew-angle, $skew-angle);
        }
      }
    }

/////////////////////////////////////////////////////////////////