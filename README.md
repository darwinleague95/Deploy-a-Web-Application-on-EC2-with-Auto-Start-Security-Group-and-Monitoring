# Deploy a Web Application on EC2 with Auto Start, Security Group, and Monitoring

This project demonstrates how to deploy a web application on an AWS EC2 instance with:

- ✅ Apache web server setup
- 🔐 Custom security group for public access
- 🔁 Auto-start configuration on reboot
- 📈 CloudWatch monitoring for resource tracking

---

## 📦 Project Structure

.
├── my_work.html # Your main web page
├── scripts/
│ └── startup.sh # Startup script for auto-start
├── monitoring/
│ └── cloudwatch-agent.json # CloudWatch config (optional)
└── README.md # This file

yaml
Copy code

---

## 🔧 Requirements

- AWS EC2 instance (Amazon Linux 2)
- Key Pair to SSH into instance
- Security Group allowing HTTP (80) and SSH (22)
- Internet access to install packages

---

## 🚀 Deployment Instructions

### 🔹 1. Launch EC2 Instance

- AMI: **Amazon Linux 2**
- Instance Type: `t2.micro` (Free Tier)
- Key Pair: Use or create one (e.g., `my-key.pem`)
- Security Group Rules:
  - **HTTP (80):** `0.0.0.0/0`
  - **SSH (22):** Your IP only
- Launch and wait until the instance is running.

---

### 🔹 2. Connect to Instance

```bash
ssh -i my-key.pem ec2-user@3.88.135.6
🔹 3. Install Apache and Deploy Website
bash
Copy code
sudo yum update -y
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
Copy your HTML file to the Apache root:

bash
Copy code
sudo cp my_work.html /var/www/html/index.html
✅ Now open in your browser:
cpp
Copy code
http://3.88.135.6
🔹 4. Auto-Start on Reboot
Create a script:

bash
Copy code
nano /home/ec2-user/startup.sh
Paste:

bash
Copy code
#!/bin/bash
sudo systemctl start httpd
Then:

bash
Copy code
chmod +x startup.sh
crontab -e
Add this line at the bottom:

bash
Copy code
@reboot /home/ec2-user/startup.sh
🔹 5. Setup CloudWatch Monitoring (Optional)
Install agent:

bash
Copy code
sudo yum install amazon-cloudwatch-agent -y
Create /opt/cloudwatch-agent/config.json with:

json
Copy code
{
  "metrics": {
    "metrics_collected": {
      "cpu": {
        "measurement": ["cpu_usage_idle", "cpu_usage_iowait"],
        "metrics_collection_interval": 60
      },
      "mem": {
        "measurement": ["mem_used_percent"],
        "metrics_collection_interval": 60
      }
    }
  }
}
Start the agent:

bash
Copy code
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
-a fetch-config \
-m ec2 \
-c file:/opt/cloudwatch-agent/config.json \
-s
🌐 Final App URL
cpp
Copy code
http://3.88.135.6
Your site should now be publicly accessible and auto-start even after a reboot.

📌 Notes
You can replace my_work.html with any HTML project.

Use systemctl status httpd to verify Apache service.

Use CloudWatch to monitor CPU, memory, and disk usage over time.



🔒 Why Elastic IP is Recommended:
Normal EC2 IP changes every reboot

Elastic IP is static and permanent (until you release it)

Great for:

Hosting web apps

DNS routing

Maintaining uptime

📜 License
MIT License © 2025 Darwin Alex
