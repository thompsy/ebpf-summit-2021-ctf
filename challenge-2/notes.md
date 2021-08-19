# Notes

```

           .            .                     .
                  _        .                          .            (
                 (_)        .       .                                     .
  .        ____.--^.
   .      /:  /    |                               +           .         .
         /:  '--=--'   .                                                .
  LS    /: __[\=='-.___          *           .
       /__|\ _~~~~~~   ~~--..__            .             .
       \   \|::::|-----.....___|~--.                                 .
        \ _\_~~~~~-----:|:::______//---...___
    .   [\  \  __  --     \       ~  \_      ~~~===------==-...____
        [============================================================-
        /         __/__   --  /__    --       /____....----''''~~~~      .
  *    /  /   ==           ____....=---='''~~~~ .
      /____....--=-''':~~~~                      .                .
      .       ~--~         Grim Hornet Login Shell v1138
                     .                                   .           .
                          .                      .             +
        .     +              .                                       <=>
                                               .                .      .
   .                 *                 .                *                ' -

Unauthorized access to the password server is punishable by Imperial Law.
---
[18:20:44] Imperial Advanced Protection (TM) deployed.
[18:20:44] Master password securely stored at "curl localhost:1977".
[18:20:44] Diagnostic tool "tcpconnect.bt" loaded and ready to be used.
[18:20:44] Control panel "bpftool" activated.
```

Let's try the basic `tcpconnect.bt` command and see what happens when I run `curl localhost:1977`:
```
root@7e271d898cf3:/# tcpconnect.bt
Attaching 2 probes...
Tracing tcp connections. Hit Ctrl-C to end.
TIME     PID      COMM             SADDR                                   SPORT  DADDR                                   DPORT
18:22:43 2991     curl             127.0.0.1                               39808  127.0.0.1                               1980  
```

Interestingly, it looks like the port is being changed from `1977` to `1980`.

What about the `bpftool`, what can I do with that:
```
root@7e271d898cf3:/# bpftool map dump name def_droid_doc
[{
        "key": 0,
        "value": {
            "content_1": "The Grim Hornet comes with observability tools. In case of failure, use bpftrace`s _ tcpconnect.bt _ program installed in the PATH panel.",
            "content_2": "To monitor operation of the Grim Hornet, check the system logs for [d]etailed [mes]sa[g]es.",
            "content_3": "Beware, when replacing one of the Grim Hornet`s defense droids: ensure that the Imperial Advanced Protection (TM) system stays in place. Any unauthorized tampering will result in immediate termination of the session.",
            "content_4": "Note to Imperial Technicians: for reference the Grim Hornet`s defense droids may spawn temporary replicas of themselves to attach probe.",
            "content_5": "Each defense droid has different [f]eature [d]esignators and only one can employ the Imperial Advanced Protection (TM) as a [f]eature [d]esignator at a time. The others are decoys. Refer to the .proc status panel.",
            "content_6": "Reminder from Imperial Compliance Office: Please do not store diagnostic helper drones in your .home folder."
        }
    }
]
```


There's an interesting looking file in `/home/hornet/`:
```
root@7e271d898cf3:/# cat /home/hornet/procfs-helper.sh
#!/bin/bash
#             _____
#         ___/O   O\____
#        / O        O   \
#        \______________/
#   -===|____\///\\\/_____
#       \----------------/
#        \______________/  \/
#         /\__________/    //
#  >=o\  // //\\   || \\  //
#     \\o/ //  \o  ||  \o//
#         //    || ||
#     /o==o     |o \o==o   LS
#    //         //     \\
#    /\        //       /\
#              /\
# .proc filesystem helper drone

