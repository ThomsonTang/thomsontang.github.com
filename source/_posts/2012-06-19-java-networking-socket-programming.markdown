---
layout: post
title: "Java Networking Programming--socket"
date: 2012-06-19 17:23
comments: true
categories: [Java, Networking] 
---

The *java.net* package of the JAVASE APIs contains a collections of classes and interfaces that provide the low-level communication details, allowing you to write programs that focus on soloving the problem at hand.  
URLs and URLConnections provide relatively high-level mechanism for accessing resources on the Internet. Sometimes you need to write programs that require low-level network communication, for example, when you want to write a client-server application.  
<!-- more -->

TCP/IP and UDP/IP communications
--------------------------------
The *java.net* package provides support for the two common network protocols:   

* **UDP:** UDP stands for User Datagram Protocol, is a connection-less protocol, meaning that each time you send datagrams, you also need to send the local socket descriptor and the receiving socket's address. As you can tell, additional data must be sent each time a communication is made.  

* **TCP:** TCP stands for Transfer Control Protocol, is a connection-oriented protocol. In order to do communication, a connection must be established between the pair of sockets first. It allows for reliable communication between two applications. TCP is typically used over the Internet Protocol, which is referred to as TCP/IP.

Socket
----------
A socket is one endpoint of a two-way communication link between two programs running on the network. A socket is bound to a port number so that the TCP layer can identify the application that data is destined to be sent. An endpoint is a combination of an IP address and a port number. Every TCP connection can be uniquely indentified by its two endpoint. That way you can have multiple connections between your host and the server. Normally, a server runs on a specific computer and has a socket that is bound to a specific port number. The server just waits, listen to the socket for a client to make a connection request.  

* client-side: it knows the hostname of the machine on which the server is running and the port number on which the server is listening. The client also needs to identify itself to the server so it binds to a local port number that it will use during the connection. This is usually assigned by the system.  

![socket-connection-request]

* server-side: If everything goes well, the server accepts the connection. Upon acceptance, the server gets a new socket bound to the same lcoal port and also has its remote endpoint set to the address and port of the client. It needs a new socket so that it can continue to listen to the original socket for connection requests while tending to the needs of the connected client.

![socket-connection]

On the client side, if the conenction is accepted, a socket is successfully created and the client can use the socket to communicate with the server. The client and server can now communicate by writing to or reading from their socket.

Socket Programming
-------------------
The *java.net.Socket* class represents a socket, and the *java.net.ServerSocket* class provides a mechanism for the server program to listen for clients and establish connections with them.  
The following steps occur when establishing a TCP connection between two computers using sockets:

* The server instantiates a ServerSocket object, denoting which port number communi-cation is to occur on.
* The server invokes the accept() method of the SeverSocket class. This method waits until a client connects to the server on the given port.
* After the server is waiting, a client instantiates a Socket object, specifying the server name and port number to connect to.
* The constructor of the Socket class attempts to connect the client to the specified server and port number. if communication is established, the client now has a Socket object capable of communicating with the server.
* On the server side, the accept() method returns a reference to a new socket on the server that is connected to the client's socket.

After the connections are estabished, communication can occur using I/O streams. Each socket has both an OutputStream and an InputStream. The client's OutputStream is connected to the server's InputStream, and the client's InputStream is connected to the server's OutputStream.

Example
--------
The following client program that connects to a server by using a socket and sends a hello, and then waits for a response.

{% codeblock HelloClient.java lang:java %}
public class HelloClient {
    public static void main(String[] args) throws IOException {
        String serverName = args[0];
        int port = Integer.parseInt(args[1]);

        Socket client = null;
        PrintWriter out = null;
        BufferedReader in = null;
        try {
            System.out.println("Connecting to " + serverName + " on port " + port);

            client = new Socket(serverName, port);
            System.out.println("Connected to " + client.getRemoteSocketAddress());

            out = new PrintWriter(client.getOutputStream(), true);
            out.println("Hello from " + client.getLocalSocketAddress());

            in = new BufferedReader(new InputStreamReader(client.getInputStream()));
            System.out.println("Server says: " + in.readLine());
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            out.close();
            in.close();
            client.close();
        }
    }
}
{% endcodeblock %}

The following program is an example of a server application that uses the Socket class to listen for clients on a port number specified by a command-line argument.

{% codeblock HelloServer.java lang:java %}
public class HelloServer extends Thread {
    private ServerSocket serverSocket;

    public HelloServer(int port) throws IOException {
        serverSocket = new ServerSocket(port);
        serverSocket.setSoTimeout(100000);
    }

    public void run() {
        Socket server;
        BufferedReader in;
        PrintWriter out;

        while (true) {
            try {
                System.out.println("Waiting for client on port " + serverSocket.getLocalPort() + "...");

                server = serverSocket.accept();
                System.out.println("connected to " + server.getRemoteSocketAddress());

                in = new BufferedReader(new InputStreamReader(server.getInputStream()));
                System.out.println(in.readLine());

                out = new PrintWriter(server.getOutputStream(), true);
                out.println("Thank you for connecting to " + server.getLocalSocketAddress() + "\nBye!!");

                server.close();
            } catch (IOException e) {
                e.printStackTrace();
                break;
            }
        }
    }

    public static void main(String[] args) {
        int port = Integer.parseInt(args[0]);
        try {
            Thread t = new HelloServer(port);
            t.start();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
{% endcodeblock %}

[socket-connection-request]: /images/blog/cmpsys/socket-connect.gif
[socket-connection]: /images/blog/cmpsys/connect.gif
