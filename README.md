Download Link: https://assignmentchef.com/product/solved-cs148-homework-3-programmable-shaders
<br>
Introduction to Computer Graphics and Imaging

<em>Warning: </em>The coding portion of this homework involves features of OpenGL that are much newer than ones we have used in previous assignments. Thus, there are some odds they won’t work on your machine. Even if you don’t start coding for the assignment early, do try compiling and running the skeleton code ASAP to make sure everything is functional. If there is an issue with your hardware, you may need to code this assignment in person on the Myth machines (forwarding may also fail).

In the written portion of this problem set, we’ll explore properties of and computations involving barycentric coordinates, introduced in Lecture 4. These coordinates are used to interpolate normals, textures, depths, and other values across triangle faces, so it is important to understand how they behave.

To begin with, let’s say we have a triangle with vertices<em>~a</em>,<em><sup>~</sup>b</em>, and<em>~c </em>in <strong>R</strong><sup>2</sup>. We’ll define the <em>signed area </em>of triangle <em>abc </em>as follows:

<em><sub>A</sub></em><em> a</em><em>x       c</em><em>x </em>

<em> a</em><em>y      c</em><em>y </em>

<strong>Problem 1 </strong>(10 points)<strong>. </strong><em>(a) Use cross products to show that the absolute value of A</em>(<em>abc</em>) <em>is indeed the area of the triangle that we expect. (b) Show that A</em>(<em>abc</em>) <em>is positive when</em><em>~a,</em><em><sup>~</sup>b, and </em><em>~c are in counterclockwise order and negative otherwise. Drawing pictures to demonstrate the solution for part (b) is good enough: no need for a formal proof.</em>

Now, let’s take a point <em>~p </em>in triangle <em>abc</em>. Assume that<em>~a</em>,<em><sup>~</sup>b</em>, and<em>~c </em>are given in counterclockwise order. Then, we can apply the formula above to yield straightforward expressions for barycentric coordinates.

<strong>Problem 2 </strong>(10 points)<strong>. </strong><em>Define the barycentric coordinates of </em><em>~p using the signed area formula above. Show that your coordinates always sum to </em>1 <em>and that they are all positive exactly when </em><em>~p is inside the triangle abc.</em>

Our definition of barycentric coordinates in terms of areas seems somewhat arbitrary! After all, we could take any three numbers as functions of vertex positions and normalize to 1. Thankfully, we won’t make you prove it, but the following relationship holds uniquely:

where <em>~p </em>has barycentric coordinates (<em>α<sub>a</sub></em>, <em>α<sub>b</sub></em>, <em>α<sub>c</sub></em>) summing to 1.

<strong>Problem 3 </strong>(10 points)<strong>. </strong><em>Use this formula to suggest a </em>2 × 2 <em>linear system that will yield two of the three barycentric coordinates of </em><em>~p. Provide a formula for the third.</em>

<strong><em>Hint: </em></strong><em>Do the second part of the problem first. Then plug in the third coordinate in our expression for </em><em>~p and refactor into a linear system for α<sub>a </sub>and α<sub>b</sub>.</em>

If you’ve done everything correctly so far, you should find that applying the standard formula for the inverse of a 2 × 2 matrix to your solution to Problem 3 yields your signed area formula from Problem 2. You can check this on scrap paper if you desire.

Now, let’s say we want to interpolate a function <em>f </em>smoothly given its values at<em>~a</em>,<em><sup>~</sup>b</em>, and<em>~c</em>. We will label these values <em>f<sub>a</sub></em>, <em>f<sub>b</sub></em>, and <em>f<sub>c</sub></em>, resp., and will use <em>f</em>(<em>~</em><em>p</em>) to denote the interpolated function with values everywhere on <strong>R</strong><sup>2 </sup>given by <em>f</em>(<em>~</em><em>p</em>) = <em>α</em><em>a f<sub>a </sub></em>+ <em>α</em><em>b f<sub>b </sub></em>+ <em>α</em><em>c f<sub>c</sub></em>

