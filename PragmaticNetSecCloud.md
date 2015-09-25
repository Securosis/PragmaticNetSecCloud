Title: Pragmatic Network Security for Cloud and Hybrid Networks
by Rich Mogull

For a few decades we have been refining our approach to network security. Find the boxes, find the wires connecting them, drop a few security boxes between them in the right spots, and move on. Sure, we continue to advance the state of the art in exactly *what* those security boxes do, and we constantly improve how we design networks and plug everything together, but overall change has been incremental. *How we think about network security* doesn't change -- just some of the particulars.

Until you move to the cloud.

While many of the fundamentals still apply, cloud computing releases us from the physical limitations of those boxes and wires by fully abstracting the network from the underlying resources. We move into entirely virtual networks, controlled by software and APIs, with very different rules. Things may look the same on the surface, but dig a little deeper and you quickly realize that network security for cloud computing requires a different mindset, different tools, and new fundamentals.

Many of which change every time you switch cloud providers.

#The challenge of cloud computing and network security

Cloud networks don't run magically on pixie dust, rainbows, and unicorns -- they rely on the same old physical network components we are used to. The key difference is that cloud customers never access the 'real' network or hardware. Instead they work inside virtual constructs -- that's the nature of the cloud.

Cloud computing uses *virtual networks* by default. The network your servers and resources see is abstracted from the underlying physical resources. When you server gets IP address 10.0.0.12, that isn't really that IP address on the routing hardware -- it's a virtual IP address on a virtual network. Everything is handled in software, and most of these virtual networks are *Software Defined Networks* (SDN). We will go over SDN in more depth in the next section.

These networks vary across cloud providers, but they are all fundamentally different from traditional networks in a few key ways:

* **Virtual networks don't provide the same visibility as physical networks** because packets don't move around the same way. We can't plug a wire into the network to grab all the traffic -- there is no location all traffic traverses, and much of the traffic is wrapped and encrypted anyway.
* **Cloud networks are managed via Application Programming Interfaces** -- not by logging in and provisioning hardware the old-fashioned way. A developer has the power to stand up an entire class B network, completely destroy an entire subnet, or add a network interface to a server and bridge to an entirely different subnet on a different cloud account, all within minutes with a few API calls.
* **Cloud networks change faster than physical networks, and constantly.** It isn't unusual for a cloud application to launch and destroy dozens of servers in under an hour -- faster than traditional security and network tools can track -- or even build and destroy entire networks just for testing.
* **Cloud networks look like traditional networks, but aren't.** Cloud providers tend to give you things that *look* like routing tables and firewalls, but don't work *quite* like your normal routing tables and firewalls. It is important to know the differences.

Don't worry -- the differences make a lot of sense once you start digging in, and most of them provide *better* security that's *more accessible* than on a physical network, so long as you know how to manage them.

##The role of hybrid networks

A hybrid network bridges your existing network into your cloud provider. If, for example, you want to connect a cloud application to your existing database, you can connect your physical network to the virtual network in your cloud.

Hybrid networks are extremely common, especially as traditional enterprises begin migrating to cloud computing and need to mix and match resources instead of building everything from scratch. One popular example is setting up big data analytics in your cloud provider, where you only pay for processing and storage time, so you don't need to buy a bunch of servers you will only use once a quarter.

But hybrid networks complicate management, both in your data center and in the cloud. Each side uses a different basic configuration and security controls, so the challenge is to maintain *consistency* across both, even though the tools you use -- such as your nifty next generation firewall -- might not work the same (if at all) in both environments.

This paper will explain how cloud network security is different, and how to pragmatically manage it for both pure cloud and hybrid cloud networks. We will start with some background material and cloud networking 101, then move into cloud network security controls, and specific recommendations on how to use them. It is written for readers with a basic background in networking, but if you made it this far you'll be fine.

#Cloud Networking 101

There is no canonical cloud networking stack -- each cloud service provider uses its own mix of technologies to wire everything up. Some of these use known standards, tech, and frameworks, while others are completely proprietary and so secret that you as a customer never know exactly what is going on under the hood.

Building cloud scale networks is insanely complex, and the different providers clearly see networking capabilities as a competitive differentiator.

So instead of trying to describe all possible options we will keep things relatively high-level, and focus on common building blocks we see relatively consistently on the different platforms.

##Types of Cloud Networks

When you shop providers cloud networks roughly fit into two buckets:

* **Software Defined Networks (SDN)** that fully decouple the virtual network from the underlying physical networking and routing.
* **VLAN-based Networks** that still rely on the underlying network for routing, lacking the full customization of an SDN.

Most providers today offer full SDNs of different flavors, so we'll focus more on those, but we do still encounter some VLAN architectures which we need to cover at a high level.

###Software Defined Networks

As we mentioned, Software Defined Networks are a form of virtual networking that (usually) takes advantage of special features in routing hardware to fully abstract the virtual network you see from the underlying physical network. To your instance (virtual server) everything looks like a normal network. But instead of connecting to a normal network interface it connects to a virtual network interface which handles everything in software.

SDNs don't work the same as physical networks (or even older virtual networks). For example, in an SDN you can create two networks that use the same address spaces and run on the same physical hardware but never see each other. You can create an entirely new subnet, not by adding hardware, but with a single API call that 'creates' the subnet in software.

How do they work? Ask your cloud provider. Amazon Web Services, for example, intercepts every packet, wraps it and tags it, and uses a custom mapping service to figure out where to actually send the packet over the physical network with multiple security checks to ensure no customer ever sees someone else's packet. (Amazon has posted a [video with great details](http://www.youtube.com/watch?v=Zd5hsL-JNY4)). Your instance never sees the *real* underlying network, and AWS skips a lot of normal networking (including ARP requests/caching) within the SDN itself.

