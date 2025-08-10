# Getting SSL Working for Apache on EC2 with ZeroSSL  
*(with explanations for each step)*


---


## **Step1: Go to ZeroSSL to Generate SSL Certificate**  

- 1. Go to [ZeroSSL](https://zerossl.com)  
- 2. Enter your domain name: 
- 3. Choose the **free 90-day certificate** option.


---


## **Step2: Choose Verification Method**  

I picked **HTTP file upload** verification.  

ZeroSSL must confirm you own the domain before issuing the certificate.  

HTTP verification works by placing a unique file on your website, which ZeroSSL checks over the internet.


---


## **Step3: Download the Verification File**  

ZeroSSL provides a `.txt` file with a unique name and content.  

This file proves domain ownership when placed on the website in a specific location.


---


## **Step4: Create the Verification Directory on EC2**  

```
sudo mkdir -p /var/www/html/.well-known/pki-validation/
```

This is the path ZeroSSL expects to find the verification file. SeroSSL wants to verify the file here


---

## **Step5: Copy the Verification File to EC2**  

```
scp -i "your-key.pem" "auth-file.txt" ec2-user@your-ec2-ip:/tmp/
```

We copy to `/tmp` first because `/etc` and `/var/www` often require root permission.


---


## **Step6: Move the File to the Verification Directory**  

```
sudo mv /tmp/auth-file.txt /var/www/html/.well-known/pki-validation/
```

This will move the file from `tmp` folder to `pki-validation` folder, where Zerossl wants them to verify the domain 


---

## **Step7: Verify File is Accessible in Browser**  

Visit:  
[http://yourdomainname/.well-known/pki-validation/auth-file.txt](http://sudheerbhat.in/.well-known/pki-validation/auth-file.txt)

If your browser can load the file, ZeroSSL can too.


---


## **Step8: Complete Verification on ZeroSSL**  

Go back to ZeroSSL and click **Verify**.

ZeroSSL will fetch that file from your site and confirm you own the domain.


---


## **Step9: Download Your SSL Certificate Files**  

Follow the on screen steps, you’ll get:  
- `certificate.crt` → Your SSL certificate  
- `private.key` → Your private key (keep this secure!)  
- `ca_bundle.crt` → Chain of intermediate certificates for browser trust.  

These three files are all Apache needs to serve HTTPS.


---


## **Step10: Create SSL Storage Directory on EC2**  

```
sudo mkdir -p /etc/httpd/ssl
```
We store certificates in `/etc/httpd/ssl so web server knows where to find them.


---

## **Step11: Copy SSL Files to EC2**  

Exit from EC2, and then copy the 3 files to tmp folder in EC2

```
scp -i "your-key.pem" certificate.crt ec2-user@your-ec2-ip:/tmp/
```
Repeat for `private.key` and `ca_bundle.crt`.

Then SSH to EC2, and move the files from `/tmp` to `/ssl` folder

```
sudo mv /tmp/certificate.crt /etc/httpd/ssl/
```
Repeat for `private.key` and `ca_bundle.crt`.

Same two-step process as before — upload to `/tmp`, then move to `/etc/httpd/ssl` with root permissions.


---


## **Step12: Configure Apache for SSL**  

The goal here is to tell Apache:  
- Where the certificate files are (the ones you copied into `/etc/httpd/ssl`
- Which domain they apply to  
- Which port to use for secure connections (443)

### Open the SSL configuration file  

Apache’s SSL settings usually live in `/etc/httpd/conf.d/ssl.conf`.  
Edit that file using:  

```
sudo nano /etc/httpd/conf.d/ssl.conf
```
This command opens the `ssl.conf` file for editing.


###  Replace or Add the <VirtualHost> block

```
<VirtualHost *:443>
    ServerName sudheerbhat.in

    DocumentRoot /var/www/html

    SSLEngine on
    SSLCertificateFile /etc/httpd/ssl/certificate.crt
    SSLCertificateKeyFile /etc/httpd/ssl/private.key
    SSLCertificateChainFile /etc/httpd/ssl/ca_bundle.crt

    <Directory /var/www/html>
        AllowOverride All
    </Directory>
</VirtualHost>
```

### Save and exit
- Press `CTRL + O` → Save file  
- Press `Enter` → Confirm filename  
- Press `CTRL + X` → Exit editor

### What this does

- `<VirtualHost *:443>` → Tells Apache to handle HTTPS traffic on port 443 for all IPs.  
- `ServerName sudheerbhat.in` → Your domain name.  
- `DocumentRoot /var/www/html` → Where your site’s files are stored.  
- `SSLEngine on` → Enables SSL encryption for this virtual host.  
- `SSLCertificateFile` → Path to your `certificate.crt` (public cert).  
- `SSLCertificateKeyFile` → Path to your `private.key` (keep secret!).  
- `SSLCertificateChainFile` → Path to your `ca_bundle.crt` (intermediate certs).  
- `<Directory /var/www/html>` → Allows `.htaccess` to override rules.


---

## **Step13: Restart Apache to Apply Changes**  

```
sudo systemctl restart httpd
```

Restarting Apache reloads the configuration files so SSL settings take effect.

---

## **Step14: ERROR: Invalid command 'SSLEngine'**  

**Why it happened:**  
- SSLEngine is not a built-in Apache command.
- It comes from the mod_ssl module.
- If mod_ssl isn’t installed or isn’t loaded, Apache won’t recognize it — hence the error.

**Fix:**  

```
sudo yum install -y mod_ssl
```
This installs SSL support for Apache.


---

## **Step15: Check Listening Ports

Run this command to verify Apache is listening on port 443 (HTTPS):

```
sudo ss -tuln | grep 443
```

---

## **Step16: ERROR: Apache Not Listening on 443**  

**Check config:**  

```
sudo grep -i listen /etc/httpd/conf/httpd.conf /etc/httpd/conf.d/*.conf
```
If only Listen 80 is present (for HTTP), Apache is not set to listen on port 443.

Fix:

```
echo "Listen 443" | sudo tee /etc/httpd/conf.d/ssl-listen.conf
```
This tells Apache to listen for HTTPS connections on port 443.

----

## **Step17: Restart Apache**  

```
sudo systemctl restart httpd
```

---

## **Step18: Confirm Apache is Listening on 443**  

```
sudo ss -tuln | grep 443
```
You should now see output similar to:
```
tcp    LISTEN     0      511    *:443       *:*
```

---

## **Step19: Test in Browser**  

Visit:  
[https://sudheerbhat.in](https://sudheerbhat.in)  

You should see a **padlock icon** indicating SSL is working.

----

