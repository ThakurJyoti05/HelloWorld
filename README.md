# Problem Statement:
1. Create a HelloWorld app in NodeJs and push the code on Git Hub.
2. Set up the secure infrastructure for the App on AWS and host that app on Infrastructure.
3. Set up Ngnix to serve the application on port 80.
4. Install the SSL certificate from Lets Encrypt on the app and use the domain name "your name".signiance.com. You can share the endpoint of your server or load balancer with me during review and I will map it with the domain.
5. Create a CI/CD pipeline for the app using tools of your choice.
6. It should build and deploy the code on push.
7. Changes should be visible on the above URL.
8. Document the assignment and share your learning.

# Solution:
## Base OS: Ubuntu 23.04

## Step 1. Create a HelloWorld app in NodeJS.
### A: Update Package Index and install Nodejs and npm

### B. Verify Installation

### C. Create a Project dir and write simpe code for HelloWorld

### D. Steps to create a Nodejs app and source code
1. Create a new file named index.html in the same directory as the JavaScript file.
2. Copy and paste the HTML content above into the index.html file.
3. Save the index.html file.
4. Create a new file with a .js extension (e.g., app.js).
5. Copy and paste the JavaScript code above into the server.js file.
6. Save the app.js file.
7. Open a terminal and navigate to the directory where the files are saved.
8. Run and test the following command to start the server:
```bash
node app.js
### E. Push the code on GitHub
1. create a new repo on GitHub with the name: "HelloWorld"
2. On your local machine in the project folder give the following commands
  ```bash 
  > git init
  > git branch -M main
  > git remote add origin https://github.com/ThakurJyoti05/HelloWorld.git
  > git push -u origin main 

### f. Git commands on the local machine for branch sync
```bash
git checkout main
git pull --rebase
git rebase main
git push -f origin <my_branch>
```

## Step 2: Set up the secure infrastructure for the App on AWS and host that app on Infrastructure.
### For secure infrastructure we will create our own VPC, IGW, RT, Subnets and ALB and use 
1. Create a VPC with CIDR: 10.0.0.0/16
2. Create a two subnets: az1a: 10.0.1.0/24  and az1b: 10.0.2.0/24
3. Create an IGW
4. Create a Route table -> Edit subnet association and Add rule
5. Create a Security Group: allow 80, 443 and 22
6. Create a Ec2 instance -> ubuntu -> Network (Select Node-project-VPC) -> select AZ1 (subnet01) -> enable elastic IP -> add SG (http and https) 
7. Create a Target group
8. Create a Load balancer
# Install curl
# Install Nginx
# Install Node.js and npm
### Step 3: Create a CI/CD pipeline for the app using the tools of your choice. 
#### Here, we are using Git Actions to set the CI/CD pipeline
1. Go to your GitHub repo -> click on "Action"
2. Select Continuous Integration -> Node js
3. It will create a file as -> .github/workflows/node.js.yml
4. do changes in the node.js.yml file  a) remove pull, b) add self-hosted instead of ubuntu-hosted c) remove the last 2 instructions
5. Now we need to add a runner. go to settings -> Actions -> Runners -> new self-hosted runner [select Linux -> x86]
6. Copy each step and run it on the EC2 Ubuntu server
7. then finally give the following commands
   ```bash
    sudo ./svc.sh install
    sudo ./svc.sh start
  8. install pm2 for the production server
  9. check whether pm2 is installed or not:
  10. now let's configure the NGINX server
  Inside the server block, add a location block to proxy requests to your Node.js application running on port 3000:
  make the following changes to the default file:

#### Test Nginx Configuration:
#### Restart Nginx:
## ADD SSL with lets-encrypt

