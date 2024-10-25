> [!important]  
> GoalsWrite HTTP Server using C++Make a C++ Binary libraryStatic vs. DynamicContainerize serverUse the image of the server in a different project to host a static web serverwrite IaC for web server in EC2Follow this article:> [!info] C++ Web Server from Scratch | Part 1: Creating a Socket Object  
> An open source, modular web server that will allow you to create a website that can be customized to your needs.  
> [https://www.youtube.com/watch?v=YwHErWJIh6Y](https://www.youtube.com/watch?v=YwHErWJIh6Y)  
> [!info] HTTP Server: Everything you need to know to Build a simple HTTP server from scratch | by Skrew Everything | in From The Scratch - Freedium  
> Vote for next post or mention what do you want to see next here:  
> [https://freedium.cfd/https://medium.com/from-the-scratch/http-server-what-do-you-need-to-know-to-build-a-simple-http-server-from-scratch-d1ef8945e4fa#id_token=eyJhbGciOiJSUzI1NiIsImtpZCI6ImFkZjVlNzEwZWRmZWJlY2JlZmE5YTYxNDk1NjU0ZDAzYzBiOGVkZjgiLCJ0eXAiOiJKV1QifQ.eyJpc3MiOiJodHRwczovL2FjY291bnRzLmdvb2dsZS5jb20iLCJhenAiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJhdWQiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJzdWIiOiIxMDAxNjY5NzMyMzY5NjMzNTQ3NDYiLCJlbWFpbCI6InJvaGluZXNoLmEucmFtQGdtYWlsLmNvbSIsImVtYWlsX3ZlcmlmaWVkIjp0cnVlLCJuYmYiOjE3MTE2NTUwNDMsIm5hbWUiOiJSb2hpbmVzaCBSYW0iLCJwaWN0dXJlIjoiaHR0cHM6Ly9saDMuZ29vZ2xldXNlcmNvbnRlbnQuY29tL2EvQUNnOG9jSjBjTGRQRDdNWndOaG43WW5wRkNQNndJbml4RS1hNmdWOFYxbGQwT3Q1PXM5Ni1jIiwiZ2l2ZW5fbmFtZSI6IlJvaGluZXNoIiwiZmFtaWx5X25hbWUiOiJSYW0iLCJpYXQiOjE3MTE2NTUzNDMsImV4cCI6MTcxMTY1ODk0MywianRpIjoiNTBhYzg5MzZlZGFlODIxNmRmNTcxZmQ5YTkwZDY0MGM3MTAwM2ZhMyJ9.SRg8-QhG1VePv1FYNsPWBIIhC1uVGo-NZzNAchGTOHqwDEIF9C35ObxIVFvRqMhQrYNB5_nb2Emp3_6t7Yz8xIkmP2q0oHzriXA3776BpJ6dJmHnZC6_lIftspARUVrqk3WL92DHrHocckfwblkBaaon0TMG20ZiTZy8Q0vn8IFOWuZgIVQJX3YTWDh2vQs-OUhd33YTA-82ITsq1s8X_oVNlhto9qSWPCtTbkGTdxzhU-zkARBV4DYCSdr7NzzgpxX9KHO_F-Zwqtfy-cj3tmHdfTh-NayBIdO3AI8SZ7-5eEChQZgeJ1vtMfRcaPOPW7DPU4l_5rngSbTOjjDyTg](https://freedium.cfd/https://medium.com/from-the-scratch/http-server-what-do-you-need-to-know-to-build-a-simple-http-server-from-scratch-d1ef8945e4fa#id_token=eyJhbGciOiJSUzI1NiIsImtpZCI6ImFkZjVlNzEwZWRmZWJlY2JlZmE5YTYxNDk1NjU0ZDAzYzBiOGVkZjgiLCJ0eXAiOiJKV1QifQ.eyJpc3MiOiJodHRwczovL2FjY291bnRzLmdvb2dsZS5jb20iLCJhenAiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJhdWQiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJzdWIiOiIxMDAxNjY5NzMyMzY5NjMzNTQ3NDYiLCJlbWFpbCI6InJvaGluZXNoLmEucmFtQGdtYWlsLmNvbSIsImVtYWlsX3ZlcmlmaWVkIjp0cnVlLCJuYmYiOjE3MTE2NTUwNDMsIm5hbWUiOiJSb2hpbmVzaCBSYW0iLCJwaWN0dXJlIjoiaHR0cHM6Ly9saDMuZ29vZ2xldXNlcmNvbnRlbnQuY29tL2EvQUNnOG9jSjBjTGRQRDdNWndOaG43WW5wRkNQNndJbml4RS1hNmdWOFYxbGQwT3Q1PXM5Ni1jIiwiZ2l2ZW5fbmFtZSI6IlJvaGluZXNoIiwiZmFtaWx5X25hbWUiOiJSYW0iLCJpYXQiOjE3MTE2NTUzNDMsImV4cCI6MTcxMTY1ODk0MywianRpIjoiNTBhYzg5MzZlZGFlODIxNmRmNTcxZmQ5YTkwZDY0MGM3MTAwM2ZhMyJ9.SRg8-QhG1VePv1FYNsPWBIIhC1uVGo-NZzNAchGTOHqwDEIF9C35ObxIVFvRqMhQrYNB5_nb2Emp3_6t7Yz8xIkmP2q0oHzriXA3776BpJ6dJmHnZC6_lIftspARUVrqk3WL92DHrHocckfwblkBaaon0TMG20ZiTZy8Q0vn8IFOWuZgIVQJX3YTWDh2vQs-OUhd33YTA-82ITsq1s8X_oVNlhto9qSWPCtTbkGTdxzhU-zkARBV4DYCSdr7NzzgpxX9KHO_F-Zwqtfy-cj3tmHdfTh-NayBIdO3AI8SZ7-5eEChQZgeJ1vtMfRcaPOPW7DPU4l_5rngSbTOjjDyTg)  
  

  

> [!important]  
> Future Work Figure out how to serve files with the server allow user of HTTP server class to add routes dynamically (/help maps to help.html, /dashboard ⇒ dashboard.html) OR we just focus on client side routing allow user of HTTP server to add not-found html or use a default not found Figure out how we can take the Networking Library and transform it into static or dynamic binary or source library  

  

- General Flow> [!important]  
    > General Flow1) Open TCP ConnectionTCP Is reliable as opposed to UDPClient can resuse this connection or open a new one2) Send HTTP MessageGET / HTTP/1.1  
    Host: developer.mozilla.org  
    Accept-Language: fr3) Read Response Sent by the serverHTTP/1.1 200 OK  
    Date: Sat, 09 Oct 2010 14:28:02 GMT  
    Server: Apache  
    Last-Modified: Tue, 01 Dec 2009 20:18:22 GMT  
    ETag: "51142bc1-7449-479b075b2891b"  
    Accept-Ranges: bytes  
    Content-Length: 29769  
    Content-Type: text/html  
      
    <!DOCTYPE html>… (here come the 29769 bytes of the requested web page)  
    4) Close or reuse the connection for more requests  
    
