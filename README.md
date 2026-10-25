# Networking: Domain + EC2 + DNS

## What I Built

Short summary: bought a domain via Cloudflare, launched an EC2 instance
running NGINX, and pointed an A record at it so the site loads over my domain.

## Architecture

Browser -> DNS lookup (Cloudflare) -> A record -> EC2 public IP -> NGINX (port 80)

## Steps

1. Bought domain
2. Launched EC2 + security group + prefix list that cloudflare could point to
3. Installed NGINX
4. Created A record
5. Tested

## What I Learned

- How DNS resolves a name to an IP
- What an A record does
- Ports, security groups, and why port 80 had to be open
- Public vs private IPs

## Challenges and Solutions

- Problem: site didn't load at first, showed as host down even with NGINX running.
  Fix: security group was missing the port 80 rule / DNS hadn't propagated, also had to create a prefix list in AWS so that cloudflare could be pointed to it

## Screenshots

![NGINX page](https://github.com/ahmadh-1/docker-learning/blob/main/networking/screenshots/landing-page%20.png)
