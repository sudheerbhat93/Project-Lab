# Getting SSL Working for Apache on EC2 with ZeroSSL  
*(with explanations for each step)*

---

## **1. Go to ZeroSSL to Generate SSL Certificate**  
**Why:** Apache can’t serve secure `https://` traffic without an SSL certificate. We use **ZeroSSL** to get a free certificate.  

**Logic:** An SSL certificate encrypts traffic between your server and the visitor’s browser so nobody can intercept sensitive data.

---

## **2. Start SSL Certificate Creation**  
Enter your domain name: sudheerbhat.in 
Choose the free 90-day certificate option.

Why: We start the process by telling ZeroSSL which domain to secure.

---

## **3. Choose Verification Method**  
We picked **HTTP file upload** verification.  

**Logic:** ZeroSSL must confirm you own the domain before issuing the certificate.  

HTTP verification works by placing a unique file on your website, which ZeroSSL checks over the internet.

---

## **4. Download the Verification File**  
ZeroSSL provides a `.txt` file with a unique name and content.  

**Why:** This file proves domain ownership when placed on the website in a specific location.

---

## **5. Create the Verification Directory on EC2**  

```
sudo mkdir -p /var/www/html/.well-known/pki-validation/
```
Command Breakdown:
sudo — run as admin (root user)
mkdir — create directory
-p — create parent folders if they don’t exist
/var/www/html/.well-known/pki-validation/ — the exact folder Apache will serve the verification file from.

Logic: This is the path ZeroSSL expects to find the verification file.

---

---

## **6. Copy the Verification File to EC2**  

```
scp -i "your-key.pem" "auth-file.txt" ec2-user@your-ec2-ip:/tmp/
```
Command breakdown:
scp → secure copy from your local computer to the server
-i "your-key.pem" → SSH key for authentication
"auth-file.txt" → the verification file on your local machine
ec2-user@your-ec2-ip → username and server address /tmp/ → temporary location on EC2

Logic: We copy to /tmp first because /etc and /var/www often require root permission.

---


---

## **7. Move the File to the Verification Directory**  

```
sudo mv /tmp/auth-file.txt /var/www/html/.well-known/pki-validation/
```
This will move the file from tmp folder to pki-validation folder, where Zerossl wants them to verify the domain 

---

## **8. Verify File is Accessible in Browser**  

Visit:  
[http://sudheerbhat.in/.well-known/pki-validation/auth-file.txt](http://sudheerbhat.in/.well-known/pki-validation/auth-file.txt)

**Logic:** If your browser can load the file, ZeroSSL can too.

---



## **9. Complete Verification on ZeroSSL**  

Go back to ZeroSSL and click **Verify**.

**Logic:** ZeroSSL will fetch that file from your site and confirm you own the domain.

---


## **10. Download Your SSL Certificate Files**  

You’ll get:  
- `certificate.crt` → Your SSL certificate  
- `private.key` → Your private key (keep this secure!)  
- `ca_bundle.crt` → Chain of intermediate certificates for browser trust.  

**Logic:** These three files are all Apache needs to serve HTTPS.

---



## **11. Create SSL Storage Directory on EC2**  

```
sudo mkdir -p /etc/httpd/ssl
```
Logic: We store certificates in /etc/httpd/ssl so Apache knows where to find them.

---

## **12. Copy SSL Files to EC2**  

Example for certificate:  

```
scp -i "your-key.pem" certificate.crt ec2-user@your-ec2-ip:/tmp/
```
```
sudo mv /tmp/certificate.crt /etc/httpd/ssl/
```
Repeat for private.key and ca_bundle.crt.
Logic: Same two-step process as before — upload to /tmp, then move to /etc/httpd/ssl with root permissions.

---


## **13. Configure Apache for SSL**  

Edit `/etc/httpd/conf.d/ssl.conf` and add the following:  

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

Logic:
<VirtualHost *:443> → Handle traffic on port 443 (HTTPS)
SSLEngine on → Enable SSL
The three SSL file paths tell Apache where the certs are stored.
<Directory> block lets .htaccess override rules.

---

## **14. Restart Apache to Apply Changes**  

```
sudo systemctl restart httpd
```

Restarting Apache reloads the configuration files so SSL settings take effect.

---

## **15 ERROR: Invalid command 'SSLEngine'**  

**Why it happened:**  
The `mod_ssl` module, which enables SSL directives in Apache, wasn’t installed.

**Fix:**  

```
sudo yum install -y mod_ssl
```

This installs SSL support for Apache.

---
## **16. Check Listening Ports

Run this command to verify Apache is listening on port 443 (HTTPS):

```
sudo ss -tuln | grep 443
```

ss shows active sockets;
grep 443 filters results to SSL port 443.
If nothing shows → Apache is not listening on port 443.


---


## **17. ERROR: Apache Not Listening on 443**  

**Check config:**  

```
sudo grep -i listen /etc/httpd/conf/httpd.conf /etc/httpd/conf.d/*.conf
```
If only Listen 80 is present (for HTTP), Apache is not set to listen on port 443.

Fix:

```
echo "Listen 443" | sudo tee /etc/httpd/conf.d/ssl-listen.conf
```
Logic: This tells Apache to listen for HTTPS connections on port 443.

----

## **18. Restart Apache**  

```
sudo systemctl restart httpd
```

---

## **19. Confirm Apache is Listening on 443**  

```
sudo ss -tuln | grep 443
```
You should now see output similar to:

tcp    LISTEN     0      511    *:443       *:*


---

## **20. Test in Browser**  

Visit:  
[https://sudheerbhat.in](https://sudheerbhat.in)  

You should see a **padlock icon** indicating SSL is working.

----

