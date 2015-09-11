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
* *Cloud networks are managed via Application Programming Interfaces*, not by logging in and provisioning hardware like normal. A developer has the power to stand up an entire class B network, completely destroy an entire subnet or add a network interface to a server and bridge to an entirely different subnet on a different cloud account, all within minutes with just a few API calls.
* *Cloud networks change faster than physical networks, and constantly*. It isn't unusual for a cloud application to launch and destroy dozens of servers in under an hour — at rates greater than traditional security and network tools can keep track of things — or even build and destroy entire networks just for testing.
* *Cloud networks look like traditional networks, but aren't*. Cloud providers tend to give you things that look like routing tables and firewalls, that don't work *exactly* like your normal routing tables and firewalls. It's important to know the differences.

Don't worry, the differences make a lot of sense once you start digging in, and most of them provide *better* security that's *more accessible* than on a physical network, as long as you know how to manage it.

## The role of hybrid networks

A hybrid network bridges your existing network into your cloud provider. If, for example, you want to connect a cloud application to your existing database, you will connect your physical network to the virtual network in your cloud. 

Hybrid networks are extremely common, especially as traditional enterprises begin migrating to cloud computing and need to mix and match resources instead of building everything from scratch. One popular example is to set up some big data analytics in your cloud provider, where you only pay for the processing and storage time, and don't have to buy a bunch of servers you will only use once a quarter.

But hybrid networks complicate management both in your data center and in the cloud. Since both sides use a different set of basic configuration and security controls, the challenge is to maintain *consistency* across both, even when the tools you use — like that nifty next generation firewall — might not even work the same (if at all) in both environments.

This paper describes how cloud network security is different and how to pragmatically manage it for both pure cloud and hybrid cloud networks. We start with some background material and cloud networking 101, then move into cloud network security controls and specific recommendations on how to use them. It's written for those with a basic background in networking, but if you made it this far, you'll be fine.

# Cloud Networking 101

There isn't one canonical cloud networking stack out there; each cloud service provider uses their own mix of technologies to wire everything up. Some of these might use known standards, tech, and frameworks, while others might be completely proprietary and so secret that you, as the customer, don't ever know exactly what is going on under the hood. 

Building cloud scale networks is insanely complex, and the different providers clearly see networking capabilities as a competitive differentiator.

So instead of trying to describe all the possible options, we'll keep things at a relatively high level and focus on common building blocks we see relatively consistently on the different platforms.

##Types of Cloud Networks

When you shop providers, cloud networks roughly fit into two buckets:

* *Software Defined Networks (SDN)* that fully decouple the virtual network from the underlying physical networking and routing.
* *VLAN-based Networks* that still rely on the underlying network for routing, lacking the full customization of an SDN.

Most providers today offer full SDNs of different flavors, so we'll focus more on those, but we do still encounter some VLAN architectures and need to cover them at a high level.

###Software Defined Networks

As we mentioned, Software Defined Networks are a form of virtual networking that takes advantage (usually) of special features in routing hardware to fully abstract the virtual network you see from the underlying physical network. To your instance (virtual server) everything looks like a normal network, but instead of connecting to a normal network interface it connects to a virtual network interface, which handles everything in software.

SDNs don't work the same as a physical network (or even an older virtual network). For example, in an SDN you can create two networks with overlapping address spaces that run on the same physical hardware, but never see each other. You can create an entirely new subnet not by adding hardware, but with a single API call that "creates" the subnet in software. 

How do they work? Ask your cloud provider. Amazon Web Services, for example, intercepts every packet, wraps it and tags it, and uses a custom "mapping service" to figure out where to actually send the packet over the physical network, with multiple security checks to ensure no customer ever sees someone else's packet. (You can watch a video with great details [at this link](http://www.youtube.com/watch?v=Zd5hsL-JNY4)). Your instance never sees the real network, and AWS skips a lot of the normal networking (like ARP requests/caching) within the SDN itself.

SDN allows you to take all your networking hardware, abstract it, pool it together, and then allocate it however you want. On some cloud providers, for example, you can allocate an entire class B network with multiple subnets, routed to the Internet behind NAT, in just a few minutes or less. Different cloud providers use different underlying technologies, and further complicate things since they all offer different ways of managing the network.

Why make things so complicated? Actually, it makes management of your cloud network much *easier*, while allowing cloud providers to give customers a ton of flexibility to craft the virtual networks they need for different situations. Plus, it handles issues unique to cloud, like provisioning network resources faster than existing hardware can handle configuration changes (a very real problem), or multiple customers needing the same private IP address ranges to better integrate with their existing applications.

