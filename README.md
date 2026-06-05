# Web Infrastructure Design

This project documents common web infrastructure designs for `www.foobar.com`.
Each task includes the required components, the role of each component, and the
main weaknesses or improvements of the design.

## Quiz Answers

0. A server is a device, a virtual device or computer program for providing functionality for other programs or devices, called "clients".
1. A web server is a software that serves web pages to clients upon their request, it does this over the protocol HTTP.
2. A codebase is the collection of source code that is used to build a software system.
3. A database is a collection of information that is stored and organized so that it can be easily accessed, updated and managed.
4. DNS is a system to translate domain names into IP addresses.
5. HTTPS is a version of HTTP that secures traffic between your browser and the website by encrypting it.
6. TCP/IP, Transmission Control Protocol/Internet Protocol, is a suite of communications protocols used to interconnect network devices on the Internet or any private network.

## Task 0: Simple Web Stack

### Diagram

See `diagrams/0-simple_web_stack.mmd`.

### Components

- `User`: A client using a browser to request the website.
- `DNS`: Resolves `www.foobar.com` to the server IP address `8.8.8.8`.
- `Server`: Hosts the whole web stack.
- `Nginx`: Web server that receives HTTP requests and serves static content or forwards dynamic requests.
- `Application Server`: Runs the website application code.
- `Codebase`: The source code for the website.
- `MySQL Database`: Stores and returns persistent application data.

### Request Flow

1. The user enters `www.foobar.com` in a browser.
2. DNS resolves `www.foobar.com` to `8.8.8.8`.
3. The browser sends an HTTP request to the server.
4. Nginx receives the request.
5. Nginx serves static files or forwards dynamic requests to the application server.
6. The application server runs the codebase and queries MySQL when data is needed.
7. The response is returned to the user's browser.

### DNS Record

`www.foobar.com` uses an `A` record because it maps a hostname to an IPv4 address.

### Issues

- `SPOF`: One server means any server failure brings the site down.
- `Downtime during maintenance`: Deploying new code or restarting services can interrupt traffic.
- `Scalability`: One server cannot handle large traffic increases.

## Task 1: Distributed Web Infrastructure

### Diagram

See `diagrams/1-distributed_web_infrastructure.mmd`.

### Added Components

- `HAProxy Load Balancer`: Distributes traffic across multiple servers.
- `Web Server 1 and 2`: Serve HTTP requests through Nginx.
- `Application Server 1 and 2`: Run application code.
- `Primary MySQL Database`: Handles writes.
- `Replica MySQL Database`: Copies data from the primary and can handle reads.

### Distribution Algorithm

The load balancer can use `round robin`, sending requests to each backend server in turn.

### Active-Active Setup

This is an `active-active` setup because both web/application servers receive live traffic.

### Primary-Replica Database

- `Primary`: Receives write operations such as inserts, updates, and deletes.
- `Replica`: Receives copied data from the primary and can serve read operations.

### Issues

- `SPOF`: A single load balancer is still a single point of failure.
- `Security`: Traffic is not encrypted with HTTPS and there is no firewall.
- `Monitoring`: No system is checking health, metrics, or alerts.

## Task 2: Secured and Monitored Web Infrastructure

### Diagram

See `diagrams/2-secured_and_monitored_web_infrastructure.mmd`.

### Added Components

- `Firewalls`: Filter allowed traffic and block unwanted access.
- `SSL Certificate`: Enables HTTPS for encrypted browser-to-site traffic.
- `Monitoring Clients`: Collect metrics and logs from servers.
- `Monitoring Service`: Stores metrics, dashboards, and alerts.

### Monitoring

Monitoring clients collect metrics such as CPU, memory, disk usage, network traffic,
web server request count, error rate, response time, and database activity. To monitor
QPS, the monitoring client can collect Nginx or load balancer request metrics and send
them to the monitoring service.

### Issues

- `SSL termination at load balancer`: Traffic between the load balancer and backend servers may be unencrypted unless internal HTTPS is also configured.
- `Single write database`: Only one primary database accepts writes, so write capacity and failover are limited.
- `Identical components on every server`: Combining web server, application server, and database roles on the same machines makes scaling and isolation harder.

## Task 3: Scale Up

### Diagram

See `diagrams/3-scale_up.mmd`.

### Added Components

- `Second Load Balancer`: Removes the load balancer SPOF.
- `Load Balancer Cluster`: Provides high availability for traffic routing.
- `Dedicated Web Servers`: Handle HTTP and static content only.
- `Dedicated Application Servers`: Run the application code only.
- `Dedicated Database Servers`: Store and replicate data only.

### Why Split Components

Splitting web, application, and database layers makes each layer easier to scale,
secure, monitor, and maintain independently.

## Acronyms

- `LAMP`: Linux, Apache, MySQL, PHP/Python/Perl.
- `SPOF`: Single Point Of Failure.
- `QPS`: Queries Per Second.

## Manual QA

This project requires manual QA review after the answer files are pushed to GitHub.

## Before Pushing

- Replace each `TODO` in the task answer files with the hosted screenshot URL for that task.
- Confirm Question #1 uses the software-focused answer: "A web server is a software that serves web pages to clients upon their request, it does this over the protocol HTTP."
- Push the project folder to GitHub.
- Submit the GitHub file links in the project checker.
- Request manual QA review when the submitted files are ready.
