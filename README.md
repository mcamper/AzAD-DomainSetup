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
        <li>Create a Virtual Network and Subnet named "Active-Directory-VNet". Select the previously created Resource Group</li>
        <img width="488" alt="image" src="https://github.com/user-attachments/assets/8b395571-b06d-4b19-afcf-f931e0ac78f0" />
        <img width="357" alt="image" src="https://github.com/user-attachments/assets/685ba339-b9b4-4889-8d46-99cc260d668e" />
        <li>Create the Domain Controller VM (Windows Server 2022) named "DC-1". Select the previously created Resource Group</li>
        <img width="523" alt="image" src="https://github.com/user-attachments/assets/72834571-4104-4687-bbe4-049bf9c1d572" />
        <li>For Image, choose Windows Server 2022. For Size, choose at least 2vcpus or DS2sv</li>
        <img width="498" alt="image" src="https://github.com/user-attachments/assets/5de49d39-9154-456f-a750-887840bb0161" />
        <li>Enter a username and password (username: labuser  password: Cyberlab123!)</li>
        <img width="512" alt="image" src="https://github.com/user-attachments/assets/efbbc57b-35da-4ed9-9878-02084baf2faf" />
        <li>Do not change any other settings</li>
        <li>Click Next: Disks.  Click: Next: Networking</li>
        <li>Confirm correct VNet (Active-Directory-VNet). Leave the subnet default. Click "Review & Create". Click "Create".</li>
        <img width="479" alt="image" src="https://github.com/user-attachments/assets/708e0326-6365-451c-b74f-cb6f3f5c8899" />
      </ul>
   <li>After VM is created, set Domain Controller's NIC Private address to be static</li>
      <ul>
        <img width="619" alt="image" src="https://github.com/user-attachments/assets/7ccfb544-56f3-4056-aad5-09db723f38ad" />
        <img width="719" alt="image" src="https://github.com/user-attachments/assets/60d08e13-a6a2-4b19-b92d-f0edfacfef1c" />
        <img width="350" alt="image" src="https://github.com/user-attachments/assets/ec6494b0-67f9-420c-b6cc-e0bef580117c" />
      </ul>
    <li>Log into the VM and disable the Windows Firewall (for testing connectivity)</li>
      <ul>
        <li>Copy DC-1's Public IP address</li>
        <img width="795" alt="image" src="https://github.com/user-attachments/assets/bef04a23-bdca-40de-a938-71497afc6d39" />
        <li>Connect remotely. Paste DC-1's Public IP address </li>
        <img width="303" alt="image" src="https://github.com/user-attachments/assets/64a582cb-3928-4da2-9073-80d883162be9" />
        <li>Enter a username and password (username: labuser  password: Cyberlab123!)</li>
        <img width="281" alt="image" src="https://github.com/user-attachments/assets/8e1b8241-b1f7-4030-b3d4-169267e0972c" />
        <img width="260" alt="image" src="https://github.com/user-attachments/assets/fd998614-9aec-4b8a-ad16-50fbe2f5d6c5" />
        <li>Confirm you are inside the Domain Controller. The "Server Manager Dashboard" should appear</li>
        <img width="651" alt="image" src="https://github.com/user-attachments/assets/4563c3b7-0499-42c3-93dc-e98334991ebf" />
        <li>Right click the "Start" menu, then type: wf.msc for Windows Firewall (to disable it)</li>
        <img width="258" alt="image" src="https://github.com/user-attachments/assets/0aeff83b-f53d-46b3-a66d-3f5d5c7c104b" />
        <li>Under "Public Profile is Active", click "Windows Defender Firewall Properties" (to disable it)</li>
        <img width="465" alt="image" src="https://github.com/user-attachments/assets/32010b4e-9a68-4f79-8e95-7755090d13e9" />
        <li>Turn Firewall state: "Off" for Domain Profile, Private Profile, Public Profile, "Apply", "OK"</li>
        <img width="253" alt="image" src="https://github.com/user-attachments/assets/eda87b43-8661-44ba-82f6-48f94dbd4fcf" />
        <img width="246" alt="image" src="https://github.com/user-attachments/assets/928620ce-e270-492e-aaf3-0f097bbfd126" />
        <img width="248" alt="image" src="https://github.com/user-attachments/assets/cf441824-5d5a-4055-a378-a8670afdd727" />
        <img width="250" alt="image" src="https://github.com/user-attachments/assets/cfaea8dd-7143-46e8-a91a-9ed701c3ff9f" />
      </ul>
   <li>Setup Client-1 Azure</li>
     <ul>
      <li>Create the Client VM (Windows 10 Pro) named "Client-1"</li>
      <img width="491" alt="image" src="https://github.com/user-attachments/assets/9db8b6b3-1d4c-4d24-b1a7-c196fe3efc82" />
      <li>For Image, choose Windows 10 Pro, version 22H2 -x64 Gen2. For Size, choose at least 2vcpus or DS2sv</li>
      <img width="485" alt="image" src="https://github.com/user-attachments/assets/5e870fab-8220-45e3-a714-cf2b435707ad" />
      <li>For Authentication type, choose Password.  Enter a username and password (username: labuser  password: Cyberlab123!) </li>
      <img width="494" alt="image" src="https://github.com/user-attachments/assets/f08c3f30-6f12-40ba-887b-b875450be679" />
      <li>Do not change any other settings</li>
      <li>Click Next: Disks.  Click: Next: Networking</li>
       <img width="477" alt="image" src="https://github.com/user-attachments/assets/10bb0b7a-f00e-4550-bf2f-de7c94df3a55" />
      <img width="298" alt="image" src="https://github.com/user-attachments/assets/f1262388-03c9-4aa7-8420-a93a15ff4b84" />
      <li>Inside Networking, confirm correct Virtual Network created in DC-1</li>
      <img width="478" alt="image" src="https://github.com/user-attachments/assets/c4cf293b-89fa-43d7-b58c-a764259f523d" />
      <li>Click "Review and Create"</li>
      <img width="278" alt="image" src="https://github.com/user-attachments/assets/f1ffa751-14b1-4542-bf76-aad3fa13c37d" />
      <li>Click "Create"</li>
    </ul>
     <li>Set Client-1's DNS settings to DC-1's Private IP address</li>
     <ul>
      <li>Create the Client VM (Windows 10 Pro) named "Client-1"</li>
       <img width="804" alt="image" src="https://github.com/user-attachments/assets/111a0a47-6ca6-44ab-8af1-ee51de0a0f99" />
       <img width="428" alt="image" src="https://github.com/user-attachments/assets/49922f4c-c697-4bf0-9146-b8bc291d3b4e" />
       <img width="366" alt="image" src="https://github.com/user-attachments/assets/c4b7eb49-7126-46e3-96bd-0049c2205e24" />



      <img width="491" alt="image" src="https://github.com/user-attachments/assets/9db8b6b3-1d4c-4d24-b1a7-c196fe3efc82" />
      <li>For Image, choose Windows 10 Pro, version 22H2 -x64 Gen2. For Size, choose at least 2vcpus or DS2sv</li>
      <img width="485" alt="image" src="https://github.com/user-attachments/assets/5e870fab-8220-45e3-a714-cf2b435707ad" />
      <li>For Authentication type, choose Password.  Enter a username and password (username: labuser  password: Cyberlab123!) </li>
      <img width="494" alt="image" src="https://github.com/user-attachments/assets/f08c3f30-6f12-40ba-887b-b875450be679" />
      <li>Do not change any other settings</li>
      <li>Click Next: Disks.  Click: Next: Networking</li>
       <img width="477" alt="image" src="https://github.com/user-attachments/assets/10bb0b7a-f00e-4550-bf2f-de7c94df3a55" />
      <img width="298" alt="image" src="https://github.com/user-attachments/assets/f1262388-03c9-4aa7-8420-a93a15ff4b84" />
      <li>Inside Networking, confirm correct Virtual Network created in DC-1</li>
      <img width="478" alt="image" src="https://github.com/user-attachments/assets/c4cf293b-89fa-43d7-b58c-a764259f523d" />
      <li>Click "Review and Create"</li>
      <img width="278" alt="image" src="https://github.com/user-attachments/assets/f1ffa751-14b1-4542-bf76-aad3fa13c37d" />
      <li>Click "Create"</li>
    </ul>
     
</ol>

<h2>Step 2: Deploying Active Directory</h2>
<p>
  <ol>
     <li>Observe your Virtual Network within Network Watcher</li>
  </ol>
<p>
<h2>Step 3: Preparing Active Directory Infrastructure in Azure</h2>
<p>
  <ol>
     <li>Observe your Virtual Network within Network Watcher</li>
  </ol>
<p>
<h2>Step 4: Preparing Active Directory Infrastructure in Azure</h2>
<p>
  <ol>
     <li>Observe your Virtual Network within Network Watcher</li>
  </ol>
<p>
<h2>Step 5: Preparing Active Directory Infrastructure in Azure</h2>
<p>
  <ol>
     <li>Observe your Virtual Network within Network Watcher</li>
  </ol>
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
