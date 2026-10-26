# Networking: Domain + EC2 + NGINX + DNS

I bought a domain, launched an AWS EC2 instance running NGINX, and pointed the domain at the server with a DNS A record, so a page loads over my own domain name.

## What I Built

- Registered a domain through Cloudflare
- Launched an EC2 instance on AWS with a public IPv4 address
- Configured a security group to allow SSH (port 22) and HTTP (port 80)
- Installed and started NGINX as the web server
- Created an A record in Cloudflare pointing the domain to the EC2 public IP
- Confirmed the NGINX landing page loads at http://<nginx.ahmadh.co.uk>

### Bonus: wrote a small Flask app (app/app.py) to see how an application server differs from NGINX serving a default page

Architecture
Browser
|
| 1. "What is the IP for nginx.ahmadh.co.uk?"
v
Cloudflare DNS (A record)
|
| 2. Returns the EC2 public IPv4 address
v
EC2 instance (security group allows port 80)
|
v
NGINX (listening on port 80) --> serves the landing page

Steps
1: Bought a domain on Cloudflare
2: Launched an EC2 instance and created a key pair for SSH access
3: Set up the security group with inbound rules for SSH (22) and HTTP (80)
4: Connected over SSH and installed NGINX
5: Started and enabled NGINX so it runs now and on reboot
6: Created an A record in Cloudflare pointing to the instance's public IPv4
7: Tested by visiting the domain in a browser and confirming the NGINX welcome page

## What I Learned

- DNS: how a domain name is translated to an IP address, and what an A record does

- Public vs private IPs: the instance has both, and DNS must point at the public one

- Ports: an IP address gets traffic to the machine, and the port decides which service receives it (80 for HTTP, 22 for SSH)

- Security groups: they act as a firewall and deny inbound traffic by default, so port 80 had to be opened explicitly

- Web servers: NGINX listens on a port and serves content to incoming requests

## Challenges and Solutions

My domain showed a Flask page instead of the NGINX landing page

- Problem: After running my Flask app on port 80, my domain displayed "Hello, World!" instead of the NGINX welcome page.

- Cause: NGINX wasn't running, so port 80 was free and Flask bound to it. Only one process can listen on a given port at a time. If NGINX had been running first, Flask would have failed to start with an "Address already in use" error.

- Fix: I stopped Flask, started NGINX with sudo systemctl start nginx, confirmed with curl localhost, and reloaded the domain to see the NGINX page.

- Takeaway: I can check what is using a port with sudo ss -tlnp | grep :80. In a real setup, NGINX would sit on port 80 and act as a reverse proxy to the Flask app running on a different port (for example 5000).

## Bonus: Flask App

I wrote a minimal Flask app to understand how an application server differs from NGINX serving a static default page:

python
from flask import Flask

app = Flask(**name**)

@app.route('/')
def hello_world():
return "Hello, World!"

if **name** == '**main**':
app.run(host='0.0.0.0', port=80)

host='0.0.0.0' makes Flask listen on all network interfaces, so it is reachable from outside the instance
Binding to port 80 requires elevated privileges and conflicts with NGINX
Flask's built-in server is for development only. Production setups put NGINX in front as a reverse proxy
