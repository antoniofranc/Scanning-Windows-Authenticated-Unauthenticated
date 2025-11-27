<img width="419" height="410" alt="image" src="https://github.com/user-attachments/assets/9c82695e-d80c-4adc-a57f-fa84315c8ddb" />

# Linux Vulnerability Scanning: Authenticated vs Unauthenticated

## Project Overview
This project demonstrates the significant differences between authenticated and unauthenticated vulnerability scans on a Linux system using Tenable Nessus and Azure-hosted Ubuntu virtual machines. The exercise highlights why credential-based scanning is essential for comprehensive security assessments.

## Objectives
- Deploy an Ubuntu Linux virtual machine in Azure
- Configure network security rules to allow vulnerability scanning
- Perform an unauthenticated vulnerability scan using Tenable Nessus
- Configure SSH credentials and enable root access
- Perform an authenticated vulnerability scan
- Compare and analyze the differences in vulnerability detection
- Understand the importance of authenticated scanning in security assessments

## Technologies Used
- Cloud Platform: Microsoft Azure
- Operating System: Ubuntu Server 22.04 LTS
- Vulnerability Scanner: Tenable Nessus
- Protocols: SSH, ICMP
- Tools: PowerShell/Terminal, Azure Portal
----
## Lab Environment Setup
Step 1: Create Linux Virtual Machine in Azure
Navigate to Azure Portal → Virtual Machines → Create
Configuration Details:
- Resource Group: Your assigned resource group
- VM Name: [YourName]-Linux-Vulnerability-Scan
- Region: East US 2
- Image: Ubuntu Server 22.04 LTS
- Size: Standard DS1 v2
- Authentication Type: Password
- Username: lab-user
- Password: ****

Step 2: Configure Storage

Step 3: Configure Networking

Step 4: Deploy Virtual Machine

Step 5: Configure Network Security Group

Step 6: Verify Connectivity
Test ICMP (Ping): ping [VM-Public-IP]
Test SSH Connection: ssh lab-user@[VM-Public-IP]
- Password: ***
- Verify you're connected by running: hostname
----

## Part 1: Unauthenticated Scan  
Step 1: Create Basic Network Scan in Tenable
Navigate to Tenable Nessus → Scans → New Scan → Basic Network Scan
Scan Configuration:
- Name: [YourName]-Linux-Scan
- Scanner: Internal Scanner → Local Scan Engine
- Targets: [VM-Private-IP] (found in Azure VM overview)

Step 2: Configure Discovery Settings
Navigate to Discovery tab:
- ☑️ Custom Discovery
- ☑️ Ping the remote host
- ☑️ Use fast network discovery

Step 3: Launch Unauthenticated Scan
Click Save and Launch

<img width="442" height="163" alt="image" src="https://github.com/user-attachments/assets/78a3364b-d3c7-4174-b75f-91cfdd2098af" />
<img width="442" height="600" alt="image" src="https://github.com/user-attachments/assets/5763dbad-e9d3-4daf-9bab-4600c2485de0" />
<img width="442" height="132" alt="image" src="https://github.com/user-attachments/assets/119a43be-7626-49d3-a86c-b405c8e7983f" />

Step 4: Review Unauthenticated Results
Once complete, view scan details:
<img width="500" height="774" alt="image" src="https://github.com/user-attachments/assets/c8688156-09ee-44b8-8b82-f52c0ea6c8bb" />

Expected Results:
- Critical: 0
- High: 0
- Medium: 0
- Low: 1 (ICMP Timestamp Request Remote Date Disclosure)
- Info: 18

Step 5: Export Unauthenticated Results
Click Export → Executive Summary

Save as: [YourName]-Linux-Scan-Unauthenticated

----
## Part 2: Authenticated Scan
Step 1: Configure Root Access on Linux VM

SSH into the VM:

ssh lab-user@[VM-Public-IP]

Reset root password: sudo passwd root

sudo passwd root

Enter new password: *** (twice)

Enable root SSH login:
sudo grep -q '^PermitRootLogin' /etc/ssh/sshd_config && sudo sed -i 's/^PermitRootLogin.*/PermitRootLogin yes/' /etc/ssh/sshd_config || echo 'PermitRootLogin yes' | sudo tee -a /etc/ssh/sshd_config > /dev/null && sudo systemctl restart sshd

<img width="600" height="97" alt="image" src="https://github.com/user-attachments/assets/b0515483-4a6c-4b34-8164-1bf8af28f663" />
<img width="900" height="187" alt="image" src="https://github.com/user-attachments/assets/0cb1fbaa-a2b1-42a1-bcb5-021b5a616f52" />

Step 2: Verify Root SSH Access

Exit the current SSH session: `exit`

Test root login:

ssh root@[VM-Public-IP]

Password: *****

Verify with:

`whoami`
`id`
`hostname`

<img width="481" height="43" alt="image" src="https://github.com/user-attachments/assets/10a2306b-a343-40ca-8480-4d879152992c" />
<img width="573" height="107" alt="image" src="https://github.com/user-attachments/assets/c31990f2-80d4-4597-b089-442f908df5fa" />


Step 3: Add Credentials to Tenable Scan
Navigate to: Tenable → Scans → Edit your Linux scan → Credentials
Add SSH Credentials:

- Authentication Method: Password
- Username: root
- Password: Cyberlab123!
- Elevate Privileges: Su
- Su Login: root
- Su Password: *****

Step 4: Launch Authenticated Scan
Click Save and Launch

<img width="600" height="859" alt="image" src="https://github.com/user-attachments/assets/82c35d3e-31ab-4995-a57d-066205a07fbf" />
<img width="600" height="513" alt="image" src="https://github.com/user-attachments/assets/bd630a88-eab5-45b7-a1d2-54d41006125e" />

Step 5: Review Authenticated Results
Once complete, view scan details:


<img width="600" height="773" alt="image" src="https://github.com/user-attachments/assets/f6c86126-0c67-4332-811a-3665165071ad" />

Expected Results:
- Critical: 0
- High: 0
- Medium: 2
- Low: 2
- Info: 56

Step 6: Export Authenticated Results

Click Export → Executive Summary

Save as: [YourName]-Linux-Scan-Authenticated

-----
## Results Comparison

Vulnerability Count Comparison

Scan Type	                        Critical    	   High	      Medium	  Low	      Info         Total

Unauthenticated                     0               0           0        1         18           19

Authenticated                       0               0           2        2         56           60

Scan Duration

Both scans completed in approximately `6 minutes`, demonstrating that authenticated scanning does not necessarily increase scan time significantly for Linux systems.

## Key Findings

Unauthenticated Scan Limitations:
- Only surface-level vulnerabilities detected
- No visibility into installed packages
- No configuration auditing
- Limited to network-accessible services

Authenticated Scan Advantages:
- Complete package inventory analysis
- Configuration file auditing
- Missing security updates identified
- Privilege escalation vulnerabilities detected

## Key Takeaways
Why Authenticated Scanning Matters

1. Defense in Depth: Even if external vulnerabilities seem limited, attackers who gain initial access (as a low-privileged user) can exploit internal vulnerabilities to escalate privileges to root/admin level.

2. Proactive Security: Authenticated scans allow security teams to identify and remediate vulnerabilities before attackers can exploit them through privilege escalation chains.

3. Complete Visibility: Credential-based scanning provides a comprehensive view of the system's security posture, including:
- Outdated packages and missing patches
- Insecure configurations
- Local privilege escalation vectors
- Compliance violations
