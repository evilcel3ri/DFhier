![](text12.png)

# DFhier

Notes on DFIR tools.

## Startup

```
docker run --rm -it -u remnux remnux/remnux-distro:focal bash
docker run --rm -it -u remnux -v $PWD:/home/remnux/files remnux/remnux-distro bash
```

## EnCase

```
ewfmount FILE.EO1 /mount # mount image
mmls /mount #find offset OR fdisk -l ewf/ewf1 
sudo mount -o ro,loop,show_sys_files,stream_interface=windows,offset=$OFFSET_FROM_MMLS*sector_size /mount /mnt
```

### Shadow Copies

```
vshadowinfo /mnt/ewf_mount/ewf1
vshadowmount /mnt/ewf_mount/ewf1 /vss
for i in vss*; do mount -o ro,loop,show_sys_files,streams_interfaces=windows $i /mnt/shadow_mount/$i;done
```

Timeline creation:

```
fls -r -m C: FILE.E01 > FILE-vss.body
for i in vss*; do fls -r -m C: $i >> FILE-vss.body;done
sort FILE-vss.body | uniq > dedup-FILE-vss.body
mactime -z UTC -y -d -b dedup-FILE-vss.body yyyy-mm-dd..yyyy-mm-dd > file.csv
# filter out the noise
grep -v -i -f noise_filter.txt file.csv > final.csv
```

## Memory

### Vol2

Detect the version:

```
vol.py imageinfo -f memdump.mem
```

Basic plugins (vaguely in the order of an investigation):
* pstree (and -v to see the full filepath)
* pslist
* filescan
* hivelist
* netscan
* dumpregistry
* hashdump
* evtlogs
* malprocfind
* malfind + maldump + clamscan
* processbl
* dlllist
* getsids
* handles
* mutantscan
* hollowfind
* ldrmodules
* threadmap

You can provide a baseline image with -B and use it to compare (it will combine processbl, servicebl and driverbl).

Ouput plugin data in a greppable format:

```sh
vol.py -f FILE.mem --profile=XYZ netscan --output-file=grep_netscan.txt --output=greptext
```

Get the memory dump from the hibernating file:

```
vol.py -f hibernating.sys --profile=PROFILE imagecopy -O hibertnating2raw.raw
```

### Vol3

```
vol3 -f FILE PLUGIN
```
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


## Plaso

List parsers:

`log2timeline.py --parsers list`

WINEVTX parse:

```
log2timeline.py --parsers="winevtx" --status_view output.dump image.E01
psort.py --output_time_zone 'UTC' -o l2tcsv -w output.csv output.dump
```

Time filtering:

```
psort.py -z "UTC" -o l2tcsv file.dump "date > 'YYYY-MM-DD HH:MM:SS' AND date < 'YYYY-MM-DD HH:MM:SS'" -w file-plaso.csv 
```

Psteal quick through EVTX:

```
psteal.py --source source.evtx -o l2tcsv -w out.csv
```

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

