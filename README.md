# Azure-Arc-and-AWS
Hybrid Cloud Deployment with Azure Arc and AWS – A Comprehensive Guide
In this detailed real-world use case, we will explore how to integrate Azure Arc with AWS, allowing seamless management of AWS resources using Azure's powerful services.
A large multinational enterprise runs workloads in both Azure and AWS but faces:
❌ Fragmented management – Different monitoring & security policies
❌ Inefficient compliance enforcement – No centralized governance
❌ Multi-cloud complexities – Managing workloads across multiple providers

💡 The Solution: Azure Arc with AWS
By using Azure Arc, the company can:
✅ Manage AWS EC2 instances from Azure
✅ Apply Azure Security & Policies on AWS resources
✅ Monitor AWS workloads with Azure Monitor & Log Analytics

📌 What Needs to Be Done?
Our goal is to:
✅ Onboard AWS EC2 instances into Azure Arc
✅ Enable Azure Policies for security & compliance on AWS
✅ Monitor AWS VMs using Azure Monitor & Log Analytics
✅ Automate deployment using Terraform

🌍 Final Architecture Overview:
1️⃣ AWS EC2 Instances → Workloads running in AWS
2️⃣ Azure Arc → Onboard AWS instances into Azure
3️⃣ Azure Monitor & Log Analytics → Collect performance logs
4️⃣ Azure Policies & Defender → Apply security & compliance controls

 Step-by-Step Implementation
🛠 Step 1: Set Up AWS EC2 Instances
First, let's launch EC2 instances that we will manage from Azure Arc.

➡️ Create AWS EC2 Instances
1️⃣ Log in to AWS Console → Navigate to EC2.
2️⃣ Click Launch Instance.
3️⃣ Choose Ubuntu 22.04 LTS as the OS.
4️⃣ Select an instance type (e.g., t2.medium for testing).
5️⃣ Configure security groups: Allow SSH (22) and Azure Arc (443, 80, 4433, 1688, 12000-12100).
6️⃣ Launch the instance.

🔹 Get the instance details:

bash
Copy code
aws ec2 describe-instances --query "Reservations[*].Instances[*].[InstanceId,PrivateIpAddress]" --output table
🛠 Step 2: Register AWS EC2 Instances with Azure Arc
Next, we will connect AWS EC2 instances to Azure Arc.

➡️ Enable Azure Arc in Your Subscription
1️⃣ Log in to Azure Portal → Go to Azure Arc.
2️⃣ Click Add a machine → Select Add using script.
3️⃣ Choose your Resource Group and Region.
4️⃣ Generate the script → Download OnboardingScript.sh.

➡️ Run the Script on AWS EC2
Log in to your AWS EC2 instance and run:

bash
Copy code
curl -o OnboardingScript.sh https://aka.ms/AzureArcInstallLinux
chmod +x OnboardingScript.sh
sudo ./OnboardingScript.sh
✅ Now, AWS EC2 instances are onboarded into Azure Arc!

🛠 Step 3: Apply Azure Security & Policies on AWS EC2
Once the AWS EC2 instances are onboarded, we can apply security policies using Azure:

➡️ Apply Security Policies in Azure
1️⃣ Go to Azure Policy → Assignments.
2️⃣ Select Azure Arc-enabled Servers as the scope.
3️⃣ Apply policies like:

Enforce Disk Encryption
Enable Endpoint Protection
Monitor Guest Configuration
4️⃣ Click Assign.
✅ Azure security policies are now enforced on AWS EC2 instances!

🛠 Step 4: Monitor AWS Instances with Azure Monitor
📌 Azure Monitor provides insights into AWS workloads by collecting logs & metrics.

➡️ Enable Azure Monitor for AWS
1️⃣ Open Azure Monitor → Log Analytics Workspaces.
2️⃣ Click Add Data Source → Choose Azure Arc-enabled servers.
3️⃣ Configure the Log Analytics agent:

bash
Copy code
wget https://aka.ms/AzureMonitorLinuxAgent
sudo dpkg -i AzureMonitorAgent.deb
4️⃣ Run queries in Azure Log Analytics:

sql
Copy code
Perf | where Computer contains "aws-instance"
✅ Now, AWS EC2 logs are visible in Azure Monitor!

🛠 Step 5: Automate Deployment with Terraform
📌 To automate everything, we will use Terraform.

➡️ Terraform Code to Deploy AWS EC2 & Register with Azure Arc
hcl
Copy code
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "arc_instance" {
  ami           = "ami-0abcdef1234567890"
  instance_type = "t2.medium"

  tags = {
    Name = "AzureArc-Managed-Instance"
  }
}

resource "null_resource" "install_arc" {
  provisioner "remote-exec" {
    inline = [
      "curl -o OnboardingScript.sh https://aka.ms/AzureArcInstallLinux",
      "chmod +x OnboardingScript.sh",
      "sudo ./OnboardingScript.sh"
    ]
  }

  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file("~/.ssh/aws-key.pem")
    host        = aws_instance.arc_instance.public_ip
  }
}
➡️ Deploy the Infrastructure
bash
Copy code
terraform init
terraform apply -auto-approve
✅ Terraform automates the entire setup, making it repeatable and scalable!

📌 – Business Impact
🔹 Centralized Management – AWS workloads are now managed from Azure Arc.
🔹 Enhanced Security & Compliance – Azure policies applied to AWS EC2.
🔹 Unified Monitoring – AWS logs visible in Azure Monitor.
🔹 Reduced Complexity – Single-pane-of-glass control across AWS & Azure.

🚀 The company now benefits from a seamless hybrid cloud strategy!
