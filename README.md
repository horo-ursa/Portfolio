# Portfolio

## CUDA path tracer

<p align="center">
  <img src="https://user-images.githubusercontent.com/54868517/194881974-b596f7ff-f59a-443c-8cf3-33cb7a69f9a3.png" width="300" height="300">
</p>

In this project, I implemented a CUDA-based path tracer, which is very different from CPU path tracer. Pathtracing in CPU is used to be recursive, but since CUDA doesn't support recursion(or very slow on new GPUs), this path tracer uses a iterative way.
<p align="center">
  <img style="float: right;" src="https://user-images.githubusercontent.com/54868517/194418589-a882f9be-abda-4ae0-afe7-5b39bb03791e.png" width="500" height="200">
</p>

Also, traditional CPU pathtracing is down pixel by pixel, but as the figure shows, doing the same algorithm(parallel by pixel) on GPU will cause different threads finish in different time and therefore cause many divergence. The solution, then, is to parallel by rays. We launch a kernel that traces **one** bounce for every ray from a ray pool, update the result, and then terminate rays with stream compaction


### 1. Basic shading

There are three basic shadings implemented --- pure diffuse, pure reflection, and both reflection and refraction. Diffuse is done by generating a new direction randomly across the hemisphere, reflection is done by reflecting the incident ray direction by the surface normal, and refraction is done by using Schlick's approximation to create Fresnel effect.
<p align="center">
  <img src="https://user-images.githubusercontent.com/54868517/194421652-410c82f6-60c2-4ca4-8200-2039355fc622.png" width="300" height="300">
</p>

### 2. Acceleration

- Stream Compaction

The first acceleration is to remove unnecessary rays that are terminated(remaining bounce = 0) by using Stream Compaction. In this project I used ```thrust::stable_partition``` to compact, which gives noticeable performance improvement.

- Sort rays by material type

The second acceleration is to sort all rays by their material types, which can result in good memory access pattern for CUDA. In this project I used ```thrust::sort_by_key``` to sort.

- Cache the first bounce for re-use

The third acceleration is to cache the first bounce intersection data, therefore all other iterations can use the same data and save some performance. 

### 3. Microfacet shading model

Microfacet shading model is a very popular pbr model, which works by statistically modeling the scattering of light from a large collection of microfacets.

<p align="center">
  <img src="https://user-images.githubusercontent.com/54868517/194881501-66ea73cd-66ab-4d6e-a219-7e33247528e0.png" width="300" height="300"/>
  <img src="https://user-images.githubusercontent.com/54868517/194881503-35fcbc65-0699-4a77-896d-be69ad1448cb.png" width="300" height="300"/> 
</p>



### 4. Mesh loading with tinyObj

In order to test my microfacet shading model, I need to add more complex meshes and decided to use tinyObj to load ```OBJ``` format meshes.  In order to speed up ray-mesh intersetion, I created a toggleable AABB bounding box for intersection culling.

### 5. Stochastic Sampled Antialiasing

According to [Stochastic Sampling](https://web.cs.wpi.edu/~matt/courses/cs563/talks/antialiasing/stochas.html), in stochastic sampling, every point has a finite probability of being hit. Stochastic sampling is extremely powerful as the visual system is much more sensitive to aliases than noise. The one that I choose to implement is Jittered, which is done by perturbing the position that camera shoot rays.

**Left: no antialiasing**                  
**right: with antialiasing**


<p align="center">
  <img src="https://user-images.githubusercontent.com/54868517/194441398-835325e3-cfa5-4f2b-9d6f-3d99b6948547.png" width="300" height="300"/>
  <img src="https://user-images.githubusercontent.com/54868517/194441429-c3a3363c-28a6-4d64-b558-8e3af246329d.png" width="300" height="300"/> 
</p>

### 6. Physically-based Depth-of-field

In regular pathtracing, we assume that the camera is an ideal pinhole camera, which is not physically possible. In order to create a physically-based camera, a classic way is the **thin lens approximation**. The algorithm I choose to implement is adapted from [PBRT[6.2.3]](https://www.pbr-book.org/3ed-2018/Camera_Models/Projective_Camera_Models), and the idea is quite simple. We set another 2 parameters for the camera, which are **lensRadius** and **focalDistance**. We sample on a concentric disk based on the lensRadius, and then jitter the camera ray based on that sample position and focalDistance. This simple algorithem can actually create very beautiful results.

<p align="center">
  <img src="https://user-images.githubusercontent.com/54868517/194445204-bee8a91c-3f3f-4e00-b104-3a88ff2d1b5e.png" width="300" height="300"/>
  <img src="https://user-images.githubusercontent.com/54868517/194445206-c30230a2-3a20-41a0-856f-3ac8a5a502ae.png" width="300" height="300"/> 
  <img src="https://user-images.githubusercontent.com/54868517/194445205-6e306d7c-6ef7-45c5-a879-7495620d6a27.png" width="300" height="300"/> 
</p>


## Direct-Reseasrch
Project result:
- Geometry particle system
- Percentage closer soft shadow
- Screen space reflection
- LOD implemented with tessollation shaders

![bb67069c347937cd7d4cbcd1e5478d5](https://user-images.githubusercontent.com/54868517/166194061-b8dc556a-e26f-4e3b-a173-a44026133447.png)



https://user-images.githubusercontent.com/54868517/190204490-b5908695-ecae-40f6-93df-d54c7bed3774.mp4



## 485_Game_Engine

This is the class project of USC ITP485.

I implemented a custom game engine with common functions and systems, such as lighting, collision detection, animation system, job system, profiling system, and post effect using C++ and DirectX 11

### First Video

This video shows player movement, character animation, basic Blinn-phong shading, bloom post-effect shader, normal mapping shader, and  collision detection

https://user-images.githubusercontent.com/54868517/190210054-de61d800-97bf-4187-9dd6-e1e8ad8ed6e9.mp4


### Second Video

This video shows the CPU-based particle system implemented with compute shader.

https://user-images.githubusercontent.com/54868517/190210065-387b0d6e-2cf5-49e2-b54b-09b6b0e4fb12.mp4

## 420_OpenGL_Projects
These are class projects from USC CSCI-420 Computer Graphics

### Roller Coaster Project
In this project, I experimented with OpenGL version 3.3 to create a physically realistic roller coaster.

**implemented features**

1. Railways are created with Catmull-Rom splines

2. Camera. You can use WASD to move, use mouse to rotate, and mid-button to zoom in/out. Camera can also move along the railway

3. T-shaped rail cross-sections that are auto-generated by the program. 

4. Double rail and railway sleepers between them

5. Implemented sky and ground using cubemap.

6. Environment mapping for railways. If you look closer, you can see the railways are reflecting the environment.

7. Lighting! I added a directional light in the scene, and railway sleepers are rendered with Blinn-Phong model. If you move and rotate the camera around, you can see the specular.

8. Physically realistic movement.

**Video**


https://user-images.githubusercontent.com/54868517/190031678-f6b37458-5508-4ecc-9911-61ead5512d3e.mp4

### Ray Tracing Project

In this project, I implemented a CPU-based ray tracer. 

**Implemented features**

1. Bounding Volume Hierarchy for acceleration

2. Multisample Anti-Aliasing to generate high-quality images

3. multithreading to accelerate recursive reflection.

**Image Example**

![003_extra](https://user-images.githubusercontent.com/54868517/190033328-99b5e9ae-0cc8-40e1-acde-e1ec65904ce1.jpg)

![005_extra](https://user-images.githubusercontent.com/54868517/190033334-2b17ecc4-473f-45ac-b9c1-c9e871347e88.jpg)
