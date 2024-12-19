# Cloud Networking by building a VPC (Virtual Private Cloud)

![image](https://github.com/user-attachments/assets/4fabcb19-53aa-4d2e-bc5f-5d7894a32231)

Objectives:
1. Deploying instances in subnets within a VPC
2. Exposing the instances from the subnet to the internet using internet gateway

![image](https://github.com/user-attachments/assets/b371f742-aed1-49e7-aff6-16d6f801c053)


Steps:
1. Build a VPC
- VPC allows you to launch AWS resources in a logically isolated virtual network so that we could operate in our own data center with the scalable infrastructure
- A VPC is created within a region and it is powered by multiple availability zones. Availability zones are the clusters of data centers to work with
- The subnets are the subdivisions of the VPC and it lies within availability zone. It can also be associated with other AZs in a region. But an AZ can have only one subnet at once
-By default, AWS provides a VPC for the user to logically deploy resources in it. Also a user can create multiple VPCs on own

![image](https://github.com/user-attachments/assets/c9dcc972-457e-4ed7-b905-3872c35921f2)

2. A VPC is created by giving specific tag name for identification, selecting IPv4 CIDR block and unchecking IPv6 CIDR block.
- IPv4 is the most commonly used range of IP addresses. It ranges from 0.0.0.0 to 255.255.255.255 and it is a 32-bit (4*8-bit) address
- CIDR (Classless Inter-Domain Routing) block represents the number of fixed and varied bits. If the address is 10.1.0.1/16 then the first 16-bits are fixed and the next 16-bits are varied. So the IP ranges from 10.1.0.1 to 10.1.255.255

![image](https://github.com/user-attachments/assets/5fa6b42b-7a9a-4b1e-88fd-3c1c1892e3d7)

- Like VPC, AWS provides default subnets in each AZs

![image](https://github.com/user-attachments/assets/03a29fe7-4606-4aa8-b96a-ac445ea1422a)

3. Created a public subnet and enable auto-assign address to make AWS allocate the IP address within the range we specified for the resources deployed in it

![image](https://github.com/user-attachments/assets/b9fa6372-f7f0-43fa-a5b2-76b467b6b0f2)

4. Created an Internet Gateway to route the traffic from and to outside. Initially the state of gateway was detached. So it is attached to the VPC created

![image](https://github.com/user-attachments/assets/eff4d419-5e1e-4398-b48f-23c195b348d8)

![image](https://github.com/user-attachments/assets/1dc0bb42-b02e-44a9-b97b-502286a404cb)

5. Enabled route tables
- Creating an internet gateway and attaching it to VPC does not make resources connect to internet outside or receive traffic from it
- Route tables (like a collection of IP addresses and its ranges) only will direct the traffic from resources to internet and vice versa
- A subnet can have only one route table and multiple subnets can have same route table
- AWS creates a default route table for the user

![image](https://github.com/user-attachments/assets/90b534bb-4dd4-4278-8dd7-4bed0e03057b)

- In one route table, two routes are available. One with 0.0.0.0/0 as destination and default IG as the target. This means, it will route the traffic to that IG from anywhere. In second one, 172.31.0.0/16 (IP address of default VPC) as destination and target is local. This means, the traffic is routed within the resources inside VPC having the entire address space 172.31.0.0/16
- In another route table, only one route is mentioned with the IP address of the VPC created (10.0.0.0/16). This means the traffic flows within the VPC and not outside. Now to this, add another route with 0.0.0.0/0 as destination and IG created as target. So that it routes the traffic from anywhere to that IG

![image](https://github.com/user-attachments/assets/45b53ba3-5f34-4a64-9d97-ec8d60e588c5)

- Also, when the IP addresses overlap, AWS gives priority to the restrictive one first (here, the VPC we created and its resources) and then the traffic from anywhere
- Associate the subnet created with the route tables created. If not created, it will associate with the main route table

6. EC2 instance is created and deployed in the subnet

7. Security group is enabled over the instance
- Security group is like a firewall for every resource inside the subnet/VPC. They have strict rules on what kind of traffic comes in or goes out of the resources based on their IP address, port and protocol
- Both inbound and outbound rules can be enabled. Inbound rules for the traffic coming into resource. The protocol can be anything like HTTP, HTTPS, SSH, FTP, SMTP etc. with port numbers like 80, 443, 22, 21, 25 etc. Same applicable for the outbound rules which is for the traffic going outside from the resources.

![image](https://github.com/user-attachments/assets/aed3baa1-ce31-481c-90c7-600d28506e27)

8. Added NACL (Network Access Control List)
- NACL is an additional layer of security at the subnet level. It monitors the traffic coming in and out of the subnet. It checks against the ACL table before allowing them inside subnet
- It is like a combination of route table and security group
- AWS creates a default NACL and it allows all traffic until it is customised
- The rules in both inbound and outbound rules of the NACL works on priority. If the rule 100 is failing, then * works and allows traffic. Else it won't. Similarly if there are two rules like rule 100, 200 and * in order, where 100 allows https traffic, 200 allows http traffic and * allows all traffic, then, if 100 fails, 200 works. If 200 fails, then * works

![image](https://github.com/user-attachments/assets/a31da872-b82d-4bf0-867f-2b60f73e54ea)

9. Created a private subnet, private route table and private NACL
- This helps in securing the resources from the outside traffic. They will be isolated from internet gateway
- If they want to communicate to the internet, then NAT (Network Address Translation) gateway is created and associated with them

![image](https://github.com/user-attachments/assets/4ecbac15-8bb9-424b-9127-0ac1313d67c0)

![image](https://github.com/user-attachments/assets/317a6577-2622-4cb9-9de5-f821285ed0f9)

![image](https://github.com/user-attachments/assets/ba768e07-3612-4ea3-85c3-672d66dd0535)

10. Launching resources inside subnets in a VPC
- Deploying both private and public instances inside private and public subnets in the VPC
- Note: Adding public security group created for the public instance as the source for the inbound SG in private instance enables the secured communication between two instances

![image](https://github.com/user-attachments/assets/ea56deaf-e066-4113-b210-c68a331a850c)

![image](https://github.com/user-attachments/assets/80c84df7-ba2a-4986-969c-84b506ea76f1)

11. Made SSH connectivity with the public instance using EC2 instance connect. Also checked the connectivity between private and public instances
- Ping messages are mostly blocked by the private instances using ICMP (Internet Control Message Protocol) to prevent network attacks

![image](https://github.com/user-attachments/assets/640f48ba-982f-4d99-ba5f-982e767ed617)

- The inbound and outbound rule of the NACL and security group of the private instance is changed in a way to allow those messages

![image](https://github.com/user-attachments/assets/44b8986d-9141-4e66-a3fd-eef3bc99175e)

![image](https://github.com/user-attachments/assets/bfce593f-86f2-4092-9ec0-b4518bc6b30d)

![image](https://github.com/user-attachments/assets/2f2447d9-c9f1-4488-b407-1f5c3f1de7dd)

- Curl message is used to test the connectivity between public instance and public internet

![image](https://github.com/user-attachments/assets/4192ebe9-4a40-41f9-8798-a27d627b6da9)
