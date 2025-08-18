# Managing Website with Cloudflare

## Objective

I am trying to learn about the different options available in Cloudflare and how they can be used to manage a website effectively.  

---

## Step 1: Change of Name Servers

Till now, the name servers for the domain were managed by GoDaddy. We will now switch them to Cloudflare.

- Create a Cloudflare account and add your domain.  
- Cloudflare will provide new name servers. Replace the existing GoDaddy name servers with these.  
- It may take some time for the changes to propagate across the internet.  
- Once updated, verify that the A record points to the correct IP address.  
- After this step, DNS for the domain is now managed by Cloudflare.

---
## I experimented with following options
---

## Step 2: Enabling Proxy Status

By enabling proxy status in Cloudflare:  
- All traffic will be routed through Cloudflare’s network.  
- Origin IP will be hidden from the public with proxy servers.  
- CDN caching will be enabled to improve website performance.

### Verification using nslookup
- In Command Prompt, run: ``nslookup sudheerbhat.in``
  - This will return a list of IP addresses instead of the IP of your EC2 instance.  
  - These IPs belong to Cloudflare’s CDN edge servers and proxy servers, which improve performance and protect origin server by hiding its IP.

- Now disable Proxy Status and run nslookup again:  
  - You will see the IP of the origin server (EC2 instance) instead.

---

## Step 3: Security Rules Configuration - GeoBlock

- GeoBlock can be used to block traffic from a particular country.  
- Navigate to **Security → Security Rules** in the Cloudflare dashboard.  
- Create a new rule → **Custom Rule**.  
- Give the rule a name, e.g., `"Block requests from India"`.  
- Set the parameters:  
    - **Field:** `Country ` 
    - **Operator:** `Equals ` 
    - **Value:** `India`  
- Select **Block** for the action, then click **Deploy**.  
- Now, when loading the website from a blocked country (India in this case), requests will be blocked and the user will see:  
 

---
## Step 4: Security Rules Configuration - Verify you are human

- You can check if the traffic is from a human.  
- Create a new rule → **Custom Rule**.  
- Give the rule a name, e.g., `"Verify Human"`.  
- Set the parameters:  
  - **Field:** `URI Path`  
  - **Operator:** `Wildcard`  
  - **Value:** `/`  
- Select **Managed Challenge** for the action, then click **Deploy**.  
- Now, when loading the website, you will be verified if human.  
- Sometimes you will be asked to tick the box, and sometimes it will verify automatically.

---

### I will keep trying new things as I learn new concepts and then experiment with them in my Cloudflare account.  






