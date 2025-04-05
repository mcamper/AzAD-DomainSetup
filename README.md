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
        <li>Create a Resource Group named "Active-Directory-Lab"</li>
        <img width="485" alt="image" src="https://github.com/user-attachments/assets/78f1eedd-6059-42a3-8ed1-f49022093ec8" />
        <li>Create a Virtual Network and Subnet named "Active-Directory-VNet"</li>
        <li>While creating the Virtual Network and Subnet, select the previously created Resource Group </li>
        <img width="488" alt="image" src="https://github.com/user-attachments/assets/8b395571-b06d-4b19-afcf-f931e0ac78f0" />
        <img width="357" alt="image" src="https://github.com/user-attachments/assets/685ba339-b9b4-4889-8d46-99cc260d668e" />
        <li>Create the Domain Controller VM (Windows Server 2022) named "DC-1"</li>
        <li>While creating the Domain Controller, select the previously created Resource Group</li>
        <img width="523" alt="image" src="https://github.com/user-attachments/assets/72834571-4104-4687-bbe4-049bf9c1d572" />
        <img width="498" alt="image" src="https://github.com/user-attachments/assets/5de49d39-9154-456f-a750-887840bb0161" />
        <img width="512" alt="image" src="https://github.com/user-attachments/assets/efbbc57b-35da-4ed9-9878-02084baf2faf" />
      </ul>
     <li>Create a Windows 10 Virtual Machine (VM)</li>
    <ul>
  
    <img width="494" alt="image" src="https://github.com/user-attachments/assets/8d69c74c-be70-4235-83b4-b02ce794eebb" />
  <li>For Image, choose Windows 10. For Size, choose at least 2vcpus or DS2sv</li>
  <img width="520" alt="Screenshot 2025-04-04 070032" src="https://github.com/user-attachments/assets/d6a1c2c9-5b2b-4e99-8263-52eadeb0df16" />
  <li>Enter a username and password (username: labuser  password: Cyberlab123!)</li>
  <img width="518" alt="Screenshot 2025-04-04 070314" src="https://github.com/user-attachments/assets/15eb8459-e5fc-4321-9f4a-6bcdc01eb775" />
  <li>Do not change any other settings</li>
  <li>While creating the VM, allow it to create a new Virtual Network (Vnet) and Subnet, by clicking "Review and Create"</li>
 <img width="478" alt="image" src="https://github.com/user-attachments/assets/82519c5e-bb82-4a8d-9c38-6afcdc3400eb" />
  <img width="320" alt="image" src="https://github.com/user-attachments/assets/10add3ab-c9f7-4c21-8961-48de72ea46da" />
  <li>Click "Create"</li>
</ul>
  <li>Create a Linux (Ubuntu) VM</li>
    <ul>
  <li>While creating the VM, select the previously created Resource Group and Vnet</li>
</ul>
      <li>Create a Linux (Ubuntu) VM</li>
    <ul>
  <li>While creating the VM (Linux-VM), select the previously created Resource Group (RS-NSG)</li>
      <img width="506" alt="image" src="https://github.com/user-attachments/assets/22d016db-ce67-45a9-bdde-508c11acccf5" />
  <li>For Image, choose Ubuntu Server 22.04 LTS - x64 Gen2 or 24. For Size, choose at least 2vcpus or DS2sv</li>
  <img width="497" alt="image" src="https://github.com/user-attachments/assets/ab72af90-922f-4420-8e1a-1cb79660230a" />
  <li>For Authentication type, choose Password.  Enter a username and password (username: labuser  password: Cyberlab123!) </li>
  <img width="542" alt="image" src="https://github.com/user-attachments/assets/691404a2-7720-461e-92c6-1dc854d93fc2" />
  <img width="518" alt="image" src="https://github.com/user-attachments/assets/ccb6fbd2-58e6-4810-9004-9cbb9bfab934" />
  <li>Do not change any other settings</li>
  <li>Click Next: Disks.  Click: Next: Networking</li>
  <img width="298" alt="image" src="https://github.com/user-attachments/assets/f1262388-03c9-4aa7-8420-a93a15ff4b84" />
  <li>Inside Networking, confirm correct Virtual Network and Subnet that was created in Windows-VM</li>
  <img width="350" alt="image" src="https://github.com/user-attachments/assets/7dafc92d-f07e-4b94-a4aa-f085d4daf9eb" />
  <li>Click "Review and Create"</li>
  <img width="288" alt="image" src="https://github.com/user-attachments/assets/4cae5865-bb3a-43af-a9ba-a6680cbc2d56" />
  <li>Click "Create"</li>
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
