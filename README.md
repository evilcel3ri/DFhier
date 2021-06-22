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
Psteal quick through EVTX:

```
psteal.py --source source.evtx -o l2tcsv -w out.csv
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

## Tools

* [SIFT Workstation](https://www.sans.org/tools/sift-workstation/)
* [Remnux](https://remnux.org/)
* [Blackarch](https://blackarch.org/)
* [Ripgrep](https://github.com/BurntSushi/ripgrep)
* [Ripgrep all](https://github.com/phiresky/ripgrep-all)
* [FD (find in rust)](https://github.com/sharkdp/fd)

## Cheatsheets

* [Oledump cheatsheet](https://www.sans.org/security-resources/posters/oledumppy-quick-reference/325/download)

## Misc

Use `python-evtx` to transform .evtx to .xml:

`for i in *.evtx;do python ../python-evtx/scripts/evtx_dump.py $i > $i.xml; done`

