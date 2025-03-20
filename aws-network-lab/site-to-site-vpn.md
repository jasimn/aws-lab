# AWS Site-to-Site VPN Setup Guide

By Chetan Agrawal  
[https://www.awswithchetan.com](https://www.awswithchetan.com)

---

## Architecture

### Mumbai Region
- **Region**
  - VPC-AWS: `10.100.0.0/16`
  - Availability Zone 1
    - Private subnet
    - Private IP
      - EC2-A: `10.100.0.0/24`

### N. Virginia Region
- **Region**
  - VPC-DC: `192.168.0.0/16`
  - Availability Zone 1
    - Public subnet
    - Public IP
      - EC2-VPN: `192.168.0.0/24`

### AWS Network
- **IPSec VPN**
  - IPSec VPN

### Customer Network
- **Libreswan VPN software**

---

## Steps

### 1. Create VPCs
- Create `VPC-AWS` (`10.0.0.0/16`) in the Mumbai region and `VPC-DC` (`192.168.0.0/16`) in the N. Virginia region.
  - **For VPC-AWS**:
    - Create one private subnet, corresponding route table, and associate it with the subnet.
  - **For VPC-DC**:
    - Create an Internet Gateway (IGW), associate it with `VPC-DC`.
    - Create one public subnet, corresponding route table, and associate the route table with the subnet.

### 2. Launch EC2 Instances
- Launch EC2 instances in both VPCs:
  - **In VPC-AWS**:
    - The instance will have only a private IP.
    - Configure the security group to allow All ICMP IPv4 traffic from the `VPC-DC` CIDR (`192.168.0.0/16`).
  - **In VPC-DC**:
    - Launch an EC2 instance (`EC2-VPN`) with Amazon Linux 2023 AMI.
    - The instance should have a public IP.
    - Configure the security group to allow:
      - All ICMP IPv4 traffic for the `VPC-AWS` CIDR.
      - SSH access from `MyIP` or `0.0.0.0/0`.
    - Disable source/destination check for `EC2-VPN`:
      - Go to **Actions -> Networking -> Source/Destination Check -> Stop**.

### 3. Create Virtual Private Gateway
- In the Mumbai region:
  - Go to the VPC console -> **Virtual Private Gateways** -> **Create Virtual Private Gateway** (use default ASN).
  - Name: `VPC-AWS-VGW` -> **Create Virtual Private Gateway**.
  - Select the VGW -> **Actions -> Attach to VPC** -> Select `VPC-AWS` from the dropdown -> **Attach to VPC-AWS**.
  - Wait for the attachment to complete.
  - Modify the `VPC-AWS` private subnet route table and add a route for `0.0.0.0/0` with the target as the Virtual Private Gateway (`vgw-xxxxx`).
  - Note down or copy the public IP of the `EC2-VPN` instance in the N. Virginia region.

### 4. Create VPN Connection
- In the VPC console -> **Site-to-Site VPN Connections** -> **Create VPN Connection**.
  - Name: `AWS-DC-VPN`.
  - Target gateway type: **Virtual Private Gateway**.
  - Select the VGW from the dropdown.
  - Customer Gateway: **New**.
    - Enter the public IP address of the `EC2-VPN` EC2 instance.
  - Routing options: **Static**.
    - Static IP prefixes: `192.168.0.0/16`.
  - Local IPv4 network CIDR: `192.168.0.0/16`.
  - Remote IPv4 network CIDR: `10.0.0.0/16`.
  - **Create VPN Connection** and wait for the connection to complete.

### 5. Download the Configuration File
- Select the VPN connection you created -> **Download Configuration**.
  - Select vendor as **Openswan** -> **Download**.
  - Save the configuration file on your local machine and open it in Notepad.

### 6. Install and Configure DC VPN Server
- SSH into `EC2-VPN` from your workstation using PuTTY or any SSH client.
- Install Libreswan:
  ```bash
   sudo yum install libreswan
###Ope7.n the downloaded VPN server configuration file and follow the instructions. The instructions should include the following steps:
- Open /etc/sysctl.conf and ensure the following values:
   ```bash
    net.ipv4.ip_forward = 1
    net.ipv4.conf.default.rp_filter = 0
    net.ipv4.conf.default.accept_source_route = 0
- Apply the changes:
  ```bash
   sysctl -p
###8.Open /etc/ipsec.conf and ensure the following line is uncommented:
- go to /etc/ipsec.conf
   ```bash
    vim /etc/ipsec.conf
- make sure this line is uncommented:
  ```bash
   #include /etc/ipsec.d/*.conf
###9.Create a new file at /etc/ipsec.d/aws.conf (if it doesn't exist) and append the following configuration:
 ```bash

 conn Tunnel1
   authby=secret
   auto=start
   left=%defaultroute
   leftid=<Public IP of EC2-VPN>
   right=<AWS VPN Public IP>
   type=tunnel
   ikelifetime=8h
   keylife=1h
   phase2alg=aes256-sha1;modp2048
   ike=aes256-sha1;modp2048
   keyingtries=%forever
   keyexchange=ike
   leftsubnet=192.168.0.0/16
   rightsubnet=10.0.0.0/16
   dpddelay=10
   dpdtimeout=30
   dpdaction=restart_by_peer
   encapsulation=yes
Create a new file at /etc/ipsec.d/aws.secrets (if it doesn't exist) and append the following line:

bash
Copy
<Public IP of EC2-VPN> <AWS VPN Public IP>: PSK "<Pre-Shared Key>"
Start the IPsec service:

bash
Copy
sudo systemctl start ipsec.service
Check the status of the IPsec service:

bash
Copy
sudo systemctl status ipsec.service
7. Test Connectivity
Ping the private IP of EC2-A from EC2-VPN:

bash
Copy
ping 10.0.0.x
Example output:

bash
Copy
PING 10.0.0.167 (10.0.0.167) 56(84) bytes of data.
64 bytes from 10.0.0.167: icmp_seq=1 ttl=127 time=187 ms
64 bytes from 10.0.0.167: icmp_seq=2 ttl=127 time=186 ms