where the barycentric coordinates of <em>~p </em>are (<em>α<sub>a</sub></em>, <em>α<sub>b</sub></em>, <em>α<sub>c</sub></em>). It’s easy to see that <em>f</em>(<em>~</em><em>a</em>) = <em>f<sub>a</sub></em>, <em>f</em><em> f<sub>b</sub></em>, and <em>f</em>(<em>~</em><em>c</em>) = <em>f<sub>c </sub></em>(make sure you understand why!). We also have one final important property:

<strong>Problem 4 </strong>(10 points)<strong>. </strong><em>Take</em><em>. Show that f</em><em> for any t </em>∈ <strong>R</strong><em>.</em>

<strong><em>Comprehensive hint: </em></strong><em>Use barycentric coordinates to write</em>

<em>Now, define </em><em>. Plug in our barycentric expressions for</em><em> and </em><em> above and refactor to yield the barycentric coordinates of </em><em>~p from the coefficients of</em><em>~a,</em><em><sup>~</sup>b, and</em><em>~c. Use these barycentric coordinates to find f</em>(<em>~</em><em>p</em>) <em>as it is defined above the problem, and refactor once again to get the final result.</em>

This final result actually shows us that barycentric coordinates are the right way to interpolate depths for the depth buffer (ignoring the nonlinearity we discussed in Lecture 4). Namely, if we restrict <em>f </em>to a line through and , it looks linear.

By the way, just like Bresenham’s algorithm, there are incremental tricks for computing barycentric coordinates inside triangles given their values on the edges. Modern GPUs make use of fragment-level parallelization and thus such tricks might not be useful.

In the coding half of this assignment,<a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a> you will get some practice using GLSL to write shaders, which implicitly make use of barycentric coordinates and other tools to modify fragments, vertex positions, and other data. Lecture 6 covers the basics of GLSL as well as the philosophy behind writing shaders. Recall that the two most basic types of shaders in OpenGL are:

<strong>Vertex shaders </strong>These shaders modify the vertices of a triangulated piece of geometry. The default OpenGL vertex shader multiplies vertex coordinates by model-view and projection matrices and applies other aspects of the fixed-function pipeline, but we can write a shader of our own to modify shape.

<strong>Fragment shaders </strong>These shaders let us modify the method used to compute pixel colors, potentially employing textures and other external data.

OpenGL also allows for <em>geometry shaders</em>, but we won’t make use of them in CS 148.

Some useful facts beyond what was covered in lecture to get you started writing shaders:

<ul>

 <li>The OpenGL “Orange Book” documents details of GLSL and can help you look up advanced features and particular functionality. There also is some information in the “Red Book,” in the course textbook, and in online tutorials.</li>

 <li>The entry point of a GLSL shader is the function main(), as in C. A vertex shader’s main functionality is to compute gl Position, the position of the vertex, and a fragment shader’s functionality is to compute gl FragColor, the color of the fragment.</li>

 <li>GLSL has all the simple C data types. It also has built-in vector types vec2, vec3, and vec4 (with constructors like vec2(x,y)). You can access fields of these vectors as v.x/v.y/v.z/v.w or as v.r/v.g/v.b/v.a. You can extract the RGB part of an RGBA color by “swizzling:” c.rgb, and multiplying vectors does so <em>element-wise</em>. There are many useful helper functions including dot(), cross(), length(), normalize(), and reflect().</li>

</ul>

The assignment this time comes with a makefile that should work on most systems, although it will be evaluated on the Myth machines. It makes use of the OpenGL Extension Wrangler Library (GLEW) to deal with shaders. Visual Studio and Xcode users shouldn’t need any extra work to use this library. On Linux, you may need to install libglew-dev or download GLEW online (some systems may need to change the makefile to say “-lGLEW” rather than “-lglew”).