###Virtual LANS (VLANS)

A few providers still rely on VLANS... 

https://en.wikipedia.org/wiki/Virtual_LAN

- VLANs don't offer the same flexibility. You can create segmentation on the network and isolate and filter traffic, but can't do SDN-level things like create two networks on the same hardware with the same address range.
- VLANs are built into standard networking hardware, which is why that's where many people used to start. No special software needed.
- Customers don't get to control their addresses and routing very well
- Can't be trusted for security segmentation.
- Mostly being phased out of cloud computing due to these limitations

##Defining and Managing Cloud Networks

###Cloud Network Architectures

* Public cloud network - Internet facing. You connect to your instances/servers via the public Internet, no special routing needed; every instance has a public IP address.
* Private cloud network - sometimes called "virtual private cloud". Uses private IP addresses like most of you use on the LAN. You have to have a back-end connection, like a VPN, to connect to your instances. Most providers allow you to pick your address ranges so you can use these private networks as an extension of your existing network. If you need to bridge traffic to the Internet you route it back through your data center, or you use Network Address Translation to a public network segment, similar to how we NAT our existing LANs to the Internet.
* Internet connectivity (Internet Gateway) hooks your cloud network to the Internet. You don't tend to directly manage it, your cloud provider does it for you.
* Internal Gateways/connectivity- these connect your existing datacenter to your private network in the cloud. Often VPN based, but instead of managing the VPN server yourself, the cloud provider handles it (and you just manage the configuration). Some providers also support direct connections through partner broadband network providers that will route directly between your data center and the private cloud network, instead of using a VPN (leased lines).
* Virtual Private Networks- instead of using the cloud provider's, you can always set your own up assuming you can bridge the private and public networks in the cloud provider. Very common, especially if you don't want to directly connect your data center and cloud, but still want a private segment and allow access to it for your users/developers/admins.
* Cloud providers all break up their physical infrastructure differently. Typically they have different data centers (which might be a collection of multiple data centers clumped together) in different regions. This is for performance/proximity, to meet local legal and regulatory requirements around data residency, and for disaster recovery/availability. Most providers charge for some or all network traffic if you communicate across regions/locations, which would make disaster recovery expensive. So they provide local "zones" that break out an individual region into isolated pieces with their own network, power, etc. A problem might take out one zone in a region, but shouldn't take out any others, giving customer's a way to build for resiliency without having to span continents or oceans. Plus, you don't tend to pay for the local network traffic between zones. 

###Managing Cloud Networks

* everything managed over APIs. Typically REST-based.
* You can fully define and change everything remotely via API, and it happens nearly instantly in most cases.
* Cloud platforms also have web UIs, which still use the APIs, but tend to automate a lot of the heavy lifting for you.
* Key for security is protecting these management interfaces, since someone can otherwise completely reconfigure your network while sitting at a hipster coffee shop, making them, by definition, evil.

##Hybrid cloud architectures

As mentioned, connect your data center to the cloud.
Why? Sometimes you need more resources, and you don't want them on the public Internet. Also very common for established companies that aren't starting from scratch, and need to mix and match resources.
* VPN-based- you connect to the cloud via a dedicated VPN, which is nearly always hardware based and hooked into your local routers to span traffic to the cloud. The cloud provider, as mentioned, handles their side of the VPN, but you still have to configure some of it. All traffic goes over the Internet
* Direct network connections- typically over leased lines. Isn't necessarily more secure, and much more expensive, but can reduce latency.

##Routing Challenges

* Auto scaling is when you define rules in your cloud to add or remove instances of a server based on rules, like when you hit 80% CPU load. It can then terminate those instances when load drops (clearly you need to architect for this kind of behavior). Creates application elasticity, since your resources automatically adapt based on demand, instead of having to leave a bunch of servers running all the time just in case demand increases. This is the real heart of Infrastructure as a Service.
* If you think about it, you won't necessarily know the exact IP address of all your servers, since they may appear and disappear within minutes. 
* Better yet- when you design for availability you tend to set the rules to keep multiple instances in multiple subnets across multiple zones, in case one of them drops out.
* And within those virtual subnets, you might have multiple different types of instances, with different security requirements. This is pretty common in cloud computing.
* All this challenges security- fewer static routes, highly dynamic addressing, and servers that might only "live" for less than an hour. Requires new ways of thinking, which is what the rest of this paper will focus on.



