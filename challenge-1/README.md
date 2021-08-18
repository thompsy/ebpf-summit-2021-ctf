# Stage 1

The Story So Far:

    You have a bad feeling about this.

    You, Jephen’Tsa, have always kept away from politics, and you live a quiet life on the planet Berpaffyl, in the Kloudna system. You’re a beekeeper, and extracting honey from the giant bees living on the planet does not leave you much time to think about conflicts raging at the other end of the quadrant.

    But politics caught up with you. The Empire has taken an interest in the planet, imposed a blockade, and seized various assets, including your hives. Your dear, cherished hives and bees, now aboard a Star Destroyer! They said you’d get them back. Of course, they would. But the bureaucrats from the Empire are not renowned for keeping their word, and you don’t believe them. There must be something you can do?

    From a friend of a friend, a Mon Calamari going under the name Blue Hex that you met on a speedbike ride, you have heard that a moon in a neighbor system might be hosting more friends—the Rebel Alliance. After a few days of reflection you contact her again to ask if she knows how to pass the blockade to reach them. There is a way, she says, to bypass the jamming signals and eavesdrop the passphrase required for passing the checkpoint. You board the Yellow Stripe, your small aircraft, you take off and you head towards the imperial cruisers…

## Prerequisites

This challenge requires Docker and a recent Linux kernel with eBPF and WireGuard support enabled (5.6+, although Ubuntu 20.04 with kernel 5.4 and backported Wireguard support is known to work).

We recommend running it with Fedora CoreOS on an always free e2-micro instance on Google Cloud:
```
VM_NAME=ebpf-summit-ctf1

# Create VM
gcloud compute instances create $VM_NAME \
    --machine-type=e2-micro \
    --zone=us-central1-a \
    --image-project=fedora-coreos-cloud \
    --image-family=fedora-coreos-stable

# Fix docker permissions
gcloud compute ssh --zone=us-central1-a core@$VM_NAME -- sudo setfacl --modify user:core:rw /var/run/docker.sock

# Log in
gcloud compute ssh --zone=us-central1-a core@$VM_NAME
```
## Task

Your objective is to receive a secret from a UDP server. The server is running in the berpaffyl network namespace and is accessible via a WireGuard tunnel with the `100.202.1.1` IP address.

To send a request to that IP, you can use `echo | netcat -u 100.202.1.1 1138`.

Unfortunately, a jamming signal was installed. You can see it with `iptables-save -c`. You are not allowed to remove it but you are allowed to bypass it.

Your ally mentioned the existence of a `/bpf` directory, which you might find useful.

The CTF challenge itself needs to run as a privileged container. Start the challenge as follows:

`sudo docker run --privileged --name ctf-1 --rm --tty --interactive "quay.io/isovalent/ebpf-summit-2021-ctf-challenge-1"`

To create a new terminal:

`sudo docker exec -ti ctf-1 /bin/bash`

## Rules

    Do not add or remove any iptables rules. The goal of the challenge is to solve it using eBPF only.

Good luck!


## Tips

  - To build `bpf.c` run `make`
  - To (re-)attach the built BPF program, invoke `make run`
  - The output of `bpf_printk` is visible in `cat /sys/kernel/debug/tracing/trace_pipe`
  - Take a look at what `bpf_helpers_defs.h` has to offer