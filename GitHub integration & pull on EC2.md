# GitHub integration & pull on EC2


Till now, we were pushing the code to EC2 directly from local machine. Now we are integrating git so that we can pull from git on ec2. 

## Step1: Create a Repo in Git, push the code from local host to this repo. 

---

## Step2: ssh to EC2 Instance 
-- connect to ec2 instance 
- check if git is installed 

```
git --version
```

---usually git is not installed. You can install git with following command

``` 
sudo yum install git -y
```

This will install git on your ec2 instance now confirm that by checking the version

---

## Step3: Cloning the repo 

--- as its the first time, we are making a complete copy of the repo on our ec2 instance. As apache server hosts the website from  'var/www/html' folder, we have to have the code files in this folder. 

--- change the directory to html
``` 
cd /var/www/html
```
Now clone here
```
sudo clone <repo-url> . 
```
this will clone the files in html folder, '.' will tell to clone the files here and not to clone entire repo folder. 

--- check if the files are copied. 
``` 
ls -la
```
This wil list all the code files

---

## Step4: Check the website

-- Now check the website in the browser 

---

