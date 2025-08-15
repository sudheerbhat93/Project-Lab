# GitHub integration & pull on EC2

---

Earlier, I was pushing the code from my local machine directly to the EC2 server. Now, I have integrated Git with the EC2 instance, allowing me to pull the code directly from my GitHub repository on the server.

## Step 1: I created a new repository on GitHub and pushed my local project code to this repository from my local machine.

---

## Step 2: SSH to EC2 Instance

- Connect to EC2 instance using SSH.

- Check if Git was installed by running:

```
git --version
```

- Usually git is not installed. Since Git was not installed,  install it using:

``` 
sudo yum install git -y
```

- This will install git on ec2 instance, now confirm that by checking the version

---

## Step 3: Cloning the Repository


- Since this is the first time, make a complete copy of the repository on the EC2 instance. As the Apache server hosts the website from the `/var/www/html` directory, the code files must be placed in this folder.

- Navigate to the /var/www/html directory.
``` 
cd /var/www/html
```
- Clone the repository into the current directory.
```
sudo clone <repo-url> . 
```
- This command will clone the files into the html folder. The `.` specifies that the files should be cloned into the current directory rather than creating a new repository folder.

- Verify that the files have been cloned successfully.
``` 
ls -la
```
- This command lists all files and directories in the current location, including hidden ones, along with their permissions and details.

---

## Step 4: Check the Website

- Open your web browser and enter the domain name to verify that the website is loading correctly.


---

