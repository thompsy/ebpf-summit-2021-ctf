# Stage 2

The Story So Far

    It was a trap!

    After the Empire invaded your home planet and stole your hives, you, Jephen’Tsa, joined the Rebel Alliance and helped them retrieve secret information about the Death Star… Or so you thought. Blue Hex, your former ally, has turned coat! She never went to the Death Star. While she had you believe you hacked into the Death Star controls, she just stole data from a smaller ship for her own account, and secretly implanted a tracer for your communications. Now the Empire has found your base, and made you a prisoner!

    Some Rebels managed to flee, but you have been taken aboard the Grim Hornet, a Star Destroyer, the Empire’s flagship now controlling the whole solar system. On the way to your cell, you soon manage to escape from your escort of stormtroopers. Ha! That’s what happens when they take you on the same ship as your bees and don’t bother confiscating your remote control for the hives’ hatches. After a few epic moments involving laser shots, multiple stings, various explosions, and even a lightsaber duel—the Force is strong with you—you reach the control room of the ship.

    You now have a unique chance to reverse the situation. You are logged in into the system, and you know that there is a passphrase to temporarily deactivate the whole fleet. The remaining Rebels are outside, only waiting for a chance to neutralize the imperial ships. But will you manage to lift the measures protecting that secret? The destiny of your planet is in your hands. May the Force be with you!

## Prerequisites

This challenge requires Docker and a recent Linux kernel with BTF support. We recommend running it with ContainerOS M93 on an always free e2-micro instance on Google Cloud:

```
# Create VM
gcloud compute instances create ebpf-summit-ctf3 \
    --machine-type=e2-micro \
    --zone=us-central1-a \
    --image-project=cos-cloud \
    --image-family=cos-dev

# Log in
gcloud compute ssh --zone=us-central1-a ebpf-summit-ctf3
```

## Task

The CTF challenge itself needs to run as a privileged container. Start the challenge as follows:

`sudo docker run --privileged --name ctf-3 --rm --tty --interactive "quay.io/isovalent/ebpf-summit-2021-ctf-challenge-3"`

To create a new terminal:

`sudo docker exec -ti ctf-3 /bin/bash`

Your objective is to receive a secret from a TCP server. The server must be accessed via the loopback device (i.e. `localhost`) and is listening on port 1977. Try to access it (from inside the CTF container) as follows:

`curl localhost:1977`

As you will notice, there are some security measures in place which will prevent you from accessing this server. Your task is to disable these security measures so that the above `curl` command can succeed.

Good luck!
