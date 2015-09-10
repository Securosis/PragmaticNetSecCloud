Title: Pragmatic Network Security for Cloud and Hybrid Networks
by Rich Mogull

For a few decades now we've refined our approach to network security. Find the boxes, find the wires connecting those boxes, drop a few security boxes in between in the right spots, and move on. Sure, we continue to advance the state of the art in just exactly *what* those security boxes do, and we constantly improve how we design the networks and plug everything together, but, overall, the pace of change is incremental. *How you think* about network security doesn't change, just some of the particulars.

Until you move to the cloud.

While many fundamentals still apply, cloud computing releases us from the physical limitations of those boxes and wires by fully abstracting the network from the underlying resources. We move into entirely virtual networks, controlled by software and APIs, where different rules apply. Things may look the same on the surface, but dig a little deeper and you realize that network security for cloud computing requires a different mindset, different tools, and a new set of fundamentals.

Many of which change every time you switch cloud providers.

# The challenge of cloud computing and network security

Cloud networks don't run magically on pixie dust, rainbows, and unicorns; they still rely on the same old physical networks we are used to. The key difference is that those using the cloud never access the "real" network, but instead work inside a virtual construct, since that's the fundamental nature of clouds.

By default, cloud computing uses *virtual networks*. The network your servers and resources see is abstracted from the underlying physical resources. When you server gets IP address 10.0.0.12, that isn't really that IP address on the routing hardware, it's a virtual IP address on the virtual network. Everything is handled in software, and most of these virtual networks are *Software Defined Networks* (SDN). We will go over SDNs in more depth in the next section.

These networks, which vary across cloud providers, are fundamentally different than traditional networks in a few key ways:

* *Virtual networks don't provide the same visibility as physical networks*, since packets don't move around the same way. Thus we can't put a wire on the network and sniff all the traffic, since not all of it will even go across the wire, and what does is very likely wrapped and encrypted in some way.
* *Cloud networks are managed via Application Programming Interfaces*, not by logging in and provisioning hardware like normal. A developer has the power to stand up an entire class B network in minutes with a few API calls, or completely destroy an entire subnet. Or add a network interface to a server and bridge to an entirely different subnet on a different cloud account.
* *Cloud networks change faster than physical networks, and constantly*. It isn't unusual for a cloud application to launch and destroy dozens of servers in under an hour; at rates greater than traditional security and network tools can keep track of things. Or even build and destroy entire networks just for testing.
* *Cloud networks look like traditional networks, but aren't*. Cloud providers tend to give you things that look like routing tables and firewalls, that don't work *exactly* like your normal routing tables and firewalls. It's important to know the differences.

Don't worry, the differences make a lot of sense once you start digging in, and most of them provide *better* security that's *more accessible* than on a physical network, as long as you know how to manage it.

##The role of hybrid networks

A hybrid network bridges your existing network into your cloud provider. If, for example, you want to connect a cloud application to your existing database, you will connect your physical network to the virtual network in your cloud. 

Hybrid networks are extremely common, especially as traditional enterprises begin migrating to cloud computing and need to mix and match resources instead of building everything from scratch. One popular example is to set up some big data analytics in your cloud provider, where you only pay for the processing and storage time, and don't have to buy a bunch of servers you will only use once a quarter.

But hybrid networks complicate management both in your data center and in the cloud. Since both sides use a different set of basic configuration and security controls, the challenge is to maintain *consistency* across both. Even when the tools you use, like that nifty next generation firewall, might not even work the same (if at all) in both environments.

This paper describes how cloud network security is different, and how to pragmatically manage it for both pure cloud and hybrid cloud networks. We start with some background material and cloud networking 101, then move into cloud network security controls, and specific recommendations on how to use them. It's written for those with a basic background in networking, but if you made it this far, you'll be fine.

#Cloud Networking 101

###Key SDN differences for security professionals


SDNs don't work the same as a physical network. For example, in an SDN you can create two networks with overlapping address spaces that run on the same physical hardware, but never see each other. You can create an entirely new subnet not by adding hardware, but with a single API call that "creates" the subnet in software. 

SDN allows you to take all your networking hardware, abstract it, pool it together, and then allocate it however you want. On some cloud providers, for example, you can allocate an entire class B network with multiple subnets, routed to the Internet behind NAT, in just a few minutes or less. Different cloud providers use different underlying technologies, and further complicate things since they all offer different ways of managing the network.

Why make things so complicated? Actually, it makes management of your cloud network much *easier*, while allowing cloud providers to give customers a ton of flexibility to craft the virtual networks they need for different situations. Plus, it handles issues unique to cloud, like provisioning network resources faster than existing hardware can handle configuration changes (a very real problem), or multiple customers needing the same private IP address ranges to better integrate with their existing applications.



Software defined networks are fundamentally different
Loss of traditional visibility
New ways of managing networks
Constant state of change
The role of hybrid 
Bridging corporate networks to the cloud
Even with the cloud, you often connect different networks
Cloud Networking 101 
Types of cloud networks 
Full SDN (e.g. AWS) 
How it works
Differences from physical networks
VLAN-based (e.g. Softlayer)
Defining and managing cloud networks 
APIs
Architectures 
Public
Private
Internet gateways/connectivity
Internal gateways/connectivity
VPNs
Regions and availability zones
Hybrid cloud architectures 
Why hybrid
VPN-based
Direct network connections
Routing challenges
Impact of auto scaling and static routing challenges
Impact of cloud availability architectures
Monitoring challenges
Network Security Controls (pros and cons specified for each) 
What providers give you 
Perimeter security (what you don’t see)
Security groups
ACLs
Commercial add-ons 
Physical
Virtual appliances
Monitoring/logging 
Most providers do not offer
Varies tremendously
Additional options 
Virtual appliances
Host Security
Building your cloud network security program
Key considerations 
Provider-specific limitations or advantages
Application needs
“New” architectures
PaaS/IaaS mix
Elasticity and rate of change
Managing and monitoring
Designing the architecture
Accounts
Regions
Availability zones
VPCs (or equivalent, need to see what other providers call it)
Public/private
Internet and enterprise connections
VPNs
Direct connect
Security implications
Designing the network security architecture
Which tools to use when
Building your management plane
Integrating PaaS
Managing cloud (and hybrid) security operations
Organizing and staffing
Discovery
Policy enforcement
Integrating with dev
Normalizing on-prem and cloud
Translating rules
Normalizing operations
Accounting for architectural differences
Automation and immutable network security
Design Patterns
Pure public cloud application
Public only or public/private network
Hybrid/public facing application
Hybrid/internal-apps
Emerging architectures
