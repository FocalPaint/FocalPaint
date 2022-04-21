# Custom Brush Shaders

Yes, that's right. Now you can create your own custom Compute Functions in Metal Shading Language:

https://developer.apple.com/metal/Metal-Shading-Language-Specification.pdf

Everythign in the Standard Library should be available. If something doesn't work, please file an issue.
Typos and compiler errors should appear in a (rather small) pop up.

To create a custom shader, just open the brush editor (tap a brush in the brush menu) and click the `</>` button:

![image](https://user-images.githubusercontent.com/6015639/164361195-9d5daac8-370a-456e-a50c-08d3e0b16883.png)

You'll see black text box appear where you can type.  Click the `</>`  again to close the editor and compile the function(s):

![image](https://user-images.githubusercontent.com/6015639/164362256-636515d6-179c-4f3a-ae50-0d5515f4b03a.png)


You can use the onscreen keyboard to type out a function, or hopefully you can use [Universal Control](https://support.apple.com/en-us/HT212757) on a nearby Mac to make things easier.

Here's a really simple example that just fills the buffers (defined by the dab sizes you draw) with a solid color. Your custom shader must be named `customBrushShader` and must
have the same arguments as this example, but you can also include other functions, structs, etc.  Continue reading about the Dab and DabMeta Types, which you'll need to understand

```
// this just fills the whole buffer with the brush color, skipping all other settings, etc

kernel void customBrushShader(
  constant Dab *dabArray [[ buffer(0) ]],
  constant DabMeta *dabMeta [[ buffer(1) ]],
  texture2d_array <half, access::read_write> canvas [[texture(0)]],
  texture2d_array <half, access::read> smudge [[texture(1)]],
  texture2d_array <half, access::read> lowerCanvas [[texture(2)]], // optional
  uint2 gid [[thread_position_in_grid]],
  uint tid [[thread_index_in_threadgroup]]) 
  {

    half4 color0 = half4(dabArray[0].color[0]);
    half4 color1 = half4(dabArray[0].color[1]);
    half4 color2 = half4(dabArray[0].color[2]);
    half pressure = 1.0 - dabArray[0].pressure;

    canvas.write(color0 * (pressure * 10.0), gid, 0);
    canvas.write(color1 * (pressure * 10.0), gid, 1);
    canvas.write(color2 * (pressure * 10.0), gid, 2);
    canvas.write(half4(1.0, pressure * 10.0,  1.0, 1.0), gid, 3);
}
```
