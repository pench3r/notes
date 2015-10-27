### **Chapter 7. Socket Options**

### Introduction

There are various ways to get and set the options that affect a socket:

* The `getsockopt` and `setsockopt` functions.
* The `fcntl` function, which is the POSIX way to set a socket for nonblocking I/O, signal-driven I/O, and to set the owner of a socket.
* The `ioctl` function.

### `getsockopt` and `setsockopt` Functions

These two functions apply only to sockets:

```c
#include <sys/socket.h>

int getsockopt(int sockfd, int level, int optname, void *optval, socklen_t *optlen);
int setsockopt(int sockfd, int level, int optname, const void *optval socklen_t optlen);

/* Both return: 0 if OK,–1 on error */
```

Arguments:

* *sockfd* must refer to an open socket descriptor.
* *level* specifies the code in the system that interprets the option: the general socket code or some protocol-specific code (e.g., IPv4, IPv6, TCP, or SCTP).
* *optval* is a pointer to a variable from which the new value of the option is fetched by `setsockopt`, or into which the current value of the option is stored by `getsockopt`. The size of this variable is specified by the final argument *optlen*, as a value for `setsockopt` and as a value-result for `getsockopt`.

The following table lists socket and IP-layer socket options for `getsockopt` and `setsockopt`.

[![Figure 7.1. Summary of socket and IP-layer socket options for getsockopt and setsockopt.](figure_7.1.png)](figure_7.1.png "Figure 7.1. Summary of socket and IP-layer socket options for getsockopt and setsockopt.")

The following table lists transport-layer socket options.

[![Figure 7.2. Summary of transport-layer socket options.](figure_7.2.png)](figure_7.2.png "Figure 7.2. Summary of transport-layer socket options.")

There are two basic types of options:

* **Flags**: binary options that enable or disable a certain feature (flags)
* **Values**: options that fetch and return specific values that we can either set or examine.

The column labeled "Flag" specifies a flag option:

* `getsockopt`: `*optval` is an integer. The value returned in `*optval` is zero if the option is disabled, or nonzero if the option is enabled.
* `setsockopt`: it requires a nonzero `*optval` to turn the option on, and a zero value to turn the option off.

If the "Flag" column does not contain a block dot, then the option is used to pass a value of the specified datatype between the user process and the system.

### Checking if an Option Is Supported and Obtaining the Default

[sockopt/checkopts.c](https://github.com/shichao-an/unpv13e/blob/master/sockopt/checkopts.c)

### Socket States

The following socket options are inherited by a connected TCP socket from the listening socket:

* `SO_DEBUG`
* `SO_DONTROUTE`
* `SO_KEEPALIVE`
* `SO_LINGER`
* `SO_OOBINLINE`
* `SO_RCVBUF`
* `SO_RCVLOWAT`
* `SO_SNDBUF`
* `SO_SNDLOWAT`
* `TCP_MAXSEG`
* `TCP_NODELAY`

This is important with TCP because the connected socket is not returned to a server by `accept` until the three-way handshake is completed by the TCP layer. <u>To ensure that one of these socket options is set for the connected socket when the three-way handshake completes, we must set that option for the listening socket.</u>

### Generic Socket Options

Generic socket options are protocol-independent (they are handled by the protocol-independent code within the kernel, not by one particular protocol module such as IPv4), but some of the options apply to only certain types of sockets. For example, even though the `SO_BROADCAST` socket option is called "generic," it applies only to datagram sockets.

### IPv4 Socket Options

#### `SO_BROADCAST` Socket Option

This option enables or disables the ability of the process to send broadcast messages. Broadcasting is supported for only datagram sockets and only on networks that support the concept of a broadcast message (e.g., Ethernet, token ring, etc.). You cannot broadcast on a point-to-point link or any connection-based transport protocol such as SCTP or TCP.

Since an application must set this socket option before sending a broadcast datagram, it prevents a process from sending a broadcast when the application was never designed to broadcast. For example, a UDP application might take the destination IP address as a command-line argument, but the application never intended for a user to type in a broadcast address. Rather than forcing the application to try to determine if a given address is a broadcast address or not, the test is in the kernel: If the destination address is a broadcast address and this socket option is not set, `EACCES` is returned.

#### `SO_DEBUG` Socket Option

This option is supported only by TCP. When enabled for a TCP socket, the kernel keeps track of detailed information about all the packets sent or received by TCP for the socket. These are kept in a circular buffer within the kernel that can be examined with the `trpt` program.

### ICMPv6 Socket Option

### IPv6 Socket Options

### TCP Socket Options