Make sure you can compile and run the assignment ASAP, as GLSL support can be shaky from one computer to another. To do so, first compile the included libst library and then ProgrammableShading. You actually won’t need to recompile these if you only change the shader files – shaders get compiled separately at runtime. To run the program, you will use the incantation:

./ProgrammableShading vertShader fragShader lightProbeImg normalMapImg

Here, vertShader and fragShader are the shader files, and lightProbeImg and normalMapImg are images that we will use in later parts of this assignment. For example, to run the default skeleton shaders, type:

./ProgrammableShading kernels/default.vert kernels/phong.frag images/lightprobe.jpg images/normalmap.png

Navigation in this simple viewer uses the left mouse button to rotate and the right mouse button to zoom. Pressing r resets the camera position, t toggles between displaying the plane and displaying the Utah teapot, and the arrow keys move the camera horizontally and vertically. Press s to take a screenshot and save it to screenshot.jpg.

Your first shader in GLSL will be a fragment shader implementing the Phong reflection model. Recall that Phong reflection separates specular, diffuse, and ambient reflection yielding a final intensity of

<em>I </em>= <em>M</em><em>aI</em><em>a </em><em>M</em><em>dI</em><em>d </em><em>sM</em><em>sI</em><em>s</em>

where

(a)                                                       (b)

Figure 1: For problem 5: (a) a normal-mapped plane; (b) a teapot shaded using the Phong model.

<ul>

 <li><em>I<sub>a</sub></em>, <em>I<sub>d</sub></em>, and <em>I<sub>s </sub></em>are the ambient, diffuse, and spectral illuminations (we’ll specify them as threevectors of floats between 0 and 1 with one component for each color channel).</li>

 <li><em>M<sub>a</sub></em>, <em>M<sub>d</sub></em>, and <em>M<sub>s </sub></em>are the material’s ambient, diffuse, and spectral colors (we’ll take <em>M<sub>a </sub></em>= <em>M<sub>d</sub></em>).</li>

 <li><em><sup>~</sup>L<sub>m </sub></em>is a unit vector from the surface point to the light source.</li>

</ul>

is the unit surface normal.

<em>m </em>is the <em>reflected </em>direction of a vector pointing from the surface to the light source over the

normal <em>N</em><em><sup>~ </sup></em>.

is a unit vector pointing from the surface to the viewer.

<ul>

 <li><em>s </em>controls the shininess of the material.</li>

</ul>

Note that multiplication like <em>M<sub>a</sub>I<sub>a </sub></em>happens component-by-component. Also note that the dot products could be negative, so you’ll have to clamp them to be ≥ 0.

For fun, the skeleton code already implements <em>normal mapping</em>, which reads normal vectors from a texture to yield more interesting effects on the plane. Figure 1 shows this effect applied to the plane using images/normalmap.png, as well as the teapot with Phong shading but no normal map.

<strong>Problem 5 </strong>(30 points)<strong>. </strong><em>Modify kernels/phong.frag to implement this shading model.</em>

Next, we’ll try our hand at writing a vertex shader, implementing a species of displacement mapping where the (initially flat) plane is modified along its normal direction.

We’ll simulate water ripples using a height field function specifying how each vertex on our plane should be moved up or down. Periodic functions are the easiest way to accomplish such an effect; for example, the function <em>s </em>cos(2<em>πau</em>) cos(2<em>πav</em>) will make a rippling height field oscillating between <em>s </em>and −<em>s</em>, where <em>u </em>and <em>v </em>are texture coordinates. <em>Note: For full credit in your submission, come up with your own function rather than implementing this one!</em>

You should write a vertex shader that does the following:

<ol>

 <li>Evaluate your height field on texture coordinates (<em>u</em>, <em>v</em>) to yield height <em>h</em>(<em>u</em>, <em>v</em>).</li>

 <li>Displace the vertex by <em>h</em>.</li>