- TCP> [!important]  
    > TCP (Transmission Control Protocol)TCP-handshake - happens after DNS Lookup and after TLS HandshakeSYN - synchronizeSYN-ACK - synchronize - acknowledgeACK - acknowledgeClient (Browser) sends SYNServer receives SYN and response with SYN-ACKClient(Browser) receives SYN-ACK and sends ACK  
    
- TLS (HTTPS)> [!important]  
    > TLS (Transport Layer Security)Used to Encrypt HTTP comuncationHTTPS ⇒ by convention on port 443 by default (HTTP is on port 80)> [!info] Transport Layer Security - Security on the web | MDN  
    > The security of any connection using Transport Layer Security (TLS) is heavily dependent upon the cipher suites and security parameters selected.  
    > [https://developer.mozilla.org/en-US/docs/Web/Security/Transport_Layer_Security](https://developer.mozilla.org/en-US/docs/Web/Security/Transport_Layer_Security)  
      
    
- Sys/socket.h> [!important]  
    > Usage of the sys/socket.h header from ChatGPTThe typical flow to setup a Serverside TCP socket identified from SkrewEverything article is to call:socket() - create the socketbind() - assign the socket a transport address (port in the context of IP networking) listen() - wait for an incoming connection and create a queue of pending requestsaccept() - get the first connection request in the queue  
    This process probably is different for different communication protocol, e.g. for UDP, Client Side TCP sockets, etc.  
    The steps (socket(), bind(), listen(), accept()) are commonly associated with TCP server sockets. However, there are scenarios where you might not follow this exact sequence.Here are a few scenarios:UDP Sockets: With UDP (User Datagram Protocol), you typically don't use listen() and accept(). Instead, you just use socket() to create the socket, optionally bind() to specify the local address if you need, and then sendto() and recvfrom() to send and receive datagrams. UDP is connectionless, so there's no need to establish a connection like TCP.Client-side TCP Sockets: If you're creating a TCP client, you might not call listen() and accept(). Instead, after creating the socket with socket(), you'd call connect() to establish a connection with the server. Then you can use send() and recv() to send and receive data.Non-blocking Sockets: In scenarios where you're dealing with non-blocking sockets, you may not follow a strict sequence like listen() followed by accept(). Instead, you might use select(), poll(), or epoll() to determine when there's data available for reading or when the socket is ready for writing.Specialized Use Cases: There could be specialized network protocols or scenarios where the typical sequence doesn't apply. For instance, in a peer-to-peer network where each node can act as both a server and a client, the sequence might vary depending on the role of the node and the specific protocol being used.In summary, the sequence of socket functions you mentioned (socket(), bind(), listen(), accept()) is typical for TCP server sockets, but there are many other networking scenarios where this sequence might not be followed exactly. It depends on the requirements and characteristics of the network application you're developing.  
    
- Build Systems C++> [!important]  
    > Modern Build Systems for C++automate the compiling, linking and managing of dependencies for c++ ProjectsCMakegenerates build files (such as Makefiles or project files for IDEs like Visual Studio or Xcode) based on a simple scripting language (CMakeLists.txt)GNU Makereads a Makefile that specifies how to compile and link the project and automatically rebuilds only the necessary files when changes are madBazeldeveloped by googlehandles large multi-language projects with a focus on scalability and reproducibilityMesonreads a Makefile that specifies how to compile and link the project and automatically rebuilds only the necessary files when changes are madgenerates build files for various backends, including Ninja, Visual Studio, and XcodeNinjaoften used as a BE for for other build systems (CMake and Meson)BuckBuild system developed by FBGradlefor multiple languages (including C++)uses Groovy based Domain Specific Language to describe build configs and dependencies  
    
- C++ Library> [!important]  
    > C++ Librarytwo types:Source Code Libraries - a collection of code distributed as just source code - typically header filesBinary Libraries - compiled into a package that is run-time loadable by client programsLibraries can be a hybrid of Source Code and Binary LibrariesHeader file(s) must be included for both types ⇒ tells compiler of client program what functions, etc. to look for in the libraryIn Windows and Linux, binary libraries can be dynamic or staticdynamiclibrary is dynamically imported into the client program when it executesdecreases size of the client program (b/c we are not compiling our library with the clients program)our library needs to be distributed with the client program thoughstaticcode of library is imported into the executable of the client program during the client programs compilation stepthe client does not have to distribute your library files with their pogram  
    
      
    
    > [!important]  
    > Making Statically Linked Librarycommands:Create shared object from librarygcc -o lib<LIBNAME>.so -fpic -shared <C++ file>compile the program using the libgcc -c main.c -o main.olink the binary of the program to the shared library - without lib and .sogcc -o main main.o -l<Library_shared_object> -L$(PATH_TO_SHARED_OBJECT)  
    
- Request and Response> [!important]  
    > Sample RequestGET / HTTP/1.1 // Request-line  
    Host: 127.0.0.1 // host  
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:124.0) Gecko/20100101 Firefox/124.0  
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8  
    Accept-Language: en-US,en;q=0.5  
    Accept-Encoding: gzip, deflate, br  
    DNT: 1  
    Sec-GPC: 1  
    Connection: keep-alive  
    Upgrade-Insecure-Requests: 1  
    Sec-Fetch-Dest: document  
    Sec-Fetch-Mode: navigate  
    Sec-Fetch-Site: none  
    Sec-Fetch-User: ?1  
      
    > [!important]  
    > Parsing Requestwith Reference to RFC 2616 - Section 5The request object looks like this:Request = Request-Line  
    *(( general-header  
    | request-header  
    | entity-header ) CRLF)  
    CRLF  
    [ message-body ]  
    Request-line: Method SP Request-URI SP HTTP-version CRLFMethod: OPTIONS, GET, HEAD, POST, PUT, DELETE, TRACE, CONNECTreturns 405 = Method not allowed - if method is known by the server but not implementedreturn 501 = Not Implemented - if method is not recognized or not implemented by the serverGET and HEAD must be implementedRequest-URI:Request-URI = "*" | absoluteURI | abs_path | authorityabs_path = requesting resources like an HTML filecannot be empty ⇒ if none is present, it must be given as / e.g:GET /pub/WWW/TheProject.html HTTP/1.1 // request line  
    Host: www.w3.orggetting TheProject.html from the hostHost: Can be ignored for HTTP Server  
    Request Header:allows the client to pass addition info about the request, and the client to the serverrequest-header = Accept  
    | Accept-Charset  
    | Accept-Encoding  
    | Accept-Language  
    | Authorization  
    | Expect  
    | From  
    | Host  
    | If-Match  
    | If-Modified-Since  
    | If-None-Match  
    | If-Range  
    | If-Unmodified-Since  
    | Max-Forwards  
    | Proxy-Authorization  
    | Range  
    | Referer  
    | TE  
    | User-Agent  
      
    
      
    
    > [!important]  
    > Response ObjectResponse Object looks like thisResponse = Status-Line  
    *(( general-header  
    | response-header  
    | entity-header ) CRLF)  
    CRLF  
    [ message-body ]  
    Status-Line Status-Line = HTTP-Version SP Status-Code SP Reason-Phrase CRLF
