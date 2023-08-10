---
sort: 10
---

# GS Special Effects and Optimizations

The GS has an absurdly high fillrate of 1.2 gigapixels/second when drawing textured polygons. This advantage is further compounded by free (!) alpha blending and depth buffering. However, the GS only supports single-pass texturing and is entirely fixed-unit. The combination of these factors led developers to find creative ways to make their games look good...

```note
many of these effects don't have an official name, so they are made up.
```

## Fast Screen Draw

Drawing a single sprite that covers the entire screen is relatively slow. First, this causes many DRAM page breaks per scanline drawn. Second, for textured sprites, the texture cache also has to be reloaded many times.

Instead, one can split a screen draw into many 64x32 sprites. This greatly improves cache locality, based upon the GS's nonlinear mmemory.

The fast screen draw is often used as a building block for other advanced techniques.

## Double Half Clear
Because the framebuffer and depth buffer both occupy the same memory space, framebuffer clears can be optimized by placing the start of the depth buffer halfway down the screen and halving the height of the clear draw. As depth buffering is free, this will result in the clear being around twice as fast.

## Interleaved Clear
Similar to the double half clear in principle, but takes advantage of color and depth formats being swizzled differently. The depth buffer base is set to the framebuffer base (ZBP=FBP), and instead of the clear being split into 64x32 sprites, it is split into 32x32 sprites instead. This results in the clear being interleaved, as the color clear touches the left side of a 64x32 block and the depth clear touches the right side. Because the framebuffer and depth buffer share the same cache, this should be faster than a double half clear.

Powerdrome uses a crazy variant of this, where the framebuffer is given a Z format. Due to quirks in the GS rendering process, this causes the depth clear to draw as if it had a color format. This doesn't really speed anything up, as all that changes is the order of the interleave, but it does make emudevs cry a lot.

## VIS Clear
Absolutely insane clear used in the VIS Games engine. The framebuffer width (FBW) is set to 1 (64 pixels for a 32-bit texture), then a very thin and tall sprite is drawn. In normal cases, the sprite is 64x2048. The clear is essentially if one took all the pages from the framebuffer, stacked them vertically, then drew a sprite large enough to clear the stack. This should avoid the horizontal page breaks that other methods cause, but it is unknown how fast this clear is.

Superman Returns, another VIS game, uses a somehow wilder variant, where the clear sprite is 32x4096. The sprite is so large, it wraps around from the bottom of VRAM to the top.

## Recursive Drawing
Draws where the texture buffer and framebuffer overlap, usually done with TBP = FBP. Because of the texture cache, texels do not get overwritten in VRAM until after they are read. Recursive drawing can be used to apply special effects without having to create a temporary buffer in memory.

## Channel Shuffle
A sophisticated technique made to overcome the GS's lack of shader units. At a high level, the most basic channel shuffle effect uses a texture's 32-bit RGBA channels as 8-bit palette indices to apply an effect on the texture, such as gamma correction or a brightness effect.

To perform the channel shuffle, draw a series of 8x2 sprites that read the 32-bit texture as 8-bit - normally this is done with recursive drawing, but temporary buffers can also be used. The size of the sprites is important, as 32-bit and 8-bit textures are swizzled differently. The sprites will read from a palette which applies the effect, then FBMASK is used to restrict the draw to a single channel. This must be repeated for every channel the effect is to be applied to, with a total of four shuffles for all four channels.

This effect is extremely difficult to emulate with a modern GPU. A 640x224 framebuffer requires 8960 8x2 sprites for a single shuffle. The 32-bit texture must be deswizzled, converted to 8-bit, and reconverted back to 32-bit for each sprite. Since this is usually done with recursive drawing, texture memory and framebuffer memory on the host GPU also needs to be synchronized. Finally, modern GPUs mask on the byte level, but games can mask on the bit level, which requires a slow fragment shader to process the effect correctly if the game doesn't fully mask on the byte level. All this results in a channel shuffle being more expensive than even every other draw call in the game. Other variants of channel shuffle also exist that involve reading the depth buffer, and some shuffle effects are not well-understood.
