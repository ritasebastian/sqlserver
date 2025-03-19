### **How to Set Up a Domain Controller (DC) on AWS EC2**
Setting up a **Windows Active Directory Domain Controller** on AWS EC2 involves the following steps:

---

## **Step 1: Launch an EC2 Instance**
1. **Go to AWS Console → EC2 → Launch Instance**
2. Choose an **Amazon Machine Image (AMI):**  
   - Select **Windows Server 2019 or 2022 Base AMI** (which includes Active Directory features).
3. Choose an **Instance Type:**  
   - **Minimum:** t3.medium (for small environments).  
   - **Recommended:** t3.large or larger (for production).  
4. Configure **Network Settings:**  
   - VPC: Use an existing **private VPC** or create a new one.  
   - Subnet: Place the instance in a **private subnet** (for security).  
   - Enable **Auto-assign Public IP** (Optional, for RDP access).  
   - Set **Security Group** rules:  
     - Allow **RDP (3389)** from trusted IPs.  
     - Allow **DNS (UDP 53, TCP 53)** for internal clients.  
5. **Attach an IAM Role (Optional):**  
   - If you plan to integrate with AWS Managed AD, attach an IAM role with **AmazonSSMManagedInstanceCore**.

6. **Add Storage:**  
   - Root volume: **At least 30 GB (General Purpose SSD - gp3/gp2).**  
   - Additional volume: **100 GB for Active Directory database (NTDS, SYSVOL).**

7. **Launch Instance** and connect via **RDP**.

---

## **Step 2: Configure Windows Server for Domain Controller**
1. **Login to the EC2 Instance using RDP**.
2. **Rename the Server (Optional but Recommended)**  
   - Open **Server Manager → Local Server → Computer Name → Change**
   - Set a meaningful name (e.g., `DC-01`).
   - Restart the instance.

3. **Set a Static Private IP Address (Recommended)**
   - Open **Control Panel → Network and Sharing Center → Change Adapter Settings**.
   - Select **Ethernet**, right-click → **Properties**.
   - Set **IPv4 Address** to a static **private IP (e.g., 10.0.0.10)**.

---

## **Step 3: Install Active Directory Domain Services (AD DS)**
1. Open **Server Manager → Manage → Add Roles and Features**.
2. Click **Next** until you reach the "Server Roles" page.
3. Select **Active Directory Domain Services (AD DS)** and **DNS Server**.
4. Click **Next** and **Install**.
5. Wait for installation to complete, then **Restart** the server.

---

## **Step 4: Promote the Server to a Domain Controller**
1. Open **Server Manager → Notifications → Promote this server to a domain controller**.
2. Choose **Add a new forest**, then enter your domain name (e.g., `mycompany.local`).
3. Set **Directory Services Restore Mode (DSRM) password** (keep this secure).
4. Click **Next**, review settings, and **Install**.
5. **Reboot** after installation.

---

## **Step 5: Verify Active Directory Setup**
1. **Login using the Domain Administrator account** (e.g., `Administrator@mycompany.local`).
2. Open **Active Directory Users and Computers (ADUC)**.
3. Check the **Domain Name and Controller Status**.
4. Test **DNS Resolution:**
   - Open **CMD**, run:  
     ```
     nslookup mycompany.local
     ```
   - It should resolve to the Domain Controller's private IP.

---

## **Step 6: Join Other EC2 Instances to the Domain**
1. **Launch a new Windows or Linux EC2 Instance.**
2. Assign it to the **same VPC and Subnet** as the Domain Controller.
3. Set its **Primary DNS to the Domain Controller’s private IP**.
4. On the Windows machine:
   - Open **System Properties → Change Settings → Domain**.
   - Enter the domain name (`mycompany.local`).
   - Enter **Domain Admin credentials**.
   - Restart the machine.

---

## **Step 7: (Optional) Enable AWS Route 53 for DNS Resolution**
1. In **AWS Route 53**, create a private hosted zone.
2. Add an **A Record** pointing to the Domain Controller.
3. Update the **VPC DHCP Option Set** to use the Domain Controller as the primary DNS.

---

## **Step 8: Implement Security & Backup**
- **Restrict RDP Access:** Allow only trusted IPs in Security Groups.
- **Enable AWS Backup:** Use AWS Backup to snapshot the AD database.
- **Monitor Logs:** Use AWS CloudWatch and Windows Event Viewer for logs.
- **Setup Redundancy (Optional):** Deploy a secondary domain controller in another **Availability Zone**.

---

### **Final Checks**
✔ **Verify Domain Controller is Running:**  
   ```
   Get-Service -Name NTDS
   ```
✔ **Ensure Users Can Join the Domain**  
✔ **Check Event Logs for Errors**  

---
### **That's it! Your Domain Controller is Now Ready in AWS!**
Let me know if you need help with specific configurations like **multi-AZ redundancy**, **hybrid AD integration**, or **AWS Directory Service**! 🚀
