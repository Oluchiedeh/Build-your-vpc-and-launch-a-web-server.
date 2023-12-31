# Build-your-vpc-and-launch-a-web-server.

**A Step-by-Step Guide to Building Your VPC and Launching a Web Server on AWS.**

![](diagram.jpeg)

*Architecture diagram.*

Virtual Private Cloud (VPC) allows you to launch AWS resources into a virtual network that you defined.

In the contemporary digital arena, establishing a Virtual Private Cloud (VPC) and deploying a web server provides fundamental pillars for multiple online ventures. Amazon Web Services (AWS), a cloud computing platform, has established robust tools to create a secure VPC environment and effortlessly launch a web server in the cloud. This comprehensive guide is designed to assist you through the entire process.

# Prerequisite:

- Create VPC, and add components to produce a customized network.
- Create security group
- Configure an EC2 instance to run a web server.
- Launch the EC2 instance to run in a subnet in the VPC.

# Task 1: Create Your VPC.

- Let's start by creating a VPC. Make sure you are in the **N. Virginia (us-east-1)** region.
- Where the service button is positioned, click on it and search for VPC, click on it, and next click on **Create VPC.**

![](vpc1.jpeg)

*VPC dashboard.*

**1. Configure the VPC details:**

- Choose **VPC and more.**
- Under **Name tag auto-generation**, keep **Auto-generate** selected, however, change the value from project to **lab**
- Keep the **IPv4 CIDR block** set to **10.0.0.0/16**

![](vpc2.jpeg)

*VPC Configuration process.*

- For the **Number of Availability Zones**, choose **1.**
- For the **Number of public subnets**, keep the **1** setting.
- For the **Number of private subnets**, keep the **1** setting.
- Expand the **Customize subnets CIDR blocks** section

 Change **Public subnet CIDR block** in us-east-1a to **10.0.0.0/24**
 Change **Private subnet CIDR block** in us-east-1a to **10.0.1.0/24**

![](vpc3.jpeg)

*VPC Configuration process.*

- Set **NAT gateways** to In **1 AZ.**
- Set **VPC endpoints** to **None.**
- Keep both **DNS hostnames** and **DNS resolution enabled.**
- Then click on the **`Create VPC`** button.

![](vpc4.jpeg)

*Configuration end page.*

**Note:** The VPC wizard has created a VPC with a public subnet and a private subnet on one availability zone with route tables for each subnet. And also an internet Gateway and NAT Gateway.
![](vpc5.png)

*created VPC network.*

# Task 2: Create Additional Subnets:

Here, create two additional subnets to the ones previously provisioned, a private and a public subnet in a second availability zone. 
Having subnets in multiple Availability zones within a VPC is useful for deploying a solution that provides high availability.

- On the left navigation pane, choose **Subnet**, and create a public subnet first.
- Choose **Create subnet** then configure:

 ![](subnet1.jpeg) 
  
- VPC ID: **lab-vpc** (select from the menu).
- Subnet name: **lab-subnet-public2**

![](subnet2.jpeg) 

- Availability Zone: Select the second Availability Zone (for example, us-east-1b)
- IPv4 CIDR block: **10.0.2.0/24**

![](subnet3.jpeg) 

- Choose **Create subnet**.

![](subnet4.jpeg) 

- Now, create the second private subnet.
- Choose **Create subnet** then configure:

![](subnet5.jpeg) 

- VPC ID: **lab-vpc**
- Subnet name: **lab-subnet-private2**

  ![](subnet6.jpeg)
  
- Availability Zone: Select the second Availability Zone (for example, us-east-1b)
- IPv4 CIDR block: **10.0.3.0/24**
- Choose **Create subnet**

  ![](subnet7.jpeg)

To enable internet-bound traffic for the new private subnet, you'll configure it to route through the NAT Gateway while maintaining the privacy of the resources. 
This involves setting up a Route Table.

A Route Table consists of rules, known as routes, which dictate the path of network traffic. Every subnet within a VPC needs to be linked to a route table; this table governs the routing specifics for that subnet.



In the left navigation pane, choose Route Tables.
Select the lab-rtb-private1-us-east-1a route table.
In the lower pane, choose the Routes tab.
![](routeT1.jpeg)

Choose the Subnet Associations tab.

You created this route table in task 1 when you chose to create a VPC and multiple resources in the VPC. 
That action also created lab-subnet-private-1 and associated that subnet with this route table.

Now that you have created another private subnet, **lab-subnet-private-2**, you will associate this route table with that subnet as well.
- In the Explicit subnet associations panel, choose **Edit subnet associations.**
- Leave **lab-subnet-private1-us-east-1a** selected, but also select **lab-subnet-private2.**
- Choose Save associations

