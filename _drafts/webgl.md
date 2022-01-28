# WebGL

pairs of functions written in C/C++ like language called GLSL:
1. vertex shader: compute vertex positions.
2. fragment shader: when rasterizing different kind of primitives, compute a color for each pixel of the primitive currently being drawn.

Nearly all of the entire WebGL API is about setting up state for these pairs of functions to run.  For each thing you want to draw you setup a bunch of state then execute a pair of functions by calling `gl.drawArrays` or `gl.drawElements` which executes your shaders on the GPU.

4 ways a shader can receive data.
1. Attributes and Buffers
  1. buffer: binary data
  2. attributes: which buffer, data type, offset in buffer, size. cannot random access.
2. Uniforms
  1. global variables.
3. Textures
  1. arrays of data, can berandomly accessed.
  2. typically an image data.
  3. can store any data.
4. Varyings
   1. a way for a vertex shader to pass data to a fragment shader.
   2. interpolate color

WebGL only cares about 2 things: clip space coordinates and colors. 

Clip space coordinates always go from -1 to +1 no matter what size your canvas is.