# Graphics

## roadmap

math (for reference):

- [3D Math Primer for Graphics and Game Development][gamemath]

learning graphics:

- [scratchapixel]
- [graphics from scratch][graphics-from-scratch]
- [raytracing in one weekend][raytracing-weekend]
- [real-time rendering][realtimerendering]
- [tinyrenderer]
- [rasterization in one weekend][rasterization-weekend]
- [A trip through the Graphics Pipeline][trip]
- [physically based rendering][pbr]

learning api:

- [vulkan-tutorial]
- introduction to 3D Game Programming with DirectX 12
- [vkguide]

game engine:

- [Foundations of Game Engine Development][game-engine-foundations]
- [Game Engine Architecure][engine-architecture]

### other resources

- [GPU Gems][gpugems]
- [graphics-weekly]

- [resources][cgp-resources]
- [gp-resources]
- [Finding Your Home in Game Graphics Programming][learning-graphics-tardif]
- [performance-aware]

## projects

- Make a sphere mesh using spherical coordinates, and render it.
- Implement shader for simple diffuse and specular shading.
- Directional Lights, point lights, and spot lights
- Heightmap Rendering
- Write a simple parser for a simple mesh format such as Wavefront .obj, import
  it into your program and render it. In particular, try and import and render
  meshes with textures.
- Implement a simple minecraft renderer. It is surprisingly simple to render
  minecraft-like worlds, and it is also very learningful.

- Render reflections using cubemaps
- Shadow rendering using shadow maps.
- Implement view frustum culling. This is a simple, yet very practical
  optimization technique.

- Implement rendering of particle systems
- Learn how to implement Gamma Correction.
- Implement normal mapping
- Learn how to render lots of meshes efficiently with instanced rendering
- Animate meshes with mesh skinning.

### advanced

- Various post-processing effects. Like Bloom(using Gaussian blur), ambient
  occlusion with SSAO, anti-aliasing with FXAA.
- Implement deferred shading, a technique useful for rendering many light sources.

<!-- references -->

[raytracing-weekend]: https://raytracing.github.io/
[scratchapixel]: https://www.scratchapixel.com/index.html
[tinyrenderer]: https://github.com/ssloy/tinyrenderer/wiki
[rasterization-weekend]: https://tayfunkayhan.wordpress.com/2018/11/24/rasterization-in-one-weekend-part-i/
[graphics-from-scratch]: https://www.gabrielgambetta.com/computer-graphics-from-scratch/
[realtimerendering]: https://www.realtimerendering.com/
[gamemath]: https://gamemath.com/
[pbr]: https://pbr-book.org/4ed/contents
[gpugems]: https://developer.nvidia.com/gpugems/gpugems/contributors
[learnopengl]: https://learnopengl.com/
[graphics-weekly]: https://www.jendrikillner.com/tags/weekly/
[game-engine-foundations]: https://foundationsofgameenginedev.com/
[cgp-resources]: https://gist.github.com/notnotrobby/ceef71527b4f15869133ba7b397912e9
[gp-resources]: https://graphics-programming.org/resources/
[engine-architecture]: https://www.gameenginebook.com/
[trip]: https://fgiesen.wordpress.com/2011/07/09/a-trip-through-the-graphics-pipeline-2011-index/
[learning-graphics-tardif]: https://alextardif.com/LearningGraphics.html
[vulkan-tutorial]: https://vulkan-tutorial.com/
[vkguide]: https://vkguide.dev/
[rendering-engine]: https://gist.github.com/Erfan-Ahmadi/defe4ab99af97f624b68e0dccb0712ea
[performance-aware]: https://www.computerenhance.com/p/table-of-contents
