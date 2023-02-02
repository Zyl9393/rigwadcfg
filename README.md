## rigwadcfg

This tool can be run before HLCSG when compiling a Goldsource `.map` file into a `.bsp` to rewrite the file `wad.cfg` to contain exactly one configuration called `auto` which contains exactly the WAD files listed in the specified `.map` file's `wad`-string.

### Why?

Due to engine limitations, the compile tools truncate all key values to 255 bytes when reading the `.map` file, which includes the `wad`-string: a semicolon-separated list of WAD file paths which HLCSG refers to to find all used textures. This means that if you use multiple WAD files, you will be likely to run into errors about missing WAD files.

The most feasible solution to this problem is the `wad.cfg` file, which can contain multiple named configurations each of which can specify any amount of WAD files. HLCSG can be instructed to choose such a configuration using its `-wadconfig` command line argument, causing the `wad`-string in the `.map` to be ignored.

```wadcfg
hl {
D:\Steam\steamapps\common\Half-Life\valve\halflife.wad
D:\Steam\steamapps\common\Half-Life\valve\decals.wad
}

hl3 {
D:\Steam\steamapps\common\Half-Life\ricochet\ricochet.wad
D:\Steam\steamapps\common\Half-Life\valve\decals.wad
}
```
<sup>Example `wad.cfg`.</sup>

However, Hammer itself painlessly writes long `wad`-strings, and HLCSG can be instructed to omit unused WAD files using `-wadautodetect` and react to `-wadinclude` even when `wad.cfg` is used, so for some people, such as myself, maintaining `wad.cfg` manually is unnecessary. Hence this tool.

### Usage

```bat
"%TOOLS_PATH%\rigwadcfg.exe" "%MAP_PATH%"
"%TOOLS_PATH%\hlcsg_x64.exe" "%MAP_PATH%" -wadconfig auto -wadautodetect
@REM Invoke other compile tools...
```

It is important that `rigwadcfg.exe` resides next to the other compile tools in the same directory, because we mimic the behavior of HLCSG to look for `wad.cfg` in the same folder as itself.

If an existing `wad.cfg` which was not automatically generated is found, a backup called `wad.cfg.001.backup` or such will be created.

### Drawbacks

Care needs to be taken when compiling multiple maps with the same set of compile tools simultaneously, since the same `wad.cfg` will be contested for writing. If you do not do this, it is not an issue.
