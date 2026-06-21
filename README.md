# eBPF Summit 2021 — Capture The Flag

My solutions and notes for the Capture The Flag challenges from the
[eBPF Summit 2021](https://ebpf.io/summit-2021/), run by Isovalent.

This was my first hands-on exposure to eBPF — a fun way to learn how BPF
programs attach to the networking datapath and manipulate packets in flight.
The challenges are themed as a *Star Wars*-style story in which you bypass an
Imperial blockade using eBPF instead of touching the (off-limits) iptables rules.

## Challenges

| # | Topic | Status |
|---|-------|--------|
| [1 — Bypass the blockade](challenge-1/) | TC egress/ingress `bpf.c`: rewrite UDP destination/source addresses and recompute IP checksums to tunnel past an iptables jamming rule | ✅ Solved, with working code |
| [2 — Grim Hornet login shell](challenge-2/) | Write-up and notes from working the challenge | 📝 Notes only |
| [3 — Breach the Death Star firewall](challenge-3/) | Cilium/Kubernetes-based eBPF firewall challenge | 🚧 Attempted |

## Challenge 1 highlights

The core of the solution is a TC eBPF program ([`challenge-1/bpf.c`](challenge-1/bpf.c))
that, on egress, rewrites packets destined for `100.202.1.3` to actually go to
`100.202.1.1` — slipping past an iptables filter — and rewrites the source address
back on ingress so the exchange looks transparent. Both directions fix up the IP
checksum with `bpf_csum_diff` / `bpf_l3_csum_replace`.

```c
// Rewrite the destination address and recompute the checksum
__u32 new_dest = 0x0101ca64; /* 100.202.1.1 */
sum = bpf_csum_diff(&ip->daddr, 4, &new_dest, 4, 0);
bpf_skb_store_bytes(skb, l3_off + offsetof(struct iphdr, daddr), &new_dest, 4, 0);
bpf_l3_csum_replace(skb, l3_off + offsetof(struct iphdr, check), 0, sum, 0);
```

See each challenge's own `README.md` for the full task description and setup steps.
