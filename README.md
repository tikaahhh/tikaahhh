# HOW TO ESTABLISH SOCKET ON THE SERVER SIDE IN C

## What is the Client-Server Model?
  Most interprocess communication relies on the client-server model, which involves two processes communicating with each other. In this model, the client process connects to the server process, typically to request information. A useful analogy is a person calling another person on the phone.

It's important to note that the client must be aware of the server's existence and address, while the server does not need to know the client's address or even its existence before the connection is made. Additionally, once the connection is established, both the client and the server can exchange information. The system calls for establishing a connection are somewhat different for the client and the server, but both involve the basic construct of a socket.

## Socket
  A socket is one endpoint of an interprocess communication channel that can be named and addressed within a network. Sockets are valuable for both standalone and network applications, enabling information exchange between processes on the same machine or across a network. They facilitate distribution tasks to the most efficient machine and provide easy access to centralized data.

### Type Of Socket

* **Datagram Sockets**
  
  Datagram sockets enable processes to utilize the User Datagram Protocol (UDP) for communication. This involves a two-way flow of messages, where messages may be received in a different order than they were sent and duplicates might occur. Datagram sockets maintain their message boundaries and are designated by the socket type SOCK_DGRAM. 
  
* **Stream Sockets**
  
  A stream socket enables processes to utilize the Transfer Control Protocol (TCP) for communication. It facilitates a sequenced, reliable, and bidirectional flow of data. Once a connection is established, data can be both read from and written to these sockets as a byte stream. Stream sockets are denoted by the socket type SOCK_STREAM.
  
* **Raw Sockets**
  
  Raw Sockets offer user access to the Internet Control Message Protocol (ICMP). While raw sockets are not commonly employed in most applications, they share similarities with datagram-oriented sockets, with their characteristics reliant on the interfaces. Raw sockets are primarily utilized for developing new communication protocols or accessing additional functionalities of existing protocols. Access to raw sockets is restricted to superusers. The socket type for raw sockets is SOCK_RAW.

## Definition of server
  A server is a program operating on a remote machine that offers services to clients. Upon receiving a request from a client, the server becomes ready to handle incoming requests, but it does not initiate any service on its own. Essentially, a server program is designed to run indefinitely once it starts, continuing to operate unless an issue occurs. It remains idle, waiting for client requests, and when a request is received, the server responds accordingly.

### How to establish a socket on the *server* side
  1. Create a socket with the socket() system call
  2. Bind the socket to an address using the bind() system call. For a server socket on the   Internet, an address consists of a port number on the host machine.
  3. Listen for connections with the listen() system call
  4. Accept a connection with the accept() system call. This call typically blocks until a client connects with the server.
  5. Send and receive data

### Sample Code

