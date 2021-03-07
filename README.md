# webgl2-tetris

# Rules

- https://harddrop.com/wiki/Tetris_Guideline
- https://harddrop.com/wiki/Tetris_Worlds
- ignore T-spin points
- Single HTML page

# Outcome

- CPU
- Memory
- Rendering Performance
- Implementation Complexity

# Shaders

Programs that run on the GPU. Have their own language (ex. GLSL)

# Simplification (Shaders can do many more things)

1. Vertex Shader, specify the points on the screen
2. Fragment Shader, fill in the specified points.

# Transformations

To position points on the screen we need 3 transformations:

1. Translation
2. Rotation
3. Scale

If we represent a point on our screen as a 3D vector, [x, y, 1] then we can use a matrix to encode these 3 transformations.

## Translation Matrix

1 0 tx
0 1 ty
0 0 1

## Rotation Matrix

cosθ -sinθ 0
sinθ cosθ 0
0 0 1

## Scale Matrix

sx 0 0
0 sy 0
0 0 1

Note: Matrix multiplications are not commutative, ie, order matters.
We can apply these transformation matrices in different orders with different effects.

# Projection (Pixels to Clip Space)

```js
// convert the rectangle from pixels to 0.0 to 1.0
vec2 zeroToOne = position / u_resolution;

// convert from 0->1 to 0->2
vec2 zeroToTwo = zeroToOne * 2.0;

// convert from 0->2 to -1->+1 (clip space)
vec2 clipSpace = zeroToTwo - 1.0;

gl_Position = vec4(clipSpace * vec2(1, -1), 0, 1);
```

We can also use a matrix to represent the transformation from pixels to clip space.

1. Scale by `1/resolution`
2. Scale by `2.0`
3. Translate by [-1.0, -1.0, 0]
4. Scale by [0, -1]

This can be presented by a single 'projection' matrix:

```js
projection: (width, height) => {
  // Note: This matrix flips the Y axis so that 0 is at the top.
  return [
    2 / width, 0, 0,
    0, -2 / height, 0,
    -1, 1, 1,
  ];
},
```
