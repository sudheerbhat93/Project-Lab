# Shell script to pull from Git on EC2

---

### Earlier whenever there is a change in code, we have to change the navigate to html folder and then pull the changes there. Now we are writing a simple script (got the script from chatgpt) to make this step easy. 

---

## Step 1: Create a file named deploy.sh and write the script in it. 

- Connect to EC2 instance using SSH.
- Then run the command 
```
sudo nano /home/ec2-user/deploy.sh
```
- This will screate deploy.sh file and opens the text editor
- Copy this script and paste it 
```
echo "Deploying latest code from GitHub..."
cd /var/www/html || exit
sudo git pull origin main

echo "Deployment complete at $(date)"
```
- cd `/var/www/html` → Go to your website folder
- `git pull origin main` → Pull the latest code
- `$(date)` → Adds a timestamp to show when it ran

- Here we are telling that when you run deploy.sh, navigate to this location and run this command. 

---

## Step 2: Make the script executable
- When you create a new file, it’s usually just a text file. Without +x, you could read it, but you couldn’t run it directly.
- Run this command
```
sudo chmod +x /home/ec2-user/deploy.sh
```
- `+x` – Adds execute permission to the file. This means the file can be run as a program or script.

---

## Step 3: Checking if it works 
- Do some changes in the code and push to the repo
- Then in ec2 instance, run 
```
/home/ec2-user/deploy.sh
```
- This will pull the code from Git repo 

---