Here is the [code for Server Program](https://github.com/user-attachments/files/15744057/Code.for.Server.Program.txt)

![1](https://github.com/tikaahhh/tikaahhh/assets/167418213/750a2890-7c3c-4c51-8150-38fbdbfab801)

This header file contains definitions of a number of data types used in system calls. These types are used in the next two include files. The header file socket.h includes several definitions of structures needed for sockets.The header file netinet/in.h contains constants and structures needed for internet domain addresses.

![2](https://github.com/tikaahhh/tikaahhh/assets/167418213/650f4d1a-7f97-4d60-85f1-b6da2149f877)

This function is called when a system call fails. It displays a message about the error on stderr and then aborts the program.

![3](https://github.com/tikaahhh/tikaahhh/assets/167418213/788339c6-fc8e-44de-92a0-2bba8cceb704)

*sockfd* and **newsockfd** are file descriptors, i.e. array subscripts into the file descriptor table . These two variables store the values returned by the socket system call and the accept system call.

*portno* stores the port number on which the server accepts connections.

*clilen* stores the size of the address of the client. This is needed for the accept system call.

*n* is the return value for the read() and write() calls; i.e. it contains the number of characters read or written.

*char buffer[256]* reads characters from the socket connection into this buffer.

*A sockaddr_in* is a structure containing an internet address. This structure is defined in <netinet/in.h>. The variable **serv_addr** will contain the address of the server, and **cli_addr** will contain the address of the client which connects to the server.

![4](https://github.com/tikaahhh/tikaahhh/assets/167418213/9ebe0877-76fe-4698-b112-e81634b0bce5)

The user needs to pass in the port number on which the server will accept connections as an argument. This code displays an error message if the user fails to do this.


![5](https://github.com/tikaahhh/tikaahhh/assets/167418213/2701df14-1a53-49a4-a91f-fce4cf716d6f)

The socket() system call creates a new socket. It takes *three* arguments. The first is the address domain of the socket. Recall that there are two possible address domains, the unix domain for two processes which share a common file system, and the Internet domain for any two hosts on the Internet. The symbol constant AF_UNIX is used for the former, and AF_INET for the latter (there are actually many other options which can be used here for specialized purposes).

**The second argument** fds is the type of socket. Recall that there are two choices here, a stream socket in which characters are read in a continuous stream as if from a file or pipe, and a datagram socket, in which messages are read in chunks. The two symbolic constants are **SOCK_STREAM** and **SOCK_DGRAM**. 

**The third argument** is the protocol. If this argument is zero, the operating system will choose the most appropriate protocol. It will choose TCP for stream sockets and UDP for datagram sockets.

![6](https://github.com/tikaahhh/tikaahhh/assets/167418213/01be4e86-89c5-41dc-ab7b-ce12f74fc438)

The function bzero() sets all values in a buffer to zero. It takes two arguments, the first is a pointer to the buffer and the second is the size of the buffer. Thus, this line initializes serv_addr to zeros. The port number on which the server will listen for connections is passed in as an argument, and this statement uses the atoi() function to convert this from a string of digits to an integer. The variable serv_addr is a structure of type struct sockaddr_in. This structure has four fields. The first field is short sin_family, which contains a code for the address family. It should always be set to the symbolic constant AF_INET. The second field of serv_addr is unsigned short sin_port, which contains the port number. However, instead of simply copying the port number to this field, it is necessary to convert this to network byte order using the function htons() which converts a port number in host byte order to a port number in network byte order. The third field of sockaddr_in is a structure of type struct in_addr which contains only a single field unsigned long s_addr. This field contains the IP address of the host. For server code, this will always be the IP address of the machine on which the server is running, and there is a symbolic constant INADDR_ANY which gets this address.

![7](https://github.com/tikaahhh/tikaahhh/assets/167418213/6ba848df-0ba5-43b8-90ca-432cee4fed4f)

The bind() system call binds a socket to an address, in this case, the address of the current host and port number on which the server will run. It takes three arguments, the socket file descriptor, the address to which is bound, and the size of the address to which it is bound. The second argument is a pointer to a structure of type sockaddr, but what is passed in is a structure of type sockaddr_in, and so this must be cast to the correct type. This can fail for a number of reasons, the most obvious being that this socket is already in use on this machine.

![8](https://github.com/tikaahhh/tikaahhh/assets/167418213/00cb61c6-9405-473a-9333-8a627838ade1)

The listen system call allows the process to listen on the socket for connections. The first argument is the socket file descriptor, and the second is the size of the backlog queue, i.e., the number of connections that can be waiting while the process is handling a particular connection. This should be set to 5, the maximum size permitted by most systems. If the first argument is a valid socket, this call cannot fail, and so the code doesn't check for errors.  

![9](https://github.com/tikaahhh/tikaahhh/assets/167418213/bf0ca51c-58b5-4e1e-8f70-82f92e4706c0)

The accept() system call causes the process to block until a client connects to the server. Thus, it wakes up the process when a connection from a client has been successfully established. It returns a new file descriptor, and all communication on this connection should be done using the new file descriptor. The second argument is a reference pointer to the address of the client on the other end of the connection, and the third argument is the size of this structure.

![10](https://github.com/tikaahhh/tikaahhh/assets/167418213/1ceb1f6e-7fe6-4d3b-9259-d5815c5c1aea)

Note that we would only get to this point after a client has successfully connected to our server. This code initializes the buffer using the bzero() function, and then reads from the socket. Note that the read call uses the new file descriptor, the one returned by accept(), not the original file descriptor returned by socket(). Note also that the read() will block until there is something for it to read in the socket, i.e. after the client has executed a write(). It will read either the total number of characters in the socket or 255, whichever is less, and return the number of characters read.

![11](https://github.com/tikaahhh/tikaahhh/assets/167418213/6d9bd63c-3f7f-4967-a59e-c8f2d79c6bc1)

Once a connection has been established, both ends can both read and write to the connection. Naturally, everything written by the client will be read by the server, and everything written by the server will be read by the client. This code simply writes a short message to the client. The last argument of write is the size of the message.

![12](https://github.com/tikaahhh/tikaahhh/assets/167418213/33c4a830-6f8c-42b6-ab30-100a4f9f7434)

This terminates the main and thus the program. Since the main was declared to be of type int as specified by the ASCII standard, many compilers complain if it does not return anything.

`However, this sample code is limited to handling a single connection before terminating.`

### Enhancing Server Code

An efficient server should be capable of handling multiple simultaneous connections, each within its own process. This can be achieved by creating a new process to manage each incoming connection.

To enable the server to manage multiple simultaneous connections, we implement the following modifications to the code.:

  1. Put the accept statement and the following code in an infinite loop.
  2. After a connection is established, call fork() to create a new process.
  3. The child process will close sockfd and call dostuff, passing the new socket file descriptor as an argument. When the two processes have completed their conversation, as indicated by dostuff() returning, this process simply exits.
  4. The parent process closes newsockfd. Because all of this code is in an infinite loop, it will return to the accept statement to wait for the next connection.

Here is the improved code

![13](https://github.com/tikaahhh/tikaahhh/assets/167418213/bf122b3b-cfa4-4d31-9423-655fc64f8e80)

Full [improved Code for Server Program](https://github.com/user-attachments/files/15744060/Improved.Code.for.Server.Program.txt) is here.

## Stream Sockets VS Datagram Sockets

The provided code demonstrates a stream socket in the Internet Domain, widely utilized by users. Conversely, users may opt for a datagram socket when only a single message is transmitted from the server to the clients, and another single message is sent back. Several differences exist between stream and datagram sockets in various aspects.

### Reliability
  Streams enhance reliability by using TCP, if a message isn't acknowledged, the protocol automatically detects the loss and retransmits the message without either end being aware of it. On the other hand, datagrams employing UDP lack reliability. Therefore, if a packet is lost on the Internet, the sender will not receive notification of the loss.

### Message Boundaries
  In stream sockets, where the receiver can read the message in smaller or larger chunks as needed. Meanwhile in datagram sockets, the message is kept intact as sent by the sender. For instance, if the sender sends a 100-byte datagram, the receiver must read all 100 bytes at once. 
  
### Communication
  The communication in stream sockets is done using more generic read() and write() rather than the special system calls sendto() and receivefrom().
  
### Overhead
  Unlike stream sockets, datagram sockets have less overhead because they don't require establishing and breaking connections, and packets don't need acknowledgement. That's why they're commonly used for short services like providing the time of day.
