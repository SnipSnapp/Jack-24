
Docker Exploitation Framework D.E.F.

Rohit Pitke & Emmanuel Law

# Imagine you've pwnd the container.

There are problems with lat movement or whatever you want ot do.

## Challenges
1. Missing libs/CLI
2. Running a tool is tedious
3. Lots of repetition

Lots of what oyu need is just not present, so it's very difficult to do the recon to see what you can do.  You're stuck.  Any attempt to install these tools is risky, you can get caught installing what you need.

This framework is a post exploit framework for containers (kubernetes, docker, etc.)

That's the challenge. 

# The approach

- Modular client-server architecture, with plug and play plugins.
- Dependency resolution
	- everything is a native bin
- Framework Abstraction
	- Focus on writing plugin logic
	- Importing tools, communication, diff. architecture.
- User Friendly
	- Simple UI
	- Auto Clean-up
Everything is native.  From a C2 you should push to comp container, and use that as a point into the network. 
So you have a compromised container. You have the plugins, and the software you install first, you call this an agent. So these communicate over IPC which then works over a C2. 
If egress is heavily monitored you fallback to DNS.
As an operator you just work on the C2.

# Demo

## Secret Scavenger
- Union FileSystem
	- Container images are based on this system, the changes you write are built layer on layer.  So you have BASE then install ssh, then you add a secret, etc. 
	- When you accidentally commit/write a sensitive file, some folks try to delete something.  But when you delete something it doesn't actually delete it. It makes a whiteout layer that then just hides that it's there. It's still there. 
		- Nothing is ever removed, only masked.  
- So you can point this at the registry and do a deep layer inspection.  It knows the whiteout, and checks for files that are being deleted, and what those files are.  If you're deleting something from an image it may be sensitive. 
- So from the C2 UI, on the compromised container, you can simple run "secret-scavenger"
- So you're in the secret scaveger, running on X agent.  Then you tell it "Go to this repo, and do an inspection, go to X layer or max number of layers."
- So it pulls each laer off of the image, then finds deleted files within the layers. So you can find SSH file keys.   You'd be surprised what you can find on the docker registry.

# Docker.Sock privilege escalation

People mount a docker socket within the container itself.
A Unix socket is mounted by default, but mounting it can be a part of a legit setup, often in a CICD environment.
Docker socket is the interface to send commands to the docker engine.
Mounting this in a docker container you can compromise it. 

## Exploit
This tries to find a docker socket in the container, then tries to send a command to make a new container in privileged mode, then have the privilege callback through the C2 to the new container it spins up.

PrivEsc_Docker -> run

It does enum, Starts a new container, copy image and goes ahead and calls back.  

# Uber Shell

This is the guy's favorite. 
When you find a container is stripped down, you don't have anything no whoami/ifconfig/nmap/etc.  What this does is behind the scenes, when you type a command, it tries to strip the binaries from the container and run it. 
If the binary isn't there it streams it to try to run it.  Now as an operator you don't care because you're just running everything in memory.

It's the guy's favorite because it's a QOL great thing


# K8 Audit 

WB Kubernetes? What if you want to hurt it?
Kubernetes actually has a larger attack surface.   The possibilities are everywhere with K8.

Important part is if you compromise the port you can run on that.  Non-native binnaries, the moment you try to install that, there's a good chance it'll cause alarms.  So for kubernetes, you massage the kubernet into the native binary, put it into the framework, and deploy to the compromised thing for recon purposes.

So if we have a compromised pod, You as a red teamer install the agent there, and everything you do is remote from the C2.
So for this system you run kubeaudit.  Then you do run all.

Then this is useful as recon activity.  This is the first step. You remotely run the kube audit and it goes back to you. So you can grab the service token and grab it through this.  The privileged pods are the same as privileged containers.  If this pod is compromised, you can use it like what docker saw.  And get access to the host file system.  So running it, getting the privileged ports binary, it goes and tries to read the mount tokens, and get a list of everything.  So this could be the next menu you try.  This is like a recon activity that helps you. 

The whole goal is you can scale your red teaming activity by remaining as stealthy as you can. Download the tools you want, and remotely manage all of these systems.  In recon/privesc/etc.


::::Source::::

dockerexploitationframework.github.io


At the moment they all write to file system but writing to memory is coming soon.

There're some commercial tool sto monitor containers, that can sometimes find it, but is not sure they're effective at all.

The docker sock or privileged ports, these are denied by default. The read only file system is hard to get into production apps. So Kubernetes the first hop to consider, is to look there. 

