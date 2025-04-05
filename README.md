<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Cloud-Based Active Directory Domain Services Setup in Azure VMs</h1>
This tutorial outlines the implementation of Active Directory Domain Services (AD DS) within Azure Virtual Machines. It provides step-by-step instructions for setting up and configuring AD DS in a cloud environment, ensuring a scalable and secure directory service.<br />



<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Configuration, Deployment and Management Steps</h2>

- Step 1: Preparing Active Directory Infrastructure in Azure
- Step 2: Deploying Active Directory
- Step 3: Creating Users with PowerShell
- Step 4: Enabling and Unlocking Accounts and Resetting Passwords
- Step 5: Group Policy & Managing Accounts


<h2>Step 1: Preparing Active Directory Infrastructure in Azure</h2>
<p>
  <ol>
    <li>Setup Domain Controller in Azure</li>
      <ul>
        <li>Create a Resource Group</li>
        <img width="485" alt="image" src="https://github.com/user-attachments/assets/78f1eedd-6059-42a3-8ed1-f49022093ec8" />
        <li>Create a Virtual Network and Subnet</li>
        <img width="488" alt="image" src="https://github.com/user-attachments/assets/8b395571-b06d-4b19-afcf-f931e0ac78f0" />
        <img width="357" alt="image" src="https://github.com/user-attachments/assets/685ba339-b9b4-4889-8d46-99cc260d668e" />
        <li>Create the Domain Controller VM (Windows Server 2022) named "DC-1"</li>
      </ul>
  <li>Create a Linux (Ubuntu) VM</li>
    <ul>
  <li>While creating the VM, select the previously created Resource Group and Vnet</li>
</ul>
  <li>Observe your Virtual Network within Network Watcher</li>
</ol>

</p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create a Virtual Network.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create a Domain Controller VM (Windows Server 2022) named "DC-A".  Let's use Username: Testuser and Password: Testuser123!
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
After the VM is created, configure the NIC (Network Interface Card) with a private IP address.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Log into the VM and disable the Windows Firewall (for testing connectivity).
</p>
<h2>2. Setup Client-A in Azure</h2>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create another VM using Windows 10 and name it Client-A</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Set the username as testuser and the password as Testuser123!.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Ensure both VMs are in the same region and virtual network.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
After creating Client-A, set its DNS settings to point to DC-A's private IP address.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Test the connection by using PowerShell to ping Client-A from DC-A.
</p>
<h2>3. Deploying Active Directory</h2>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Log into DC-A and install Active Directory Domain Services (AD DS)</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Promote DC-A to a Domain Controller, creating a new domain (e.g., medomain.com).
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Restart and then log back into DC-A as user.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Untitled.
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Untitled.
</p>
<br /># AzAD-DomainSetup
