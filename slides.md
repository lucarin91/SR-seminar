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



## Real-World Examples
![netflix](img/netflix.jpg)<!-- .element: style="float" -->
![hailo](img/hailo.png)



## Security issue
- Authentication and Authorization
- Greater Surface Attack Area
- Heterogeneity
- Network Complexity
- Man-in-the-middle
- Trust



### Authentication and Authorization
![img](img/monolithic_request_flow.png)
Each micro-services have to ensure that the request is made by an authenticated client with the corrent rigths.

to avoid repeted work the it can be used SSO Gatway that is the only one that communicate with the identity service.

After the sig-in it transmit to the other services a token authorize the client.


#### Thecnologie
- OpenID
- SAML
- JWT



### Greater Surface Attack Area
in monolitic application all the component talk by internal data structure of the used language.

in the microservices infrastructure all serices expose common API independed from the programming language

(the services can in principle can be accessed by the external and in every order)



### Heterogeneity
The application can be made by a very large number of services that can also not be know in advance.
No common security infrastructure, different Trusted Computing Base



### Network Complexity
Such an intrinsic complexity
determines an ever-increasing difficulty in debugging, monitoring, auditing, and forensic
analysis of the entire application. Attackers could exploit this complexity to launch attacks
against applications.