</ol>

Figure 2: For problem 6.

Since you just modified the geometry, however, you’ll need to do one more task. The normal to the plane used to be the constant (0, 1, 0), but now our surface contains ripples and must be shaded as such! Thus, you must also:

<ol start="3">

 <li>Compute a new unit normal, using either a closed form or finite differencing (look it up orask during office hours!).</li>

</ol>

Figure 2 shows proper output for one potential ripple function.

<strong>Problem 6 </strong>(30 points)<strong>. </strong><em>Modify kernels/displacement.vert to implement your height field displacement shader. Be sure to output correct normals.</em>

<em>Optional Extra Credit</em>

Shiny materials like metal reflect the surrounding environment. This is because these materials admit predominantly specular lighting. In class and in a future assignment, we’ll talk about <em>ray tracing </em>as the “proper” solution to this problem, but it can be too slow for real-time applications. Instead, we’d like to compromise, using OpenGL’s fast rendering to obtain a plausible reflectional rendering. We will use a technique called <em>environment mapping </em>(also known as <em>reflection mapping</em>) to achieve these effects. The trick is to use a <em>light probe </em>image encoding the color and intensity of the light coming in from all directions.

There are several ways to encode and decode such light information, but in this assignment we will use <em>spherical mapping </em>from a mirror ball image. Note that our math below assumes the light probe was photographed using an <em>orthographic projection</em>, an obviously nonphysical assumption but one that simplifies the math considerably.

Let’s say we take a photo of a chrome ball in our scene, as in Figure 4(a). Since our ball only reflects specularly, the color we see at each surface point must come from a distinct light direction, illustrated in Figure 3(a). In the diagram, for each point on the surface, the direction of the light source that is illuminating that point from our camera perspective is shown with a green arrow. Note that on the top and bottom of the sphere, the light we see from the eye position is coming from directly behind the ball. In fact, as suggested in Figure 3(b), photographing <em>half </em>the sphere tells us about light in all directions!

To extract lighting information from the light probe, we will assume that the direction and magnitude of the vector from the camera to the light probe is the same as the vector from the direction of our OpenGL scene camera to the point on the surface we are shading.

(a)                                                                                           (b)

(c)                                                                                           (d)

Figure 3: Images to explain the algorithm for problem 7.

(a)                                                (b)

Figure 4: For problem 7.

Recall that the source of specular light reaching the viewer at <em>V</em><em><sup>~ </sup></em>is the reflection of <em>V</em><em><sup>~ </sup></em>across the normal vector <em>N</em><em><sup>~ </sup></em>. We’re assuming our probe and surface have approximately the same <em>V</em><em><sup>~ </sup></em>, so we only need to find the color of the point on the sphere that shares the same <em>N</em><em><sup>~ </sup></em>!

Let’s say the center of the sphere is (0, 0, 0). Then, the point on the sphere with normal <em>N</em><em><sup>~ </sup></em>is simply <em><sup>~</sup>P </em> (an interesting property of the unit sphere is that position and normal vectors are the same, shown in Figure 3(c)). We’re assuming an orthographic projection of the unit sphere, shown in Figure 3(d), so to find the right lookup on the probe image we simply remove one of the three coordinates! Finally, we map <em>x</em>, <em>y </em>∈ [−1, 1] to <em>x</em><sup>0</sup>, <em>y</em><sup>0 </sup>∈ [0, 1] in image texture coordinates. Figure 4(b) shows a sample of the final result.

<strong>Problem 7 </strong>(15 points extra credit)<strong>. </strong><em>Modify kernels/lightprobe.frag to implement the strategy for environment mapping described above. It should look good on the teapot; the normal-mapped plane will look messy since its normals change with such high frequency.</em>

<a href="#_ftnref1" name="_ftn1">[1]</a> Adapted from a nearly identical assignment used in Pat Hanrahan’s CS 148 course in Fall 2011.