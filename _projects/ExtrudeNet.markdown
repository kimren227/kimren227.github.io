---
layout: page
title: ExtrudeNet&#58; Unsupervised Inverse Sketch and Extrude for Shape Parsing
description:  
img: /assets/img/ExtrudeNet/teaser_1.jpg
importance: 1
category: work
---

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ExtrudeNet/authors.png' | relative_url }}" alt="" title="Authors"/>
    </div>
</div>

<br>
<br>
<center><video class="video-fluid" autoplay loop muted>
        <source src="/assets/img/ExtrudeNet/ExtrudeNet.mp4" type="video/mp4" />
</video></center>

<table align="center" >
<tbody><tr>		
    </tr>
    <tr>
        <td width="100">
        </td>
        <td width="150">
        <center>
            <a href="https://arxiv.org/abs/2209.15632" class="imageLink"><img src="/assets/img/arxiv.png" width="200"></a><br>
        </center>
        </td>
        <td width="300">
            <center>
                <a href="https://github.com/kimren227/ExtrudeNet" class="imageLink"><img src="/assets/img/github.png" width="280"></a><br>
            </center>
        </td>
        <td width="100">
        </td>
    </tr>    
</tbody></table>

<h1>Abstract</h1>
<div class="row justify-content-sm-center">
    <div class="col-sm-7 mt-3 mt-md-0">
        Sketch-and-extrude is a common and intuitive modeling process in computer aided design. This paper studies the problem of learning the shape given in the form of point clouds by inverse sketch-and-extrude. We present ExtrudeNet, an unsupervised end-to-end network for discovering sketch and extrude from point clouds. Behind ExtrudeNet are two new technical components: 1) an effective representation for sketch and extrude, which can model extrusion with freeform sketches and conventional cylinder and box primitives as well; and 2) a numerical method for computing the signed distance field which is used in the network learning. This is the first attempt that uses machine learning to reverse engineer the sketch-and-extrude modeling process of a shape in an unsupervised fashion. ExtrudeNet not only outputs a compact, editable and interpretable representation of the shape that can be seamlessly integrated into modern CAD software, but also aligns with the standard CAD modeling process facilitating various editing applications, which distinguishes our work from existing shape parsing research.
    </div>
    <div class="col-sm-5 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ExtrudeNet/teaser_1.jpg' | relative_url }}" alt="" title="example image"/>
    </div>
</div>


<h1>ExtrudeNet Structure</h1>
<div class="row justify-content-sm-left">
    <div>
        <div class="col-sm-16 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ExtrudeNet//extrude_net_pipeline_1.jpg' | relative_url }}" alt="" title="example image"/>
        </div>
        We take inspiration from the process of sketch and extrude, a popular and intuitive approach widely used in the field of computer aided design (CAD) where engineers usually model shapes by first sketching a closed free form sketch (profile) in a 2D sketch plane and then extruding the sketch into 3D. 

        To realize ExtrudeNet, we create three modular components: <b> 1) rBezierSketch</b> , which generates a simple closed curve (i.e.\ no self-intersection); <b> 2) Sketch2SDF </b> , a versatile numerical method for computing Signed-Distance-Field from parametric curves; and <b> 3)   </b> , a differentiable method for extruding 2D Signed-Distance-Field (SDF) into a 3D solid shape. Built upon these components, ExtrudeNet takes a point cloud as input and outputs sketch and extrude parameters which form a compact, interpretable and editable shape representation.
        <br>
       
   </div>