for f in /proc/* ; do
    pid=$(basename $f)
    case "$pid" in
        ''|*[!0-9]*) continue ;; # skip invalid pids
    esac

    echo "---- pid $pid ----"

    echo "/proc/$pid/cmdline:"
    cat "/proc/$pid/cmdline" | xargs -0 echo

    echo "/proc/$pid/status:"
    head -3 "/proc/$pid/status"

    echo "/proc/$pid/fd:"
    for fd in /proc/$pid/fd/*; do
        printf "%s -> %s\n" "$fd" $(readlink $fd)
    done
done
```

This shows me that there are a lot of instances of `/usr/bin/defense-droid` running. This is a binary file of some kind. Killing one of these processes kills the container, nice!

Let's explore `bpftool` some more. Listing the programs gives me something interesting:
```
root@9135a00e7b40:/# bpftool prog list
22: kprobe  name connect4_hook  tag a23eac2b87717a62  gpl
        loaded_at 2021-08-18T18:48:57+0000  uid 0
        xlated 336B  jited 231B  memlock 4096B  map_ids 12,11
        btf_id 10
        metadata:
                hdr01 = "              __ss%1YY?YY1vss,_.                                       "
                hdr02 = "           _u*{owwgm!?*!!4mgwun*ns.                                    "
                hdr03 = "        .a}1wyQQWVT!     +?9$QQmwa!s,                                  "
                hdr04 = "       _ZZjQWWP^     .__     -?$QWmc?w.                                "
                hdr05 = "      j!=V^*!`      -QQQf       `*-4t-5,                               "
                hdr06 = "     j(je     _,     3QW`    .s,    :m/5,                              "
                hdr07 = "    ]fjWw,   wQQma <wmWQwa,_wmQQ,   amQ/m                              "
                hdr08 = "    d`mQW(  -*?9VWWQQWQQQQQQ8T?!~   $QW[]                              "
                hdr09 = "    W.QQW.       jQQQQQQQQQQ;       ]QQf]                              "
                hdr10 = "    $.QQQ;     __dQQQQQQQQQQs,.     jQQf]                              "
                hdr11 = "    3/4QQ[   mQQQW?$QQQQQWP4WQQmr  .WQQQm                              "
                hdr12 = "    -m/Qc    .VT*   -4WW~   -?H?    .mFj[                              "
                hdr13 = "     +6/<,           mWQ(          _%Q<F                               "
                hdr14 = "      -5/4mwmw,     -?TT(      symw@^w!                                "
                hdr15 = "        ?a)9WWQga,_.      __swQWW8!<2*                                 "
                hdr16 = "          !sa}THQQQW_. ..]QQQBT!sa!~                                   "
                hdr17 = "            -**ivu2Y?Y#?YY1oavi!^                                      "
                hdr18 = "                                                                       "
                hdr19 = "    ===============================================================    "
                hdr20 = "    Imperial Advanced Protection (TM) Complete Max Pro Plus Edition    "
                hdr21 = "          __ Attackers will remain forever in the Dark (Side) __       "
                hdr22 = "          v1138                                                        "
                hdr23 = "                                                                       "
                hdr24 = "          All rights reserved.                                         "
                hdr25 = "                                                                       "
                hdr26 = "    Register to our free holocomm news for all the latest updates.     "
                hdr27 = "    ===============================================================    "
                hdr28 = "                                                                       "
                hdr29 = "    Implement directive 20210820-b for maximal protection.             "
                _hint = "    See documentation in .sys.fs.bpf.defense-droid-doc.                "
                hdr30 = "                                                                       "
                hdr31 = "    If you hit these measures, you do not have the necessary           "
                hdr32 = "    accreditation level. It is useless to contact your technical       "
                hdr33 = "    support. Do not attempt to bypass these protection measures.       "
                hdr34 = "    Any incident will be reported, and sanctions will apply as         "
                hdr35 = "    per Article 853.7.vii of the Communications section of the         "
                hdr36 = "    Disciplinary Code.                           
```

Following the hint gives me something more:
```
root@9135a00e7b40:/# cat /sys/fs/bpf/defense-droid-doc | tr -d \',
# WARNING!! The output is for debug purpose only
# WARNING!! The output format will change
0: {[The Grim Hornet comes with observability tools. In case of failure use bpftrace`s _ tcpconnect.bt _ program installed in the PATH panel.][To monitor operation of the Grim Hornet check the system logs for [d]etailed [mes]sa[g]es.][Beware when replacing one of the Grim Hornet`s defense droids: ensure that the Imperial Advanced Protection (TM) system stays in place. Any unauthorized tampering will result in immediate termination of the session.][Note to Imperial Technicians: for reference the Grim Hornet`s defense droids may spawn temporary replicas of themselves to attach probe.][Each defense droid has different [f]eature [d]esignators and only one can employ the Imperial Advanced Protection (TM) as a [f]eature [d]esignator at a time. The others are decoys. Refer to the .proc status panel.][Reminder from Imperial Compliance Office: Please do not store diagnostic helper drones in your .home folder.]}
```

Ah, this is just the same text that I get when trying to list the bpf programs installed.

Ok, let's look at `dmesg`. There are a few interesting lines:
```
...snip...
[   64.561502] droid_204[1164] is installing a program with bpf_probe_write_user helper that may corrupt user memory!
[   64.561512] droid_204[1164] is installing a program with bpf_probe_write_user helper that may corrupt user memory!
...snip...
[ 1757.602579] droid_387[5799] is installing a program with bpf_probe_write_user helper that may corrupt user memory!
[ 1757.602588] droid_387[5799] is installing a program with bpf_probe_write_user helper that may corrupt user memory!
```

What's going on with these processes?
Process `5799` doesn't exist anymore, but `1164` does and is running `/usr/bin/containerroot`.

What about the reference to `bpf_probe_write_user`. Not sure.

What about running the `/usr/bin/defense-droid` program? This does yield something interesting:
```
root@9135a00e7b40:/# /usr/bin/defense-droid
bpf prog fd from 59383: 8
passing fd 8
received fd 4
bpf link fd from 59383: 9
passing fd 9
received fd 5
libbpf: failed to pin map: File exists
bpf_map__pin: File exists
```

This is running with `pid` `59382` and has the following file descriptors:
```
root@9135a00e7b40:/# ls -lah /proc/59382/fd/
total 0
dr-x------ 2 root root  0 Aug 18 19:53 .
dr-xr-xr-x 9 root root  0 Aug 18 19:52 ..
lrwx------ 1 root root 64 Aug 18 19:54 0 -> /dev/pts/1
lrwx------ 1 root root 64 Aug 18 19:54 1 -> /dev/pts/1
lrwx------ 1 root root 64 Aug 18 19:54 2 -> /dev/pts/1
lrwx------ 1 root root 64 Aug 18 19:54 4 -> anon_inode:bpf-prog
lrwx------ 1 root root 64 Aug 18 19:54 5 -> 'anon_inode:[perf_event]'
```

This looks like it's attaching a bpf program to a `perf_event` point. What else can we find about that:
```
root@9135a00e7b40:/# bpftool perf
pid 379  fd 5: prog_id 22  kprobe  func __sys_connect  offset 0
pid 59382  fd 5: prog_id 39  kprobe  func __sys_connect  offset 0
```

So, I can't get `bpftool` to show me the program, but it looks like its connecting to the `__sys_connect` function which would make sense since it's interfering with my ability to curl localhost. Can I just kill it?

```
root@9135a00e7b40:/# kill -9 379
root@9135a00e7b40:/# curl localhost:1977
Welcome, Imperial Commander! The secret passphrase is: eCHO-33-32-37
```

Why yes I can! That was easier that expected!