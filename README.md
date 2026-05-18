# Update and upgrade system packages

    sudo apt update && sudo apt upgrade -y

# Install Node.js 20.x

    curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
    sudo apt install nodejs -y

# Verify Node.js and npm versions

    node -v
    npm -v

# Install PM2 globally
    sudo npm install -g pm2

# Install and configure Nginx

    sudo apt install nginx -y
    sudo systemctl enable nginx
    sudo systemctl start nginx

# Create project directory and initialize Node.js app
    mkdir cicd-project
    cd cicd-project
    npm init -y
    npm install express

# Create app.js (write any app.js file)
    nano app.js

# Run app locally (check it is runnnig or not)
    node app.js

allow security group

     http://YOUR_PUBLIC_IP:3000

# Start app with PM2
    pm2 start app.js --name cicd-app
    pm2 status
    pm2 startup
    pm2 save

# Configure Nginx reverse proxy
    sudo nano /etc/nginx/sites-available/cicd-app      

    
    server {
    listen 80;

    server_name _;

    location / {
        proxy_pass http://localhost:3000;

        proxy_http_version 1.1;

        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;

        proxy_cache_bypass $http_upgrade;
      }
    }

delete default file

    sudo ln -s /etc/nginx/sites-available/cicd-app /etc/nginx/sites-enabled/
    sudo rm /etc/nginx/sites-enabled/default

# Test and restart Nginx

    sudo nginx -t
    sudo systemctl restart nginx


create a repo on github =name=  cicd-nodejs-project

    git init
    git add .
    git commit -m "initial commit"
    git branch -M main
    git remote add origin https://github.com/asthakwh/cicd-nodejs-project.git
    git push -u origin main

generate SSH key for github action

    ssh-keygen -t rsa
    cat ~/.ssh/id_rsa.pub
add it to

    GitHub Repo → Settings → Deploy Keys

    Title: ec2-key
    Paste public key with allow write access

now we have to add github secret

    | Secret Name | Value                        |
    | ----------- | ---------------------------- |
    | EC2_HOST    | Your EC2 Public IP           |
    | EC2_USER    | ubuntu                       |
    | EC2_KEY     | cat ~/.ssh/id_rsa            |

write deploy. yml file

    mkdir -p .github/workflows
    nano .github/workflows/deploy.yml

paste deploy.yml from code

    git add .
    git commit -m "Added CI/CD pipeline"
    git push

GitHub Repo → Actions = it deployed automatically
    
