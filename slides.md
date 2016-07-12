## Security in the Micro-Services Architecture
#### SR - Seminar

<small>Created by <a href="http://lucar.in">Luca Rinaldi</a></small>



## Agenda
- Intro to microservices

- Security problems in microservices

- Security-as-a-Service approach for microservice

- Conclusion



## Basic Definitions
A **monolithic application** is a software composed of modules that are not independent from the application to which they belong.

A **microservice** is a minimal independent process interacting via messages.

A **microservice architecture** is a distributed application where all its modules are microservices.



## Microservice Architecture
Communication is performed by **RESTful API** or **message broker** services.

Each microservice is **autonomous** and **domain specific**.

They can be deployed as a single machine, virtual machine, container or process.


### Advantages
- Composability
- Scaling
- Resilience
- Technology Heterogeneity
- Ease of Deployment


### Introduced problems ( Disadvantages )
- Increased resource use
- Increase Network communication
- Marshalling and Un-marshalling
- Network and Security issue

note:
- **Increased Resource** use - Initial investment to run these applications are high because all the independently running components need their own runtime containers with more memory and CPU.

- **Increase Network communication** - Independently running components interact with each other over a network. Such systems require reliable and fast network connections.

- **Marshalling and Un-marshalling** - When one component needs data from another component, the sender marshals the data in some standard from its internal representation, while the receiver un-marshalls data into its own representation before use. This definitely requires more processing compared to conventional application architecture.

- **Network Security** - Inter Service Communication needs to be secured to avoid any inter communication security breach. Due to several moving parts, these applications are more prone to security vulnerabilities.



## Real-World Examples: [Hailo](https://www.hailoapp.com)
![hail-architecture](img/hail-small.png)


## Real-World Examples: [Tyro](https://tyro.com)
![tyro](img/tyro.png)



## Security issue
- Greater surface attack area
- Heterogeneity
- Network complexity
- Authentication and authorization
- Secure the communications
- Trust



## Greater Surface Attack Area
In the microservice infrastructure all services expose API that are intependent from the programming language.

These services can in principle be accessed by the external.



## Heterogeneity
The application can be made out of a very large number of services that can also be unknown in advance.

No common security infrastructure, different Trusted Computing Base.



## Network Complexity
The architecture bring difficulty in debugging, monitoring, auditing, and forensic analysis of the entire application.

Attackers could exploit this complexity to attempt attacks against applications.



## Authentication and Authorization
![monolithic_flow](img/monolithic_request_flow.png) ![img](img/microservices_incorrect_flow.png)

Each micro-service has to ensure that the request cames from an authenticated client with the correct rights.

To avoid repeated work an SSO Gateway can be used, to login and check users authorization.


### More in detail
There is not an actual standard, but the main idea is to use a token system.

The most used technologies are:
- JWT, a token system.
- OpenID, decentralized authentication protocol.
- OAuth, a delegation protocol.



## Secure the communications
The communication between the services should be secure.

Adopted technology:
- HTTPS
- HMAC Over HTTP



## Trust
We can't assume that a microservice trustworthy.

Encrypt and certificate the communication is not enough, so that a compromised service can't act against us.


### Confused deputy problem
When a malicious party can trick a deputy service into making calls to a downstream service on his behalf that he shouldn't be able to.


### a Netflix vulnerability
A subdomain of Netflix was compromised so taht an adversary:
- can serve any content in the context of netflix.com.
- was able to tamper with authenticated Netflix subscribers and their data.

note:
since Netflix allowed all users cookies to be accessed from any subdomain, an adversary controlling a subdomain was able to tamper with authenticated Netflix subscribers and their data.



## Security-as-a-Service
In the paper "Security-as-a-Service for Microservices-Based Cloud Applications" is proposed a solution to monitor and analyse microservice requests to ensure some communication policy.



## The DVD retail example
![dvd_rental](img/DVD_rental.png)

![dvd_rental-microservices](img/DVD_rental-microservices.png)

note:
- Step 1: customer sends a request to Contract-Update service to create a rental contract.
- Step 2: Contract-Update invokes a method of User-Update to bill the customer’s account.
- Step 3: Contract-Update places a RPC message containing the DVD info on the message queue. The message is consumed by DVD-Update.
- Step 4: DVD-Update invokes Shipping to ship the DVD.
- Step 5 and 6: When the customer returns the DVD, DVD-Return updates customer account and DVD repository.



## One possible problem
![dvd_rental-microservices](img/DVD_rental-microservices.png)

note:
A compromised `Contract-Update` may send modified requests to `User-Update` to cause user account to be arbitrarily charged.

A compromised `DVD-Update` service may consume and then delete messages on the queue without actually shipping out DVDs, causing a denial of service attack, and so on.



## Monitor the network
The solution must have these characteristics:
- Completeness
- Tamper resistance
- Flexibility
- Efficiency

note:
- Completeness: the solution should be able to monitor and enforce over both internal and external network events of a cloud application.
- Tamper resistance: the solution should work even if individual application VMs are under adversary’s control.
- Flexibility: the solution should allow applications to specify their own policies over the kind of network events they want to monitor and enforce policies on.
- Efficiency: the solution should have minimal impact on network and CPU resources consumed.



## Design
Put the monitoring part outside the business logic.

Create a security VM that can analyse and monitor the flow coming from the application VM.

All the network event of the application VM can be redirected by the SDN of the cloud infrastructure.