![](routeT2.jpeg)

- You will now configure the Route Table that is used by the Public Subnets.
- Select the **lab-rtb-public route table** (and deselect any other subnets).
- In the lower pane, choose the **Routes tab.**

![](routeT3.jpeg)

- Choose the Subnet Associations tab.
- In the Explicit subnet associations area, choose Edit subnet associations
- Leave **lab-subnet-public1-us-east-1a** selected, but also select **lab-subnet-public2**.
- Choose **Save associations.**

![](routeT4.jpeg)

Your VPC now has public and private subnets configured in two Availability Zones. 

The route tables you created in Task 1 have also been updated to route network traffic for the two new subnets.

You will have this diagram now, gradually the architectural diagram is coming to its final state.


![](ARCHITECTURE.png)

Architectural diagram.

# Task 3: Create a VPC Security Group:

During this task, your objective is to generate a VPC security group functioning as a virtual firewall. 
As you launch an instance, you'll pair one or multiple security groups with it. 
These security groups can be customized by adding rules that dictate the allowance or restriction of traffic to and from the instances they're linked to.

- In the left navigation pane, choose **Security Groups.**
- Choose **Create security group** and then *configure*:
  
![](SG1.jpeg)

- Security group name: **Web Security Group**
- Description: **Enable HTTP access**
- VPC: choose the X to remove the currently selected VPC, then from the drop-down list choose **lab-vpc.**

![](SG2.jpeg) 

- In the Inbound rules pane, choose **Add rule**
- Configure the following settings:
- Type: **HTTP**
- Source: **Anywhere-IPv4**
- Description: **Permit web requests.**

![](SG3.jpeg) 

- Scroll to the bottom of the page and choose **Create security group**.

![](SG4.jpeg) 

- You will use this security group in the next task when launching an Amazon EC2 instance.

# Task 4: Launch a Web Server Instance:

During this task, you aim to initiate an Amazon EC2 instance within the new VPC. Your configuration for this instance will gear it to function as a web server.

- In the search box to the right of **Services**, search for and choose **EC2** to open the EC2 console.
- From the Launch instance menu choose **Launch instance.**

![](EC2-1.jpeg) 

Name the instance:

- Give it the name **Web Server 1**.

![](EC2-2.jpeg) 

Choose an **AMI** from which to create the instance:

- In the list of available **Quick Start AMIs**, keep the default **Amazon Linux** selected.
- Also, keep the default **Amazon Linux 2023 AMI** selected.

![](EC2-3.jpeg) 

Choose an Instance type:

- In the Instance type panel, keep the default **t2.micro** selected.

![](EC2-4.jpeg) 

Select the key pair to associate with the instance:
- From the Key pair name menu, select **Create key pair**.
- Name: **Web-server**.

![](EC2-5.jpeg) 

Configure the Network settings:

- Next to Network settings, choose **Edit**, then configure:
- Network: **lab-vpc**
- Subnet: **lab-subnet-public2** (not Private!)
- Auto-assign public IP: **Enable**.
- Next, you will configure the instance to use the **Web Security Group** that you created earlier.
- Under Firewall (security groups), choose Select existing **security group**.
- For Common security groups, select **Web Security Group**.
  
- This security group will permit HTTP access to the instance.

![](EC2-6.jpeg) 

- In the Configure storage section, keep the default settings.
- Configure a script to run on the instance when it launches:
- Expand the **Advanced details** panel.
- Scroll to the bottom of the page and then copy and paste the code shown below into the **User data** box:

`#!/bin/bash`

*Install Apache Web Server and PHP*

`dnf install -y httpd wget php mariadb105-server`

 *Download Lab files*

`wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-100-ACCLFO-2/2-lab2-vpc/s3/lab-app.zip
unzip lab-app.zip -d /var/www/html/`

*Turn on the web server*

`chkconfig httpd on`

`service httpd start`

*The script installs a web server, a database, and PHP libraries, and then it downloads and installs a PHP web application on the web server.*

![](EC2-7.jpeg) 

# Congratulation!!

![](AWS.png) 

**Conclusion:** 

Building a VPC and setting up a web server on AWS involves integrating several components seamlessly. This guide acts as a fundamental blueprint, guaranteeing a secure and efficient setup for hosting web services. It's crucial to prioritize security, adhere to best practices, and consistently monitor and refine your VPC to adapt to changing needs.

By mastering the art of VPC creation and web server deployment, you're poised to leverage the scalability, reliability, and security that AWS offers, empowering your online presence to thrive in today's dynamic digital landscape.