SDN enables you to take all your networking hardware, abstract it, pool it together, and then allocate it however you want. On some cloud providers, for example, you can allocate an entire class B network with multiple subnets, routed to the Internet behind NAT, in just a few minutes. Different cloud providers use different underlying technologies; just to complicate things, they all offer different ways of managing their networks.

Why make things so complicated? Because SDN actually makes management of cloud networks much *easier*, while allowing cloud providers to offer customers a ton of flexibility to craft the virtual networks they need for different situations. The providers do the heavy lifting and you as the consumer work in a simplified environment. Plus SDN handles issues unique to the cloud, like provisioning network resources faster than existing hardware can handle configuration changes (a very real problem), and multiple customers needing the same private IP address ranges to integrate optimally with existing applications.

###Virtual LANs (VLANs)

Although they do not offer the same flexibility as SDNs, a few providers still rely on [VLANs](https://en.wikipedia.org/wiki/Virtual_LAN). Customers must evaluate their own needs but VLAN-based cloud services should be considered outdated compared to SDN-based cloud services.

VLANs let you create segmentation on the network, and can isolate and filter traffic, in effect just cutting off your own slice of the network rather than creating your own virtual environment. This means you can't do SDN-level things like creating two networks on the same hardware with the same address range.

* VLANs don't offer the same flexibility. You can create segmentation on the network, and isolate and filter traffic, but cannot do SDN-level things like create two networks on the same hardware with the same address range.
* VLANs are built into standard networking hardware, which is why that's where many people start. No special software needed.
* Customers don't get to control their own addresses and routing very well.
* VLANs cannot be trusted for security segmentation.

Because VLANs are built into standard networking hardware, they are where most people started creating cloud computing, because no special software was required. But customers on VLANs don't get to control their addresses and routing very well, and they scale and perform terribly when you plop a cloud on top of them. They are mostly being phased out of cloud computing due to these limitations.

##Defining and Managing Cloud Networks

While we like to think of one big cloud out there, there is more than one kind of cloud network and several technologies that support them. Each provides different features and presents different customization options. Management also varies between vendors, but they exhibit certain common characteristics. Different providers use different terminology so we have tried to pick ones that will make sense when you look at actual offerings.

###Cloud Network Architectures

You need to understand the types of cloud network architectures, and the different technologies that enable them, to fit your needs to the right solution.

There are two basic types of cloud network architectures.

* **Public cloud networks** are Internet facing. You connect to your instances/servers via the public Internet with no special routing; every instance has a public IP address.
* **Private cloud networks** (also called "virtual private clouds") use private IP addresses like you would on a LAN. You need a back-end connection like a VPN to connect to your instances. Most providers allow you to pick your own address ranges so you can use these private networks as extensions of your existing network. If you need to bridge traffic to the Internet you route it back through your data center or use Network Address Translation to a public network segment, similar to the way home networks use NAT to bridge to the Internet.

These are enabled and supported by the following technologies:

* **Internet Connectivity:** An Internet gateway hooks your cloud network to the Internet. You normally don't directly manage it -- your cloud provider does it for you.
* **Internal Gateways** connect your existing datacenter to your private cloud network. These are often VPN based, but instead of managing the VPN server yourself the cloud provider handles it -- you just manage the configuration. Some providers also support direct connections through partner broadband network providers which route directly between your data center and the private cloud network, instead of using a VPN on leased lines.
* **Virtual Private Networks:** Instead of using your cloud provider's, you can always set up your own, assuming you can bridge your private and public networks at your cloud provider. This kind of setup is very common, especially if you don't want to directly connect your data center and cloud; but still want a private segment with access for users, developers, and administrators.

Cloud providers all break up their physical infrastructure differently. Typically they have different data centers (which might actually be a collection of multiple data centers clumped together) in different regions. A *region or location* is the physical location of the data center(s), while a *zone* is a sub-section of that region used for ensuring availability. These are for:

* **Performance:** Taking advantage of physical proximity can improve the performance of your applications which pass large amounts of traffic.
* **Regulatory Requirements:** Geographical flexibility for your data stores can help you satisfy legal and regulatory requirements around data residency.
* **Disaster Recovery and Availability:** Most providers charge for some or all network traffic between across regions and locations, which can make disaster recovery expensive. That's why they provide local 'zones' to break individual regions into independent pieces -- each with its own network, power, and so forth. A problem might take out one zone in a region, but shouldn't take out any others, giving customers a way to build resiliency without having to span continents or oceans. Fortunately you typically don't pay for network traffic between zones within a region.

###Managing Cloud Networks

Managing these networks depends on all the components listed above. Each vendor has its own set of tools, based on common principles.

* Everything is managed via APIs, which are typically RESTful (REpresentational State Transfer based).
* You can fully define and change everything remotely via APIs, and it happens nearly instantly in most cases.
* Cloud platforms also have web UIs, which are simply front ends for the same APIs you code to, but they tend to automate a lot of the heavy lifting for you.
* Key for security is protecting these management interfaces, because otherwise someone could completely reconfigure your network while sitting at a hipster coffee shop, making them, by definition, evil (fortunately you can usually spot them by their ski masks, according to our clip art library).

##Hybrid Cloud Architectures

As mentioned earlier, your data center may be connected to the cloud. Sometimes you need more resources you don't want on the public Internet. This is common for established companies which aren't starting from scratch, and need to mix and match resources.

There are two ways to accomplish this.

* **VPN Connections:** You connect to the cloud via a dedicated VPN, which is nearly always hardware-based and hooked into your local routers to span traffic to the cloud. The cloud provider handles their side of the VPN but you still have to configure some of it. All traffic goes over the Internet but is isolated.
* **Direct Network Connections:** These are typically set up over leased lines. They aren't necessarily more secure, and are much more expensive, but they can reduce latency.

##Routing Challenges

Cloud services offer remarkable flexibility, but they also require substantial customization, and they pose their own security challenges.

Nearly every Infrastructure as a Service provider supports *auto scaling*, which is one of the single most important benefits of cloud computing. You can define your own rules for when to add or remove server instances. For example you could set a rule to add servers when you hit 80% CPU load, and terminate them when load drops -- of course you need to architect appropriately for this kind of behavior.

This creates application elasticity -- your resources can automatically adapt based on demand, instead of having to leave servers running all the time *just in case* demand increases. Your consumption now aligns with demand, instead of traditional architectures, which leave a lot of hardware sitting around unused until demand is high enough. This is the heart of IaaS. This is what you're paying for, and how you can save money.

Such flexibility creates complexity. You won't necessarily know the exact IP address of all your servers -- they can appear and disappear within minutes. You may even design in complexity when you design for availability -- by creating rules to keep multiple instances in multiple subnets across multiple zones available in case one of them drops out. Within those virtual subnets, you might have multiple different types of instances with different security requirements. This is common in cloud computing.

Fewer static routes, highly dynamic addressing, and servers that can exist for less than an hour... this all challenges security. It requires new ways of thinking, which is what the rest of this paper will focus on.

Our goal is to start getting you comfortable with how different cloud networks can be. On the surface, depending on your provider, you may still be managing subnets, routing tables, and ACLs. But underneath these are now probably database entries implemented in software, not the hardware you might be used to.

#Network Security Controls

Now that we have covered the basics of cloud networks it's time to focus on available security controls. Keep in mind that all of this varies between providers, and that cloud computing is rapidly evolving, with new capabilities are appearing constantly. These fundamentals give you the background to get started, but you will need to learn the ins and outs of whatever platforms you work with.

##What Cloud Providers Give You

Not to sound like a broken record (those round things your parents listened to... no, not the small shiny ones with lasers), but all providers are different. The following options are relatively common across providers, but not ubiquitous.

* **Perimeter security** is traditional network security that the provider totally manages, invisibly to their customers. Firewalls, IPS, etc. are used to protect the provider's infrastructure. The customer doesn't control any of it.
 * **Pro:** It's free, effective, and always there.
 * **Con:** You don't control any of it, and it's only useful for stopping background attacks.

* **Security groups:** Think of a group as a tag you can apply to a network interface/instance (or certain other cloud objects, such as databases and load balancers) that applies an associated set of network security rules. Security groups combine the best of network and host firewalls, because you get policies that can follow individual servers (or even network interfaces) like a host firewall, but you manage them like network firewalls, with protection applied no matter what is running inside. You get the granularity of a host firewall with the manageability of a network firewall. They are critical to auto scaling. You are now spreading assets all over your virtual network, and instances appear and disappear on demand, so you cannot build security rules on IP addresses. Here's an example: You can create a "database" security group that only allows access to one specific database port, from instances inside a "web server" security group, and only the web servers in that group can talk to the database servers in this "database" group. Unlike a network firewall, the database servers can't talk to each other, because they aren't in the web server group (remember, these rules are applied on a per-server basis, not at subnet boundaries). As new databases pop up the right security is applied so long as they have the tag. Unlike host firewalls you don't need to log into servers to make changes, so everything is much easier to manage. Not all providers use this term, but the concept of security rules as a policy set you can apply to instances is relatively consistent.
 * **Pro:** It's free and it works hand in hand with auto scaling and default deny. It's very granular but also very easy to manage. It's the core of cloud network security.
 * **Con:** They are usually allow rules only (you can't explicitly deny), basic firewalling only, and you cannot manage them using familiar tools.

* **ACLs (Access Control Lists)** -- While security groups work on a per instance (or object) level, ACLs restrict communications between subnets in your virtual network. Not all providers offer them, and they are more to handle legacy network configurations (when you need a restriction that matches what you might have in your existing data center) than 'modern' cloud architectures (which typically ignore or avoid them). In some cases you can use them to get around the limitations of security groups, depending on your provider.
 * **Pro:** ACLs can isolate traffic between virtual network segments and can create both `allow` and `deny` rules.
 * **Con:** They're not great for auto scaling and don't apply to specific instances. You also lose some powerful granularity.
 * By default nearly all cloud providers launch your assets with default-deny on all inbound traffic. Some might automatically open a management port from your current location (based on IP address), but that's about it. Some providers may use the term ACL to describe what we called a security group. Sorry -- we know it's confusing, but blame the vendors, not your friendly neighborhood analysts.

###Commercial Options

There are a number of add-ons you can buy through your cloud provider, or buy and run yourself.

* **Physical security appliances:** The provider will provision an old-school piece of hardware to protect your assets. These are mostly seen at VLAN-based providers, and are pretty antiquated. They may also be used in private (on-premise) clouds, where you control and run the network yourself, which is out of scope for this research.
 * **Pro:** They're expensive, but they're something you are used to managing. They keep your existing vendor happy? Look, it's really all cons on this one unless you're a cloud provider, and in that case this paper isn't for you.

* **Virtual appliances** are a virtual machine version of your friendly neighborhood security appliance, and must be configured and tuned for the cloud platform you are working on. They can provide more advanced security -- such as IPS, WAF, NGFW -- than cloud providers typically offer. They are also useful for capturing network traffic, which providers tend not to support.
 * **Pro:** They enable more advanced network security, and can be managed the same as on-premise versions of these tools.
 * **Con:** Cost can be a concern because they use resources like any other virtual server constraining your architectures, and may not play well with auto scaling and other cloud features.

* **Host security agents** are software agents you build into images, which run in your instances and provide network security. This could include IDS, IPS, or other features beyond basic firewalling. We recommend lightweight agents with remote management. The agents (and management platform) need to be designed for use in cloud computing, because auto scaling and portability break traditional tools.
 * **Pro:** Like virtual appliances, host security agents can offer features missing from your provider. With a good management system they can be extremely flexible, and they usually include capabilities beyond network security. They are a great option for monitoring network traffic.
 * **Con:** You need to make sure they are installed and run in all your instances, and they are not free. They also won't work well if you get one that isn't designed for the cloud.

> A note on monitoring: None of the major providers offers packet-level network monitoring and many don't offer any network monitoring at all. If you need it, consider host agents and virtual appliances.



[1]:	http://www.youtube.com/watch?v=Zd5hsL-JNY4
[2]:	https://en.wikipedia.org/wiki/Virtual_LAN

To review, your network security controls, no matter what your provider calls them, nearly always fall into 5 buckets:

* Perimeter security the provider puts in place, you never see or control.
* Software firewalls built into the cloud platform (security groups) that protect cloud assets (like instances), offer basic firewalling, and are designed for auto scaling and other cloud-specific uses.
* Lower-level Access Control Lists for controlling access into, out of, and between the subnets in your virtual cloud network.
* Virtual appliances to add expanded features from your familiar network security tools, such as IDS/IPS, WAF, and NGFW.
* Host security agents to embed in your instances.

>Advanced Options on the Horizon
>
>We know some niche vendors already offer more advanced network security built into their platforms, such as IPS, and we suspect major vendors to eventually offer similar options. We don't recommend picking a cloud provider based on these, but you may get more options in the future.

#Building Your Cloud Network Security Program

There isn't any single "best" way to secure a cloud or hybrid network. Cloud computing is moving faster than any other technology in decades, with providers constantly trying to out innovate each other with new capabilities. Thus you can't lock into any single architecture, and instead need to build out a program capable of handling diverse, ever changing needs.

There are four major focus areas when building out this program.

* Start by understanding the *key considerations* for the cloud platform and application you are working with.
* Design the *network and application architecture* for security.
* Design your *network security architecture*, which includes additional security tools (if needed) and management components.
* *Manage security operations* for your cloud deployments, including everything from staffing to automation.

##Understand Key Considerations

Building applications in the cloud is most definitely not the same as building them on traditional infrastructure. Sure, you can do it, but the odds are pretty high something will break. Badly. As in "update that resume" badly. To really get the benefits of cloud computing, applications must be designed specifically for cloud, including the security controls.

From a network security perspective, this means there are a few key things you need to keep in mind before you ever start mapping out security controls.

* Provider specific limitations or advantages. All providers are different. Nothing is standard, and don't expect it to ever be standard. One provider's security group is another's ACL. Some allow more granular management. There may be limits on the number of security rules available. A provider might offer both allow and deny rules, or allow only. Take the time to learn the ins and outs of your provider's capabilities. They all offer plenty of documentation and training, and in our experience most organizations limit themselves to no more than one to three infrastructure providers, keeping the problem manageable.
* Application needs. Applications, especially ones using newer architectures we will mention in a moment, will often have different needs than apps deployed on traditional infrastructure. For example, application components in your private network segment may still need Internet access to connect to a cloud component, such as storage, a message bus, or a database. These needs will directly affect architectural decisions, security or otherwise.
* New architectures -- Cloud applications will use different design patterns than apps on traditional infrastructure. For example, as previously mentioned, components will probably distributed in diverse network locations for resiliency, tied tightly to cloud-based load balancers. Early cloud applications often emulated traditional architectures, but modern cloud applications make extensive use of advanced features, particularly Platform as a Service, that may be deeply integrated into a particular cloud provider. Cloud-based databases, message queues, notification systems, storage, containers, and application platforms are all now common due to their cost, performance, and agility benefits. You often can't even control the network security of these services, since they are fully managed by the cloud provider. Continuous deployment, DevOps, and immutable servers are the norm, not exceptions. On the upside, used properly these architectures and patterns are far more secure, cost effective, resilient, and agile than building everything yourself, but you do need to understand how they work.

>Data Analytics Design Pattern Example
>a common data analytics design pattern highlights these differences. Instead of keeping a running analytics pool and sending it data via SFTP, you start by loading data into cloud storage directly using an (encrypted) API call. This, using a feature of the cloud, triggers the launch of a pool of analytics servers and passes on the job to a message queue in the cloud. The message queue distributes the jobs to the analytics servers, which use a cloud-based notification service to signal when they are done, and the queue will redistributed broken jobs. Once it's all done the results are stored in a cloud-based no-SQL database, and the source files are archived. It's similar to "normal" data analytics except everything is event driven, using features and components of the cloud service. It can handle as many concurrent jobs as you need, yet you don't have anything running (and racking up charges) until a job enters the system.

* Elasticity and a high rate of change are the norm in the cloud. Beyond auto scaling, cloud applications tend to alter the infrastructure around them to maximize the benefits of cloud computing. For example, one of the best ways to update a cloud application isn't to patch servers, but create an entirely new version of the app, based on a template, running in parallel and than swap traffic over from the current version. This breaks familiar security approaches, like relying on IP addresses for server identification, vulnerability scanning, logging based on server name or address, and other controls that aren't adapted for cloud.
* Managing and monitoring security changes, since you either need to learn how to manage cloud security using the provider's console and APIs, or pick security tools that directly integrate. This may become especially complex if you need to normalize security between your data center and cloud provider when building a hybrid cloud. Also, few cloud providers offer good tools to track security changes over time, which means you will need to track them yourself, or use a third party tool.

##Design the Network Architecture

Unlike traditional networks, security is built into cloud networks by default. Go to any major cloud provider, spin up a virtual network, launch a server, and the odds are very high it is well-defended with most, or all, access blocked by default.

Because security and core networking are so intertwined, and *each* cloud application has it's own (or multiple) virtual networks, the first step towards security is to work with the application team and design it into the architecture.

Here are some specific guidelines and recommendations:

* **Accounts** provide your first layer of segregation. With a given cloud provider you establish multiple accounts, each working as a different environment (e.g. dev, test, production, logging). This allows you to tailor the cloud security controls and minimize administrator access. This isn't a purely network security feature, but *will* affect network security since you can, for example, have tighter controls the closer the environment comes to production data. The rule of thumb for accounts is to consider separate accounts for separate applications, and then separate accounts for a given application when you want to restrict how many people have administrator access. For example, the dev account is more open with more admins, while production is a different account with a much smaller group of administrators. Within accounts don't forget about the physical architecture:
	* **Regions/locations** will often be used for resiliency, but may also be incorporated into the architecture for data residency requirements or to reduce network latency to customers. Unlike accounts we don't tend to use locations for security, but they *do* require you build your network security within each location.
	* **Zones** are the cornerstone of cloud application resiliency, especially when tied to auto scaling. You won't use them as a security control, but they will, again, affect security since they often map directly to subnets. Since an auto scale group might keep multiple instances of a server in different zones, which are different subnets, you won't necessarily be able to rely on subnets and addresses when designing your security.
* **Virtual Networks (Virtual Private Clouds)** are your next layer of security segregation. You can (and will) create and dedicate separate virtual networks for each application (potentially in different accounts), each with its own set of network security controls. This compartmentalization offers some pretty tremendous security advantages, but really complicates security management. It forces you to rely much more on automation, since manually replicating security controls across multiple accounts, and then multiple virtual networks within each account, takes tremendous discipline and effort. In our experience, the security benefits of compartmentalization outweigh the risks of the management complexity, especially since development and operations teams already tend to rely on automation techniques to create, manage, and update the environments and applications in the first place. There are a few additional non-security-specific aspects to keep in mind when you design the architecture:
	* Within a given virtual network, you can include public and private facing subnets, and connect them together. This is similar to our DMZ topologies, except the public facing assets can still be fully restricted from the Internet, and the private network assets are all, by default, walled off from each other. What's even more interesting is you can spin up totally isolated private network segments that only connect to other application components through an internal cloud service, like a message queue, and disable all server to server traffic over the network.
	* There is no additional cost to spin up new virtual networks (well, if your provider charges you for this, it's time to move on), and you can create one with a few mouse clicks or API calls. Some providers even allow you to bridge across virtual networks, assuming they aren't running on the same IP address range. Instead of trying to lump everything in one account and one virtual network, it makes far more sense to use multiple networks for different applications, and even within a given application architecture.
	* Within a virtual network, you also have complete control over subnets. While they can play a role in your security design, especially as you map out public and private network segments, make sure you also design them to support zones for availability.
	* Flat networks aren't flat in the cloud. Since everything you deploy in the virtual network is surrounded by it's own policy-based firewall that blocks all connections by default, you don't need to rely on subnets themselves as much for segregation between application components. Public vs. private subnets are one thing, but creating a bunch of smaller subnets to isolate application components quickly leads to diminishing returns.

###Hybrid Clouds

You may need *enterprise datacenter connections for hybrid clouds*. These VPN or direct connections route traffic from your data center to the cloud (and vice versa) directly. You simply set your routing tables to send traffic to the appropriate destination, and SDN based virtual networks allow you to set distinct subnet ranges to avoid address conflicts with existing assets.

Whenever possible, we actually recommend avoiding hybrid cloud deployments. It isn't that there is necessarily anything wrong with them, but it makes it much more difficult to support account and virtual network segregation. For example, if you use separate accounts or virtual networks for your different dev/test/prod environments, you will tend to do so using templates to automatically build out your architecture, and they will perfectly mimic each other, down to individual IP addresses. But if you connect them directly to your data center, you now need to shift to non-overlapping address ranges to avoid conflicts, and they can't be as automated, and won't be as consistent. (This consistency is the cornerstone of *continuous deployment* and *DevOps*).

Plus, hybrid clouds complicate security. We've actually seen them, not infrequently, *reduce* the overall security level in your cloud since assets in your datacenter aren't as segregated as on the cloud network, and cloud providers tend to be more secure than most organizations can achieve in their own infrastructure. Instead of cracking your cloud provider, someone only needs to crack a system on your corporate network, and use that to directly bridge to the cloud.

So when should you consider a hybrid deployment? Any time your application architecture requires direct, address-based access to an internal asset that isn't Internet accessible. Alternatively, sometimes you need a cloud asset on a static, non-Internet routable address, like an email server or other service that isn't designed to work with auto scaling, that internal things need to connect to. (We *really* recommend you minimize these since they don't take advantage of the benefits of cloud computing, so there usually isn't a good reason to deploy them there). And yes, this means hybrid deployments are extremely common unless you are building everything from scratch. Just because we try to minimize their use doesn't mean they don't play a very important role.

From a security perspective, there are a few things to keep in mind when building a hybrid deployment:

* VPN traffic will traverse the Internet. While VPNs are very secure, you do need to keep them up to date with the latest patches and make sure you use strong, up to date certificates.
* Direct connections may reduce latency, but determine if you trust your provider and if you need to encrypt the traffic.
* Don't let *your* infrastructure reduce the security of your cloud. If you mandate multifactor authentication in the cloud, but not on your LAN, that becomes a potentially loophole. Is your entire LAN connected to the cloud? Could someone compromise a single workstation and then start attacking your cloud through your direct connection? Do you have security group or other firewall rules to keep your cloud assets as segregated from datacenter assets as they are from each other? Remember, cloud providers tend to be exceptionally good at security, and everything you deploy in the cloud is isolated by default. Don't allow your hybrid connection to become the weak link by reducing this compartmentalization.
* You may still be able to use multiple accounts and virtual networks for segregation, by routing different datacenter traffic to different accounts/virtual networks. However, your on-premise VPN hardware or your cloud provider might not support this, so check ahead of time before building it into your architecture.
* Remember that cloud and on-premise network security controls may look similar on the surface, but have some pretty deep implementation differences. If you want unified management, you will need to understand these differences and be able to harmonize based on security goals, not by trying to force a standard implementation across two very different technologies.
* Cloud computing offers a lot more ways to integrate into your existing operations than you might think. For example, instead of using SFTP and setting up public servers to receive data dumps, consider installing your cloud provider's command line tools and directly transfer data to their object storage service (fully locked down, of course). Now you don't need to maintain the burden of either an Internet-accessible FTP server, or a hybrid cloud connection.

It's hard to fully convey all your options for building security into your architectures before you ever look at additional security tools. This isn't mere theory, we have a lot of real-world experience with different architectures that create much higher security levels than you can do on traditional infrastructure at any reasonable cost.

##Design the Network Security Architecture

At this point you should have a well-segregated environment where effectively every application, and every environment (e.g. dev/test) for every application, is running on its own virtual network. And these assets are mostly either in auto scale groups that spread them around zones and subnets for resiliency, or connect to secure cloud services like databases, message queues, and storage. These architectures alone, in our experience, are materially more secure than your typical starting point on traditional infrastructure.

Now it's time to layer on the additional security controls we covered back in the *Cloud Networking 101* section. Instead of repeating the pros and cons, here are some direct recommendations about when to use each option:

* Security groups -- used by default, deny by default, and only open up the absolute minimum of access needed. Since cloud allows you to right size resources far more easily than when you run things on your own hardware, we find most organizations tend to deploy a far fewer number of services on any individual instance, which directly translates to having to open up fewer network ports. A large number of cloud deployments we've evaluated use only a good base architecture and security groups for their network security.
* ACLs -- these mostly make sense in hybrid deployments where you need to more-closely match or restrict communications between the data center and the cloud. Security groups are usually a better choice, and we only recommend falling back to ACLs when you can't hit your security objectives otherwise.
* Virtual Appliances -- anytime you need capabilities beyond basic firewalls, this is where you will usually end up. However, we do think host agents often make more sense when they offer the same capabilities, since virtual appliances become costly bottlenecks that restrict your cloud architectural options. Don't deploy one merely because you have a checkbox requirement for a particular tool, ensure they make sense. Over time we do see them becoming more "cloud friendly", but when we rip into requirements on projects, we often find there are better, more cloud-appropriate ways of meeting the same security objectives.
* Host security agents are often a better option than using a virtual appliance, since they don't restrict your virtual networking architectural options. But you do need to ensure you have a way to consistently deploy them. Also, make sure you pick cloud-specific tools that are designed to work with features such as auto scaling. These tools are particularly useful to cover any network monitoring gaps, meet IDS/IPS requirements, and meet all your normal host security needs.

You will, of course, need some way of managing those controls, even if you stick to only the capabilities and features offered by your cloud provider.

Security groups and ACLs are managed via API or your cloud provider's console. They use the same *management plane* as the rest of the cloud, but this won't necessarily integrate out of the box with how you manage things internally. You can't track these across multiple accounts and virtual networks unless you use a purpose-built tool, or write your own code. We will talk about specific techniques for management in the next section, but make sure you plan on *how* to manage these controls when you design the architecture.

Platform as a Service introduces its own set of security differences. For example, in some situations you still define security groups and/or ACLs for the platform (as with a cloud load balancer), and in other cases access to the platform is only via API, and may require an outbound public Internet connection, even from a private network segment. PaaS also tends to rely more on DNS as opposed to IP addressing so the cloud provider maintains flexibility. We can't give you any hard and fast rules here. Understand what's required to connect to the platform, and then ensure your architecture allows those connections. When you *can* manage the security, treat it like any other cluster of servers and stick with the least privileges possible.

We can't cover nearly every option for every cloud in a relatively short (believe it or not) paper like this, but, for the most part, once you understand these fundamentals and the core differences of working in software defined environments, it isn't nearly as difficult to adapt to new tools and technologies.

Especially once you realize that you start by integrating security into the architecture, instead of trying to layer it on after the fact. <!-- huh... maybe we need to work that in sooner in the paper -rich -->

##Manage Cloud (and Hybrid) Network Security Operations

Building in your security is one thing, but keeping it up to date over time is an entirely different (and harder) problem. Not only to applications and deployments change over time, cloud providers have this pesky habit of "innovating" for "competitive advantage". Someday things might slow down, but it sure isn't going to be within the lifetime of this particular paper.

Here are some suggestions on managing cloud network security for the long haul:

###Organization and Staffing

* have cloud experts on the network security team, trained for the platforms you support.
* They don't need to be new people, and depending on your scale this doesn't need to be their full-time focus, but you definitely need the skills.
* We suggest you build your team with both security architects (to help in design) and operators (to implement and fix).
* Cloud projects occur outside the constraints of your data center, including normal operations, which means you might need to make some organizational changes so security is engaged in projects. A security representative should be assigned and integrated on each cloud project. Think about how things normally work -- someone starts a new project and security gets called when they need access or firewall rule changes. With cloud computing, network security isn't blocking anything (unless they need access to an on-premise resource) and entire projects can happen without security or ops every being directly involved. You need to adapt your policies and org structure to minimize this risk. For example, work with procurement to require a security evaluation/consultation before any new cloud account is opened.
* Since so much of cloud network security relies on architecture, it isn't just important to have an architect on the team, it's critical they are engaged early in projects. It also goes without saying that this should be a collaborative role. Don't merely write out some pre-approved architectures and then try and force everyone to work within those constraints. You'll lose that fight before you even know it started.
-------------------RM after here-------------------------------------
###Discovery

We hinted at this in the section above -- one of the first challenges is to find all the cloud projects, and then keep finding the new ones over time. Then you need to enumerate the existing cloud network security controls. Here are a couple ways we've seen clients successfully keep tabs on cloud computing:

* If your critical assets (such as the customer database) are well locked down, you can use this to control cloud projects. If they want access to the data/application/whatever, they need to meet your security requirements.
* Procurement and accounting are the next best options. At some point someone needs to pay the (cloud) piper, and you can work with accounting to identify payments to cloud providers and tie them back to the teams involved. Just make sure you differentiate between those credit card charges to Amazon for office supplies from the one to replicate your entire datacenter in AWS.
* Hybrid connections to your data center are clearly pretty easy to track using established process. Unless you let random employees plug in VPN routers.
* Lastly, we suppose you can always set a policy that says "don't cloud without telling us". I mean, if you trust your people and all. It could work. Maybe. It's probably good to have to keep the auditors happy anyway.

The next discovery challenge is to figure out how the cloud networks are architected and secured:

* First, always start with the project team. Sit down with them and perform an architecture and implementation review.
* It's an early market, but there are some assessment tools that can help. Especially to analyze security groups/network security and compare it to best practices.
* You can use the cloud provider's console in many cases, but most of them don't provide a good overall network view. If you don't have a tool to help, you can use scripting and API calls to pull down the raw configuration and manually analyze it.

###Integrating with Development

In the broadest sense there are two kinds of cloud deployments -- applications you build and run in the cloud (or hybrid), and core infrastructure (like file and mail servers) you transition to cloud. While developers play a central role in the former, they are also often involved in the latter.

Cloud is essentially *software defined everything*. We build and manage all kinds of cloud deployments using code. Even if you start by merely transitioning a few servers into virtual machines on a cloud provider, you will always end up defining and managing much of your environment in code.

This is actually an incredible opportunity for security. Instead of sitting outside the organization and trying to protect things by building external walls, we gain a much greater ability to manage security using the exact same tools development and operations are using to define, build, and run the infrastructure and services. Here are a few key ways to integrate with development and ensure security is integrated:

* Create a handbook of design patterns for the cloud providers you support, including security controls and general requirements. Keep adding new patterns as you work on new projects. Then make this library available to business units and development teams so they know which architectures already have general approval from security.
* A cloud security architect is pretty essential, and this person or team should engage early with development teams to help build security into the initial design. And we hate to have to say it, but their role really needs to be collaborative. Lay down the law with a bunch of requirements that interfere with the project's requirements and they definitely won't be invited back to the table.
* A lot of security can be automated and templated by working with development. For example, monitoring and automation code that can be deployed on projects without the project team having to develop them from scratch. Even integrating third party tools can often be managed programatically.

###Policy Enforcement

Change is constant in cloud computing. The entire founding concept is adjusting capacity (and configuration) to meet changing demands. When we say "enforce policies" we mean that, for a given project, once you design the security you are able to keep it consistent. Just because clouds change all the time doesn't mean it's okay to let a developer drop all the firewalls by mistake.

The key policy enforcement difference between traditional networking and cloud is that, in traditional infrastructure, security has exclusive control over firewalls and other security tools. In cloud, anyone with the proper authorizations with the cloud platform can make those changes. Even applications can potentially change their own infrastructure around them. That's why you need to rely more on automation to detect and manage change.

You lose the single point of control. Heck, your own developers can create entire networks from their desktops. Remember the days when people would occasionally plug in their own wireless router or file server? It's a little like that, and more like them building their own datacenter over lunch. Here are some techniques for managing these changes:

* Use access controls to limit who can change what on a given cloud project. It is pretty typical to allow developers a lot of freedom in their dev environment, but totally lock down any network security changes in production, using IAM features of your cloud provider.
* To the greatest degree possible, try to use cloudprovider-specific templates to define your infrastructure. These files contain a programmatic description of your environment, including complete network and network security configurations. You load them into the cloud platform and it builds the environment for you. This is a very common way to deploy cloud applications, and is essential in organizations using DevOps, since they enforce consistency.
* When this isn't possible, you will need to use a tool or manually pull the network architecture and configuration (including security) and document them. This is your baseline.
* Then you need to automate change monitoring using a tool or the features of your cloud and/or network security provider.:
	* Cloud platforms are slowly adding detection and alerting to security configurations, but it's still early and often manual to set up. This is where cloud-specific training and staffing can really pay off, and there are also some third-party tools to monitor these changes for you.
	* When you use virtual appliances or host security, you don't rely on the cloud provider and you may be able to hook in change management and policy enforcement into your existing approaches. Since these are security specific tools, unlike cloud provider features the security team will often have exclusive access and be responsible for making changes themselves.
* Did we mention automation? We will talk about it more in a minute, but that's really the only way to maintain cloud security over time.

###Normalizing On-Premise and Cloud Security

Organizations have a lot of security requirements for very good reasons, and need to ensure these controls are consistently applied. We also have a tremendous amount of network security experience from decades of running our own networks that are still relevant when moving into the cloud. The challenge is to carry over these requirements and experiences without assuming they work the same in the cloud, or letting them interfere with the benefits of cloud computing.

* Start by translating whatever rules sets you have on-premise into a comparable version in the cloud. This takes a few steps:
	* Figure out which rules should still apply, or which new rules you need. For example, a policy to deny all SSH traffic from the Internet won't work if that's how you manage public cloud servers. Instead, a policy that limits SSH access to your corporate CIDR makes more sense. Another example is the common restriction that back-end servers shouldn't have any Internet access at all, yet they might need it to connect to PaaS components of their own architecture.
	* Then, adjust your policies into enforceable rules-sets. For example, security groups and ACLs work differently, and how you enforce them changes. Instead of setting subnet-based policies with a ton of rules, tie security group policies to instances based on their function. We once encountered a client that tried to recreate very-complex firewall rules sets into security groups, exceeding the rule count limit of their provider. Instead we recommended a set of policies for different categories of instances.
	* Watch out for policies like "deny all traffic from this IP range". Those can be very difficult to enforce in the cloud using native tools, and if you *really* have those requirements you will likely need a network security virtual appliance or host security agent. In many projects we find you can resolve the same level of risk with smarter architectural decisions (e.g. using immutable servers, which we will talk about in a moment).
	* Don't just drop in a virtual appliance because you are used to it and know how to build the rules. Always start with what your cloud provider offers, then layer on additional tools as needed.
	* If you *migrate existing applications to the cloud* the process is a little more complex. You will need to evaluate existing security controls, discover and analyze application dependencies and network requirements, and *then* translate them for a cloud deployment, taking into account all the differences we have been talking about.
* Once you translate the rules, normalize operations. This means having a consistent process to deploy, manage, and monitor your network security over time. Fully covering this is beyond to scope of this research, since it depends on how you manage network security operations today. Just remember that you are trying to blend what you do now with what the cloud projects need, not merely enforce your existing processes onto an entirely new operating model.

We hate to say it (but we will), but this is a process of transition. We find customers that start on a project-by-project basis are more successful since they can learn as they go and build up a repository of knowledge and experience.

###Automation and Immutable Network Security

Cloud security automation isn't merely fodder for another paper; it's an entirely new body of knowledge are are only just beginning to build.

Any organization that moves to cloud in any significant way learns quickly that automation is the only way to survive. Think about it, how else can you manage multiple copies of a single project in different environments, never mind dozens or hundreds of different projects, all running in their own *sets* of cloud accounts across multiple providers.

Then, keep all those projects compliant with regulatory requirements and your internal security policies.

Yeah, it's like that.

But this isn't an unsolvable problem. Every day we see more examples of companies successfully using the cloud, at scale, and staying secure and compliance. Today, in large part, they build their own library of tools and scripts to continually monitor and enforce changes. We also see some emerging tools to help with this management, and we expect to see many more in the near future.

A core developing concept tied to automation is that of *immutable security*, and it's one we have used ourselves.

One of the core problems in security is managing change. We design something, build in security, deploy it, validate that security, and lock everything down. This inevitably drifts as it's patched, updated, improved, and otherwise modified. Immutable security leverages automation, DevOps techniques, and inherent cloud characteristics to break this cycle. To be honest, it's really nothing more than DevOps as applied to security, and all the principles are in wide use already.

For example, an *immutable server* is one that is never logged into and changed in production. If you go back and think about auto scaling we deploy servers based on standard images. Changing one of those servers after deployment doesn't make sense, because those changes aren't in the image, and new versions launched into the auto scale group won't include the changes. Instead, DevOps creates a new image with all changes, then alters the auto scale group rules to deploy new instances based on the new image and, in some cases, kill off the older versions.

In other words, no more patching, no more logging into servers. You take a new, known good state, and completely override what is in production.

Now think about how this applies to network security. We can build templates to automatically deploy entire environments on our cloud providers. We can write network security policies, then override any changes automatically, even across multiple cloud accounts. It pushes the security effort earlier into design and development, but then enables much more consistent enforcement in operations. And we use the exact same toolchain as development and operations to deploy our security controls, instead of trying to build our own on the side and overlay enforcement.

This might seem like an aside, but these automation principles are the cornerstone of real-world cloud security, especially at scale. It's a capability we simply never have in traditional infrastructure where we can't simply stamp out new environments automatically and have to hand-configure everything.

#Design Patterns

To finish off this research it's time to show you want some of this looks like. Here are some practical design patterns based on projects we've worked on. The examples are specific to Amazon Web Services and Microsoft Azure as opposed to generic templates. Generic patterns are harder to explain, and we would rather you understand what these look like in the real world.



















[1]:	http://www.youtube.com/watch?v=Zd5hsL-JNY4
[2]:	https://en.wikipedia.org/wiki/Virtual_LAN
