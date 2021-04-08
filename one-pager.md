# MessageFirst: a reliable user API for message-oriented network workloads
## Problem description
There are two philosophies (among others) on sending data in computer networks, those two being stream-based and message-based. In a stream based protocol, data is sent in a byte stream, with no notion of a complete unit of transmission. Alternatively, in a message-based protocol, there is a notion of a “message”, a complete unit of transmission. Message-based protocols send and receive strictly complete messages.

Although stream-based protocols are good for large file transfers, there is increasingly a number of use-cases that utilize short lived connections where small messages are sent and received. Applications that utilise short lived connections include but are not limited to: Remote procedure calls (RPC), HTTP, and key-value stores that use small objects. These types of workloads are increasingly common in data centers. TCP, being a byte stream protocol is not designed with these workloads in mind and thus faces challenges when performing these workloads in data centres. There is overhead associated with setting up the connection which becomes more significant as the length of the connection decreases (https://www.usenix.org/conference/osdi12/technical-sessions/presentation/han). TCP protocols are also very CPU-intensive for smaller workloads  (same citation as above).

Technologies such as MegaPipe aim to solve these problems. Megapipe exposes an API optimized for message-oriented workloads, and also does optimizations under the hood such as assigning a bi-directional message “channel” to a CPU core so that performance scales with the number of CPU cores. It also uses lightweight sockets which remove some overhead associated with file descriptors, and batches system calls.

Another reason message-oriented workloads are generally more suited to UDP as they utilise short connections. The connection overhead of TCP gets more significant as the connection time decreases as it makes up a greater proportion of the connection/transmission time. MessageFirst will utilise TCP, thus adding reliability properties to message-oriented workloads.
## Design 
For my thesis project, I will design a specification for a user API for writing applications with message-oriented network workloads. This API would be centered around the abstraction of a message, which is a unit of transmission of data, with an expectation of some amount of data being sent back from the receiver.

To achieve this, the design will have two main components: data structures that represent this unit of transmission, and function calls that operate on these data structures to populate, modify, send and receive them. These functions will block until a full transmission is sent and received, supporting the notion of a message.
## Research question(s)
The main thing that this thesis will be about is investigating the performance overhead using TCP in a message-oriented workload. In addition, this thesis will take these findings and apply them to the design of an API for message-oriented workloads and implement this design in Linux using the socket interface.

Thus, it presents research questions like:

* What is the overhead of a message versus a stream oriented API for message oriented workloads?

* What parameters exist in the TCP socket implementation that can be tuned to increase performance in message-oriented workloads?