note:
this solution is tamper-prof because the attacker can't access the security VM unless there is same vulnerability in the VM hypervisor.



## FlowTap Primitive
To avoid custom network configuration it is possible to define a new primitive:
```
FlowTap (SRC, DST, Flow_Syntax, Action)
```

- **SRC**, the port of the source VM
- **DST**, the port of the destination VM
- **Flow_Syntax**, identifies a specific flow to tap  
- **Action**, forwarding or redirect

Flow_Syntax Example (Monitor incoming HTTP requests):
```(python)
nw_src = 0.0.0.0/0; nw_proto = TCP; tp_dst = 80
```

note:
- **forwarding**: relevant network events will be copied and forwarded to the security VM, with the original network events still delivered to their intended destination.
- **redirecting**: the relevant network events will be directed to the security VM, and depending the decisions made by security monitor, the network events may or may not reach the their intended destination.

Flow_Syntax:
```
in_port, dl_vlan, dl_vlan_pcp, dl_src, dl_dst, dl_type, nw_src,
nw_dst, nw_proto, nw_tos, nw_ecn, nw_ttl, tp_src, tp_dst,
icmp_type, icmp_code, table, metadata, vlan_tci, ip_frag,
arp_sha, arp_tha, ipv6_src, ipv6_dst, ipv6_label, nd_target,
nd_sll, nd_tll, tun_id, tun_src, tun_dst, reg
```


### ftc compiler
A tool to translate the policies written in Datalog to a set of FlowTap call.

It can also dynamically compile the same policy into different set of FlowTap calls that maximise the efficiency of the system, based on CPU usage and network load.

note:
Datalog is a declarative logic programming language that syntactically is a subset of Prolog. It is often used as a query language for deductive databases. In recent years, Datalog has found new application in data integration, information extraction, networking, program analysis, security, and cloud computing.


### Implementation
FlowTap is implemented on the **OpenStack Icehouse** release.

By modifying the virtual routing devices:

- The integration bridge (`br-int`) that connects to VMs

- The tunneling bridge (`br-tun`) that tunnels the VM traffic across cloud nodes.


### How it works
The modified `br-int` process each packet at the following:
1. Compare the flow with the flow syntax

2. If it matches, it is duplicated or taken as it is

3. Its destination MAC address is rewritten to the security VM MAC

4. It is resubmitted to the `br-int` if the security VM is on the same node, or to the `br-tun` for tunneling.



## Evalutation
![setup](img/setup.png)
<small>

| Scenario             |  (a) |  (b) |  (c)  |  (d)  |
|:---------------------|:----:|:----:|:-----:|:-----:|
| Baseline (mbps)      | 2600 | 2600 | 12000 | 12000 |
| FlowTap (mbps)       | 2100 | 2600 | 5100  | 9100  |
| Throughput reduction | 19%  | 0%   | 57%   | 24%   |

</small>



## Problems of this approach
- Generates a lot of additional traffic in the network.

- Need an infrastructure implementation *(cloud providers have to adopt it)*.

- difficulties to deploy the security VM machine in the optimal nodes.



## Other approaches
- Infrastructure and/or platform-based security approaches, such as **VMware vCNS**, **NSX**.
They implement their physical counterpart (like switches, firewalls and routers).

- Application-based security software like **FIDO** by Netflix.
They analyze API-level behaviors within cloud applications and detect anomalous patterns.

note:
Infrastructure and/or platform-based security approaches, such as VMware vCNS [18], NSX [19], etc., extend the hypervisor/platform to provide distributed, and sometimes inline, monitoring for the cloud applications.
They mostly try to implement monitoring techniques inspired by their physical counterparts (e.g. SPAN and/or TAP ports) in distributed virtual switches [20], firewalls [21] and routers [22]. Our work introduces flexibility to these techniques with more fine grained and dynamic control, and augments microservice-specific context to address security issues that are important for this architecture.

Application-based security approaches, such as in Netflix Fido analyze API-level behaviors within cloud applications to build application profiles and then use the profiles to detect anomalous patterns.
Possible problems:
- the hooks are inside the virtual machine and that can be compressed
- lacks of visibility in the underlying infrastructure



## Conclusion
The microservice architecture is a style that is increasingly gaining popularity both in academia and in the industrial world.

Its not a totally new approach but actually an improvement of the well known OO and SOA.

Standardization and research should work to create a more robust infrastructure to build secure and scalable microservices.



## References
<div style="font-size: 24px;">
- Newman, S., 2015. **Building Microservices.** "O'Reilly Media, Inc."

- Sun, Y., Nanda, S. and Jaeger, T., 2015, November. **Security-as-a-Service for Microservices-Based Cloud Applications.** In 2015 IEEE 7th International Conference on Cloud Computing Technology and Science (CloudCom) (pp. 50-57). IEEE.

- Dragoni, N., Giallorenzo, S., Lafuente, A.L., Mazzara, M., Montesi, F., Mustafin, R. and Safina, L., 2016. **Microservices: yesterday, today, and tomorrow.** arXiv preprint arXiv:1606.04036.

- Nordic APIs. 2016. **How To Control User Identity Within Microservices** | Nordic APIs |. [ONLINE] Available at: http://nordicapis.com/how-to-control-user-identity-within-microservices/. [Accessed 10 July 2016].

- Speaker Deck. 2016. **Security for Microservices with Spring** // Speaker Deck. [ONLINE] Available at: https://speakerdeck.com/dsyer/security-for-microservices-with-spring. [Accessed 10 July 2016].
