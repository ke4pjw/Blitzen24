
#Memory Structure for pixels and DMX

```
PIXELCONFIG[0] := 1 ' Pin + 1, that way 0 is NULL
PIXELCONFIG[1] := %%0123 ' Pixel Color order. In this case BB_RR_GG_WW
PIXELCONFIG[2] := 800 'Pixel holdoff time
PIXELCONFIG[3] := @pixelbuffer
PIXELCONFIG[4] := 0 ' Pixelcount in this pin
.
.
.

E131CONFIG[0] := 1 ' Pin + 1, this is the many to one key to the PIXELCONFIG
E131CONFIG[1] := 2 ' E1.31 Universe
E131CONFIG[2] := 100 ' Pixels in this universe
E131CONFIG[3] := 0  '  @pixelbuffer for this universe
.
.
.
' Reserve the last 4 E131CONFIG slots for DMX
  Repeat 4
    E131CONFIG[ei+3] := pixeloffset + @pixelbuffer
    pixeloffset := pixeloffset + 510
    ei := ei + 4

pub pixelcnt(pingroup) : pixelct | index
  pingroup := pingroup - 1 'This is so that we can pass what is configured in the data structure in pixelconfig.
  index := pingroup * 5
  pixelct := 0
  repeat 8
    if PIXELCONFIG[index + 4] >=  pixelct
      pixelct := PIXELCONFIG[index + 4]
    index := index + 5
  return pixelct    
```