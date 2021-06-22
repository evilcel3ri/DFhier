![](text12.png)

# DFhier

Notes on DFIR tools.

## Plaso

List parsers:

`log2timeline.py --parsers list`

WINEVTX parse:

```
log2timeline.py --parsers="winevtx" --status_view output.dump image.E01
psort.py --output_time_zone 'UTC' -o l2tcsv -w output.csv output.dump
```

## Volatility

Get the process tree:

`vol3 -f target.mem windows.pstree.PsTree`

Get the netscan:

```
vol3 -f target.mem windows.netscan.NetScan | tee netscan.out
cat netscan.out | grep "ESTABLISHED"
```

Find malware:

```
vol3 -f target.mem windows.malfind.MalFind
vol3 -f target.mem windows.malfind.MalFind --dump
```

Print content of a key:

`vol3 -f target.mem windows.registry.printkey.PrintKey --key "KEY" | tee key.out`


## Misc

Use `python-evtx` to transform .evtx to .xml:

`for i in *.evtx;do python ../python-evtx/scripts/evtx_dump.py $i > $i.xml; done`

