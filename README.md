# Networking
 Networking - OSI/TCP-IP models, IP addressing, encapsulation, Telnet-based protocol analysis, and key networking commands (telnet, ip, netstat).

By Ramyar Daneshgar

---

### Task 7: Telnet (In-Depth Technical Writeup)

In this lab, I manually interfaced with TCP-based services using the Telnet protocol to understand how network-layer and application-layer protocols operate in the absence of client-side abstraction. This allowed me to observe the behavior of low-level network communication, and understand the structure of protocols like HTTP without relying on web browsers or API clients.

---

### Objective:

Establish a raw TCP connection to a target machine (`10.10.144.137`) on port `80`, simulate a browser by issuing a manual HTTP request via Telnet, extract the HTTP server details, and retrieve a flag embedded in the HTML response.

---

#### **Step 1: Initiating a Telnet Session**

```bash
$ telnet 10.10.144.137 80
```

Here, I used `telnet` to initiate a TCP session with the target on port 80. Port 80 is the default port for HTTP, and by connecting directly to it, I am essentially simulating the behavior of a browser but without any of the automated HTTP headers or rendering engines. This command opens a bidirectional TCP stream where I can manually send and receive data.

**Technical Rationale:**

* Telnet uses the TCP protocol for connection-oriented communication.
* Port 80 confirms the target is running an HTTP server.
* This raw connection allows me to observe how HTTP operates under the hood.

---

#### **Step 2: Issuing an HTTP GET Request Manually**

```http
GET / HTTP/1.1
Host: telnet.thm
```

After establishing the TCP session, I manually crafted an HTTP request using the `GET` method. HTTP/1.1 requires the inclusion of a `Host` header to support virtual hosting. Without the `Host` line, the server might respond with an error (400 Bad Request) or no content at all.

**Explanation of Components:**

* `GET / HTTP/1.1`: Requests the root resource (`/`) using the HTTP/1.1 protocol.
* `Host: telnet.thm`: Required in HTTP/1.1 to inform the server which hostname we're trying to reach—important when multiple virtual hosts are configured on the same IP.

I pressed **Enter twice** after the request to signify the end of the HTTP headers, as required by HTTP protocol syntax. This blank line tells the server, "That’s all the headers; now process the request."

---

#### **Step 3: Interpreting the Raw HTTP Response**

```
HTTP/1.1 200 OK
Server: lighttpd/1.4.63
Content-Type: text/html
...
THM{TELNET_MASTER}
...
```

Once the server received the properly formatted request, it responded with an HTTP response. Let's analyze this response line by line:

* `HTTP/1.1 200 OK`: Status line indicating that the request was successful.
* `Server: lighttpd/1.4.63`: This header identifies the web server software and version running on the host. Lighttpd is a lightweight, high-performance HTTP server.
* `Content-Type: text/html`: Indicates that the body of the response is HTML.
* `THM{TELNET_MASTER}`: A flag embedded within the HTML content, demonstrating that the manual request succeeded.

**Technical Insight:**
By issuing a raw GET request and analyzing the headers and body, I could validate that:

* The server is accessible and responsive on TCP port 80.
* The HTTP protocol is functioning correctly.
* Manual requests can yield the same data as browser-based sessions, making tools like Telnet useful for low-level testing and protocol validation.

---

### Technical Concepts Reinforced

#### 1. **TCP Port Communication**

* Ports are logical endpoints used to distinguish services on a system.
* Port 80 is reserved for HTTP and is commonly open on web servers.
* Telnet allows direct socket-level interaction with these ports, bypassing higher-level abstractions.

#### 2. **HTTP/1.1 Protocol Structure**

* All HTTP/1.1 requests consist of a request line, headers, and an empty line.
* Host headers are mandatory for HTTP/1.1 compliance.
* Responses begin with a status line, followed by headers, and then the body (payload).

#### 3. **Client Simulation and Virtual Hosting**

* Modern web servers often serve multiple domains from a single IP address. The Host header differentiates between them.
* Manual requests must follow protocol requirements exactly to be successful.

  
---

### Final Answers

**Q1:** What is the name and version of the HTTP server?

> **Answer:** lighttpd/1.4.63

**Q2:** What flag did you get when you viewed the page?

> **Answer:** THM{TELNET\_MASTER}

---

### Lessons Learned

* **Protocol Awareness:** I deepened my understanding of the HTTP protocol, specifically how request and response structures work at the socket level.

* **Security Awareness:** Telnet sends everything in cleartext. This task reinforced why Telnet is deprecated in production environments, and why secure alternatives like SSH and HTTPS are used today.

* **Practical TCP/IP Knowledge:** This exercise demonstrated TCP session establishment without automation. I had full visibility into what was sent, received, and how protocols behave.

* **Testing Without Tools:** Knowing how to simulate application behavior without relying on tools (curl, Postman, browsers) is critical for protocol debugging, penetration testing, and troubleshooting misconfigured services.

* **Precision in Syntax Matters:** A single missing line break or header can cause the server to ignore the request entirely highlighting the importance of strict protocol compliance.

