
# Bringing a Site Online with EC2

**Objective:**  
Host your website (**HTML**, **CSS**, **JS** files) on an AWS EC2 instance running the **Apache web server**.

---

## Step 1. Launch an EC2 Instance

- **Free Tier eligible**
- **OS:** Amazon Linux (Amazon Linux 2 or Amazon Linux 2023)
- **Configure Security Group:**
  - Allow **HTTP (port 80)** from anywhere.
  - Allow **SSH (port 22)** from your IP.

📄 *Refer*  [My AWS GitHub Repo](https://github.com/sudheerbhat93/aws)  *for detailed instructions.*

**📌 Step 1 Flow: Launch EC2 → Configure Security Group → Connect via SSH

---

## Step 2. Install Apache Web Server

Apache serves your website from `/var/www/html`.

### 2.1 Update packages
```
sudo yum update -y
```
- Upgrades all installed packages to latest versions.  
- `-y` answers “yes” to prompts automatically.

### 2.2 Install Apache
```
sudo yum install -y httpd
```
Installs the Apache HTTP server.

### 2.3 Start Apache now

sudo systemctl start httpd

Starts the web server immediately.

### 2.4 Enable Apache at boot

sudo systemctl enable httpd
```
Ensures Apache starts automatically after system reboots.

**📌 Step2 Flow:** Install webserver → Start now → Auto-start at boot.

---

## Step 3. Upload Website Files from Local to EC2

Your files:  
`index.html` | `style.css` | `script.js`

### 3.1 Exit from the instance and Navigate to your local project folder

### 3.2 Copy files to EC2 home directory

scp -i "PATH_TO_PEM_FILE" index.html style.css script.js ec2-user@EC2_PUBLIC_IP:/home/ec2-user/
```
**Example:**

scp -i "D:\path\to\key\key2keyname.pem" index.html style.css script.js ec2-user@<public IP Address>:/home/ec2-user/
```
⚠ You cannot directly copy to `/var/www/html` via `scp` because it requires **root permissions**.


**📌 Step 3 Flow: Local files → SCP to EC2 home directory → Ready to move

---

## Step 4. Move Files to Apache Web Directory

### 4.1 SSH into EC2

ssh -i "PATH_TO_PEM_FILE" ec2-user@EC2_PUBLIC_IP
```

### 4.2 Move files to `/var/www/html`

sudo mv index.html style.css script.js /var/www/html/
```

### 4.3 Verify files

ls /var/www/html
```

**📌 Step 4 Flow: SSH to EC2 → Move files to /var/www/html → Verify location

---

## Step 5. Restart Apache

sudo systemctl restart httpd
```

**📌 Step 5 Flow: Restart Apache → Load updated site

---

## Step 6. Test in Browser

Open:  
```
http://<EC2_PUBLIC_IP>/
```
You should see your website.

**If you get “This site can’t be reached”**:
- Use `http://` not `https://`.
- Ensure Security Group inbound rules allow **HTTP (port 80)**.

**📌 Step 6 Flow: Enter http://EC2_PUBLIC_IP/ → Browser displays site

---
