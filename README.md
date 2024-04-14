# Problem Statement:
1. Create a HelloWorld app in NodeJs and push the code on github.
2. Setup the secure infrastructure for the App on AWS and host that app on Infrastructure.
3. Setup Ngnix to serve the application on port 80.
4. Install the SSL certificate from Lets Encrypt on the app and use the domain name "yourname".signiance.com. You can share the endpoint of your server or loadbalancer with me during review and I will map it with the domain.
5. Create a CI/CD pipleine for the app using tools of your choice.
6. It shouild build and deploy the code on push.
7. Changes should be visible on the above URL.
8. Document the assignment and share your learning.

# Solution:
## Base OS: Ubuntu 23.04

## Step 1. Create a HelloWorld app in NodeJS.
### A: Update Package Index and install Nodejs and npm

First, update the package and install the latest versions of packages.

```bash
sudo apt update
sudo apt install nodejs npm
```
### B. Verify Installation
After the installation is complete, verify that Node.js and npm were installed successfully.
```bash
node -v
npm -v
```
### C. Create a Project dir and write simpe code for HelloWorld
```bash
mkdir HelloWorld && cd HelloWorld
code app.js
```
### D. Steps to create a Nodejs app and source code
1. Create a new file named index.html in the same directory as the JavaScript file.
2. Copy and paste the HTML content above into the index.html file.
3. Save the index.html file.
4. Create a new file with a .js extension (e.g., app.js).
5. Copy and paste the JavaScript code above into the server.js file.
6. Save the app.js file.

source code for "index.html"
```bash
<!DOCTYPE html>
<html>
<head>
  <title>HelloWorld</title>
</head>
<body>
  <h1>HelloWorld Web Page</h1>
  <form method="post">
    <label for="username">Enter your name:</label>
    <input type="text" id="username" name="username">
    <button type="submit">Submit</button>
  </form>
</body>
</html>
```

source code for "app.js"
```bash
// Create a simple HTTP server using Node.js
const http = require('http');
const fs = require('fs');

// Define the hostname and port for the server to listen on
const hostname = '0.0.0.0';
const port = 3000;

// Read the HTML file for the web page
const indexPage = fs.readFileSync('index.html');

// Create the HTTP server
const server = http.createServer((req, res) => {
  // Set the HTTP status code and content type in the response header
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/html');

  // Check if the request method is POST
  if (req.method === 'POST') {
    // Collect data from the POST request
    let data = '';
    req.on('data', chunk => {
      data += chunk;
    });
    req.on('end', () => {
      // Extract the username from the POST data
      const username = data.split('=')[1];

      // Send the HTML response with the personalized greeting
      res.end(`<h1>Hello, ${username}!</h1>`);
    });
  } else {
    // Send the HTML page with the form to input the username
    res.end(indexPage);
  }
});

// Start the server and listen for incoming requests
server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

7. Open a terminal and navigate to the directory where the files are saved.
8. Run and test the following command to start the server:
```bash
node app.js
npm init => to create a pakage.json file
npm install => to create a package.json.lock file
```

### E. Dockerize your applciation
```bash
# Use an official Node.js runtime as the base image
FROM node:16-alpine

# Set the working directory in the container
WORKDIR /usr/src/app

# Copy the application files to the container
COPY . .

# Expose the port on which your Node.js app will run
EXPOSE 3000

# Command to run your Node.js application
CMD ["node", "app.js"]
```

Create the docker image from the dockerfile and run the container
```bash
docker build -t app .
docker run -d -p 3000:3000 app
```
test the app on localhost:3000

### F. Push the code on github
1. create a new repo on github with name: "HelloWorld"
2. On your local machine in project folder give the following commands
  ```bash 
  > git init
  > git branch -M main
  > git remote add origin https://github.com/vishal-meshram/HelloWorld.git
  > git push -u origin main 

### G. Git comamnds on local machine for branch sync
```bash
git checkout main
git pull --rebase
git rebase main
git push -f origin <my_branch>
```