</div>
<br>
<h2>rBezierSketch</h2>
<div class="row justify-content-sm-left">
    <div class="col-sm-16 mt-3 mt-md-0">
    rBezierSketch is designed with the following benifits in mind.
    <ul>
        <li>Compatible with industry standards as it is a simple form of NURBS </li>
        <li>It can represent straight lines, circles and free-form curves in a uniform representation</li>
        <li>It represent a star-convex set and will not self intersect </li>
        <li>It can be constraint to generate only C1 continuous sketch </li>
    </ul>

    The basic mathematical model is a closed curve formed by N curve segments defined by special rational cubic Bezier curves $$ C_k(t), t\in [0,1], k=0,1,\cdots,N-1 $$ 
    The equation for each curve segment is:
    $$ 
        C_k(t) = \frac{P^k_0B_0^3(t)+ w^k_1 P^k_1B_1^3(t)+ w^k_2 P^k_2B_2^3(t)+ P^k_3B_3^3(t)}{B_0^3(t)+w^k_1 B_1^3(t)+ w^k_2B_2^3(t)+B_3^3(t)}
    $$
    We use the polar coordinate system to help define the control points P, where: 
    $$ 
        P^k_i = (x^k_i, y^k_i) = (\rho_i^k\cos(\alpha_i^k), \rho^k_i\sin(\alpha^k_i))
    $$


    $$ 
        \alpha^k_1= \alpha^k_0+\theta,\;\; \alpha^k_3 = \alpha^k_0+\frac{2\pi}{N},\;\; \alpha^k_2 = \alpha^k_3-\theta
    $$

    $$ 
        \displaystyle \theta =\frac{2\pi}{4N}+\tan^{-1}\left(\frac{1}{3}\tan\left(\frac{2\pi}{4N}\right)\right)
    $$


    These angles are specially designed to achieve the above stating benifits. (see paper for details)


    </div>
    <div class="col-sm-16 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ExtrudeNet/circular_arc_c0_c1_1.jpg' | relative_url }}" alt="" title="example image"/>
    </div>
 </div> 
 <br>
 <br>
 <h1>Sketch2SDF</h1> 
 <div class="row justify-content-sm-left">
    <div>
        We present a numerical method for computing the SDF of simple parametric sketches. The method is general. While it applies to the rational cubic Bézier curves here, it also works for other parametric curves. This method also yields good gradients which is friendly for deep learning applications.
        The SDF of a sketch is defined by the Distance-Field DF(p), the smallest distance from a given testing point p to the curve, multiplied by a sign SIGN(p) which indicates whether the testing point p is inside or outside of the sketch.
        We first sample a set of sample points from the curve
        $$
        S =\left\{\left(x(\frac{i}{n}),y(\frac{i}{n})\right)\mid i=0,1,\cdots,n\right\}
        $$
        and the distance field can be approximated by
        $$
        DF(p)= \underset{s\in S}{\min} \|s-p\|_2
        $$
        the normal of each sample point is computed
        $$
        N(t) =  \left(\frac{d y(t)}{dt}, -\frac{d x(t)}{dt}\right)
        $$
        the sign can be computed as follow
        $$
        SIGN(p)= \frac{N(CT(p))\cdot (C(CT(p))-p)}{\|N(CT(p))\cdot (C(CT(p))-p)\|_2 + \epsilon}
        $$
        put them together, the final sdf is equal to
        $$
        SDF_s(p) = SIGN(p) \times DF(p). 
        $$
</div>

 <h1>Extrusion</h1> 
 <div class="row justify-content-sm-left">
    <div>
        We first transform the testing point p back to a point p' by reversing the transformations that transform the XY-plane to the target sketch plane. Then we compute the signed distance of p' with respect to the upright extrusion shape whose base lies on the XY-plane
        If the testing point is inside the extruded shape
        $$
            SDF_i(p') = \max(\min(SDF_s((p'_x, p'_y)), h-p'_z, p'_z), 0)
        $$
        otherwise
        $$
        SDF_o(p') = -[
        (\min(h-p'_z,0))^2+(\min(p'_z,0))^2
        \mbox{} +(\min(SDF_s((p'_x, p'_y)),0))^2 ]^{\frac{1}{2}}
        $$
        Finally, the overall SDF is the sum of the two cases.
        $$
        SDF(p') = SDF_i(p') + SDF_o(p')
        $$
        
</div>

<br>
<br>
<h1>Evaluation of rBezierSketch</h1> 
<br>
<br>
<div class="row justify-content-sm-left">
    <h3>Approximability</h3>
    <div>
        To demonstrate the approximation ability and the ease of learning of the proposed rBezierSketch, we conduct a fitting experiment that directly optimize for the radial coordinate that best reconstructs a given raster emoji image.
        <br>
        <br>
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ExtrudeNet/2d_experiments_1.jpg' | relative_url }}" alt="" title="evaluation result image"/>
        <br>
        <br>
        <br>
        <br>
    </div>
    <h3>Versatility and error analysis</h3>
    <br>
    <br>
    <div class="col-sm-8 mt-3 mt-md-0">
        To show the versatility, we implemented four different kinds of parametric curves, namely polygon, ellipse, Cubic Bezier Sketchs with C1 and C0 continuity. Note that our method is not limited to these curves, and can be easily adapted to new parametric curve types. We also plot the error introduced by apprimation by comparing our results with analytical results.
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ExtrudeNet/sketch2sdf_analysis_1.jpg' | relative_url }}" alt="" title="example image"/>
    <br>
    <br>
    <br>
    <br>
    </div>
    <h3>Edibility of Sketch and Extrude</h3>
    <br>
    <br>
    <div class="col-sm-8 mt-3 mt-md-0">
       As rBezier sketch is essentially a cubic rational bezier curve and Extrusion is a typical operation for B-Rep representation, our results are well compatiable to popular CAD software. 
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ExtrudeNet/edit_fusion.png' | relative_url }}" alt="" title="example figure"/>
        <br>
        <br>
    </div>
    <h3>Comparison on Geometric Evaluation Results</h3>
    <div>
        The following figure shows the qualitative comparison with baselines, We can see our method achieves better geometry approximation with fewer primitives.
         <br>
         <br>
        <img class="img-fluid rounded z-depth-1" src="{{ '/assets/img/ExtrudeNet/compare_baselines_1.jpg' | relative_url }}" alt="" title="evaluation result image"/>
    <br>
    <br>
    <br>
    <br>
    </div>
</div>

<h3>Acknowledgement</h3>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        The work is partially supported by a joint WASP/NTU project (04INS000440C130), Monash FIT Startup Grant, and SenseTime Gift Fund.
    </div>
</div>




