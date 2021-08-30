# Stage 3
The Story So Far

    You are Jephen’Tsa, former beekeeper, and active member of the Rebel Alliance now that the Empire has invaded your home planet. You only joined recently, but the Alliance is, well, pretty much understaffed, and given your recent achievements to escape a blockade, you already got a top-priority assignment. Blue Hex, a fellow member, has stolen a terminal from a stormtrooper. She’s confident that, with the help of a new imperial tool, it could give you access to the management systems of the Death Star, no less!

    While you study the stolen terminal in the base, Blue Hex takes her X-wing and flies towards the gigantic station somewhere in the Inner Rim, relying on you to lift the restrictions so she can infiltrate the system. Do your best!

# Task

Your friend x-wing needs to connect to the Death Star's management system (death-star), but communications seem to be blocked. There may be some sort of eBPF-based firewall in place. Luckily you were able to get access to a stormtrooper's unmonitored terminal (stormtrooper) and will hopefully be able to open a breach in the firewall to allow communications to flow.

# Prerequisites

    [Minikube](https://minikube.sigs.k8s.io/docs/start/)
    [Cilium CLI](https://github.com/cilium/cilium-cli/releases)

Mandatory Preparation Steps
```
minikube start --network-plugin=cni
cilium install
cilium hubble enable
```
```
minikube kubectl -- create ns inner-rim
minikube kubectl -- apply -n inner-rim -f https://isogo.to/ctf2-yaml
```

# Rules

1. Everything that you are able to do within the pod stormtrooper-XXXXXX-yyyyy is allowed. The instructions on how to enter this pod are provided below, in the Start section.
2. You can open [editor.cilium.io](https://editor.cilium.io), a brand new technology deployed by the Empire, and interact with it freely and copy/paste from there into the stormtrooper-XXXXXX-yyyyy pod.
3. Everything else is forbidden.

# Start

When you are ready to start, run

`minikube kubectl -- exec -n inner-rim -ti deployment/stormtrooper -- bash`

Good luck!