## Step 2: Setup the secure infrastructure for the App on AWS and host that app on Infrastructure.
### For secure infrastructure we will create own VPC, IGW, RT, Subnets, ALB and use 
1. Create a VPC with CIDR: 10.0.0.0/16
2. Create a two subnets: az1a: 10.0.1.0/24  and az1b: 10.0.2.0/24
3. Create a IGW
4. Create a Route tables -> Edit subnet association and Add rule
5. Create a Security Group: allow 80 and 443 and 22
6. Create a Ec2 instance -> ubuntu -> Network (Select Node-project-VPC) -> select AZ1 (subnet01) -> enable elastic IP -> add SG (http and https) 
7. Create a Target group
8. Create a Load balancer

please run following commands on  ec2 instance:
```bash
sudo -i
sudo apt update

# Install curl
sudo apt install -y curl

# Install Nginx
sudo apt install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl status nginx

# Install Node.js and npm
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs
```
#check the installation on ec2 by using following commands
```bash
sudo -i
node -v
npm -v
systemctl status nginx
```

### Step 3: Create a CI/CD pipleine for the app using tools of your choice. 
#### Here, we are using Git Actions to setup CI/CD pipleine
1. Go to your github repo -> click on "Action"
2. Select Continious Integration -> Node js
3. It will create a file as -> .github/workflows/node.js.yml
4. do changes in the node.js.yml file  a) remove pull , b) add self-hosted insted ubuntu-hosted c) remove last 2 instructions
5. Now we need to add runner. go to settings -> Actions -> Runners -> new self-hosted runner [select linux -> x86]
6. Copy each step and run on ec2 ubuntu server
7. then finally give the following commands
   ```bash
    sudo ./svc.sh install
    sudo ./svc.sh start
  ```
  this will create a _work folder

  8. install pm2 for prodcution server
  ```bash
  sudo npm install pm2@latest -g
  OR
  sudo npm i -g pm2
  ```
  9. check whether pm2 is installed or not:
   ```bash
   > pm2
   ```
  10. now lets configure NGINX server
  ```bash
  sudo nano /etc/nginx/sites-available/default
  ```
  Inside the server block, add a location block to proxy requests to your Node.js application running on port 3000:
  make the following changes into default file:
  ``` bash
  server {
    listen 80;
    server_name <loadbalancer dns> http://helloworld-alb-44359135.ap-south-1.elb.amazonaws.com/;  # Replace with your domain name or server IP

    location / {
        proxy_pass http://localhost:3000;  # Forward traffic to Node.js app running on port 3000
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
#### Test Nginx Configuration:

Before applying the changes, it's a good idea to test the Nginx configuration for syntax errors:

```bash
sudo nginx -t
```
#### Restart Nginx:

If the configuration test is successful, restart Nginx to apply the changes:
```bash
    sudo systemctl restart nginx
```
go to project dir:
```bash
cd ~/actions-runner/_work/HelloWorld/HelloWorld/
pm2 start app.js --name="backend"
```
## Now complete the CI/CD pipleine 
goto your Gitrepo project: and add command in this file: .github/workflows/node.js.yml
```bash
 # This workflow will do a clean installation of node dependencies, cache/restore them, build the source code and run tests across different versions of node
# For more information see: https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-nodejs

name: Node.js CI

on:
  push:
    branches: [ "main" ]
  
jobs:
  build:

    runs-on: self-hosted

    strategy:
      matrix:
        node-version: [16.x, 18.x]
        # See supported Node.js release schedule at https://nodejs.org/en/about/releases/

    steps:
    - uses: actions/checkout@v3
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    - run: npm ci
    - run: pm2 restart backend
    ```
On lyour lcoal machine now run following commands
```bash
git checkout main
git pull --rebase
git rebase main
git push -u origin main
```
Now, if you made any changes in the code and push to GitHub repo they will be automatically reflected..


## ADD SSL with lets-encrypt
```bash
sudo add-apt-repository ppa:certbot/certbot
sudo apt update
sudo apt install python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Only valid for 90 days, test the renewal process with
certbot renew --dry-run
```
