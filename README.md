# ida-headless
IDA (sort of) headless

## Idea

Embed RPyc in IDA to expose IDA's API externally, by a background thread that runs the TCP server. Also in iPython this
provides autocomplete.

Props to https://github.com/vrtadmin/FIRST-plugin-ida/blob/master/first_plugin_ida/first.py#L87
for the workaround on the threading issue, for IDA Pro >= 7.2

## Quick start
```
>>> import rpyc
>>> c = rpyc.connect("ida.rpyc.server", 18812)
#
# IDA namespace will be in `c.root`
#
>>> c.root.idaapi.get_root_filename()
'ntoskrnl.exe'
>>> hex( c.root.idc.here() )
0x140088194
>>> c.root.idaapi.jumpto( 0x1400881EE )
True
```

For more facility, you can alias it:
```
>>> idc = c.root.idc
```

Then, it becomes super readable
```
>>> idc.jumpto( idc.get_name_ea_simple("DriverEntry") )
True
>>> idc.set_cmt( idc.here(), "@hugsy was here", 1)
True
```

For generator objects, you now need to use the wrapper `c.root.iterate()`.

Example:
```
>>> idc = c.root.idc
>>> idautils = c.root.idautils
>>> for ea in c.root.iterate( idautils.Functions() ):
...    print( idc.get_func_name(ea) )
```

Blame HexRays for making their API more confusing at every release.

## Links
- https://www.hex-rays.com/products/ida/support/ida74_idapython_no_bc695_porting_guide.shtml
- Same but for Binary Ninja -> https://github.com/hugsy/binja-headless

## Demo

[![img](https://img.youtube.com/vi/obX2GreSsFU/0.jpg)](https://youtu.be/obX2GreSsFU)
