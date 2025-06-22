# -AWS-Cloud-MERN-Deployment
The Travel Memory application has been developed using the MERN stack
Project Repository: https://github.com/UnpredictablePrashant/TravelMemory
# Objective:
- Set up the backend running on Node.js.
- Configure the front end designed with React.
- Ensure efficient communication between the front end and back end.
- Deploy the full application on an EC2 instance.
- Facilitate load balancing by creating multiple instances of the application.
- Connect a custom domain through Cloudflare.


# Setup EC2 Instances:
Log in to AWS Management Console. Navigate to EC2 Dashboard and click "Launch Instance". Choose Ubuntu Server as the OS, instance type and key pair for SSH access. Configure Security Group to allow ports 22 (SSH), 80 (HTTP), 443 (HTTPS), and 3000-3001 (backend/Frontend) and Launch the instance for backend. Repeat the same steps to again one instance for Front end.

# Set up MongoDB Database:
Sign up and log in to MongoDB Atlas. Create a new project and build a free-tier cluster. Create a database user with username and password. Also give permission allow to anyone host. Database can be access from local Machine with following:
-	Install MongoDB Compass on local machine
-	New Connection
-	Enter URI (mongodb+srv://mlal:password@mlal-b1.vf0r1ta.mongodb.net/)
-	For reference the screen shots:
![image](https://github.com/user-attachments/assets/0e4c51b4-c029-449e-8501-8d70996a3f5f)

![image](https://github.com/user-attachments/assets/da1a615a-ec02-4a56-8b1b-6b2b11d1d6c7)

# Configure Backend EC2 Instance:
    -	Connect to the backend instance using SSH with the key pair.
    -	Clone the Repository: git clone <Reposity_URL>
    -	Jump to Backend directory: cd TravelMemory/backend/ 
    -	Create .env file: sudo nano .env 
    -	Then set DB login URI variables in .env file:
        o	MONGO_URI='ENTER_YOUR_URL' (For me it will be ‘mongodb+srv://mlal:<password>@mlal-b1.vf0r1ta.mongodb.net/’)
        o	PORT=3001
        o	CTRL+X then Yes and Enter key press
    -	Install NodeJs & NPM: 
        o	sudo apt update
        o	sudo apt upgrade -y
        o	curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
        o	sudo apt install -y nodejs 
    -	Run Backend Application
        o	sudo node index.js

  ![image](https://github.com/user-attachments/assets/d47762d9-990e-4448-a754-6431c31e2d72)



# Configuring frontend and linking to the backend:
    -	Connect to the frontend instance using SSH with the key pair.
    -	Clone the Repository: git clone <Repository_URL>
    -	Jump to Frontend directory: cd TravelMemory/frontend/ 
    -	Create .env file: sudo nano .env 
    -	Then setup the backend URL to .env file:
      o	REACT_APP_BACKEND_URL=http://localhost:3001 (Localhost will be replaced with backend public IP)
      o	CTRL+X then Yes and Enter key press
    -	Install NodeJs & NPM: 
      o	sudo apt update
      o	sudo apt upgrade -y
      o	curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
      o	sudo apt install -y nodejs 
    -	Run Backend Application
      o	sudo npm start

![image](https://github.com/user-attachments/assets/221f1264-b884-45b9-a5cf-90c3bf0053b4)


# Set up a reverse proxy using nginx on backend and frontend:
  -	Install nginx and configure:
      o	sudo apt install nginx -y
      o	sudo systemctl start nginx
      o	Open config ngix file and configure on port:80
      server {
          listen 80;
          location / {
              proxy_pass http:// 35.160.154.34:3001;  # Your backend application
              proxy_http_version 1.1;
              proxy_set_header Host $host;
              proxy_set_header X-Real-IP $remote_addr;
              proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
              proxy_set_header X-Forwarded-Proto $scheme;
          }
      }
  -	Reload NGINX
      o	sudo nginx -t        # Test configuration
      o	sudo systemctl reload nginx  # Reload to apply changes
  -	After reverse proxy backend will be accessible by IP only from browser and repeat same steps for frontend also.

![image](https://github.com/user-attachments/assets/0db38c0b-1510-459b-ad8a-5a8f06e6f524)


# Setup load balancer to ensure efficient distribution:
  -	Create AMI template images for both the frontend and backend instances
  -	Then launch new instances from these templates. 
  -	Create Target groups. 
  -	Navigate to AWS Elastic Load Balancer (ELB) and select Create Load Balancer. 
  -	Attach the target group to the Load Balancer listener on port 80.
  -	 Configure Health checks to ensure traffic only routes to healthy instances. 
  -	Verify the setup by accessing the ELB DNS name to confirm load-balanced access to the application.

![image](https://github.com/user-attachments/assets/32b7a313-f958-4f82-bbf7-db3c2f247c3f)

![image](https://github.com/user-attachments/assets/44c180c1-9257-4b1f-8366-6cf124c93d2b)

![image](https://github.com/user-attachments/assets/c1e935c3-95cb-435b-b038-6a3be7821168)

![image](https://github.com/user-attachments/assets/33a8cc3f-47ac-4c65-a199-e83f7872dd80)


# Integrating a custom domain using Cloudflare:
  -	Create account on cloudflare and logged in
  -	Add domain from dashboard and already purchased a domain: w3developement.online
  -	Update DNS Nameservers of cloudflare to domain registration portal
  -	Created a CNAME record under DNS for AWS ELB DNS name pointing to w3developement.online  in Cloudflare.
  -	Created an SSL certificate using AWS Certificate Manager (ACM) and attached it to the Elastic Load Balancer to enable HTTPS via Cloudflare's SSL/TLS settings.


![image](https://github.com/user-attachments/assets/e2736477-d055-4844-9c84-fb8c9f8f18a6)


# Final Testing results with https://www.w3developement.online:

![image](https://github.com/user-attachments/assets/91ea1f7a-42f0-47a8-9941-ceb5e0ce40b5)


# Design a deployment architecture diagram:


![aws_deployment](https://github.com/user-attachments/assets/3277e61f-803d-4c04-9c83-fb95da6c95d6)

    
    
    
