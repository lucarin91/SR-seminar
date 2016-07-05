# SR - Seminar
#### Security in the MicroServices Architecture

<small>Created by <a href="http://www.github.com/lucarin91">Luca Rinaldi</a></small>



## Microservices Architecture
Is an Architecture based of separeted services that implement a small part of the application.

They have to be autonomus (they can be deployed as a single machine, virtual machine, container or a process)


### Advantages
- Composability
- Scaling
- Resilience
- Technology Heterogeneity
- Ease of Deployment


### Introuced problems ( Disadvantages )
- Increased Resource use
- Increase Network communication
- Marshalling and Un marshalling
- Network Security

note:
- **Increased Resource** use - Initial investment to run these applications are high because all the independently running components need their own runtime containers with more memory and CPU.

- **Increase Network communication** - Independently running components interact with each other over a network. Such systems require reliable and fast network connections.

- **Marshalling and Un marshalling** - When one component needs data from another component, the sender marshals the data in some standard from its internal representation, while the receiver un-marshalls data into its own representation before use. This definitely requires more processing compared to conventional application architecture.

- **Network Security** - Inter Service Communication needs to be secured to avoid any inter communication security breach. Due to several moving parts, these applications are more prone to security vulnerabilities.



### Security issue
