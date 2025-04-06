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

<h2>High-Level Deployment, Configuration and Management Steps</h2>

- Step 1: Preparing Active Directory Infrastructure in Azure
- Step 2: Deploying Active Directory and Configuration
- Step 3: Setup Remote Desktop for Non-Administrative Users
- Step 4: Create Users with PowerShell
- Step 5: Enabling and Unlocking Accounts and Resetting Passwords
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
       <img width="804" alt="image" src="https://github.com/user-attachments/assets/111a0a47-6ca6-44ab-8af1-ee51de0a0f99" />
       <img width="428" alt="image" src="https://github.com/user-attachments/assets/49922f4c-c697-4bf0-9146-b8bc291d3b4e" />
       <img width="377" alt="image" src="https://github.com/user-attachments/assets/85023d64-2967-4b66-ab38-c1cd954b92a7" />
       <li>From the Azure Portal, restart Client-1</li>
       <img width="803" alt="image" src="https://github.com/user-attachments/assets/a3479868-dc2a-471f-9bf4-e21374a8c879" />
      </ul>
       <li>Login to Client-1</li>
     <ul>
      <li>Connect remotely. Paste Client-1's Public IP address. Use same username and password previously used. </li>
      <li>Attempt to ping DC-1's Private IP address</li>
      <img width="277" alt="image" src="https://github.com/user-attachments/assets/492f14ec-281a-49bc-9100-3240a6f88e92" />
      <li>In Powershell, run ipconfig /all. The output for the DNS settings should show DC-1's Private IP address</li>
      <img width="490" alt="image" src="https://github.com/user-attachments/assets/04e76980-5bd7-4014-a6b2-f760a7d8dffc" />
    </ul>
     
</ol>

<h2>Step 2: Deploying Active Directory and Configuration</h2>
<p>
  <ol>
     <li>Install Active Directory Domain Services</li>
       <ul>
          <li>Remote Desktop into DC-1 using the Public IP Address</li>
          <li>Install Active Directory Domain Services</li>
          <img width="713" alt="image" src="https://github.com/user-attachments/assets/71aee3fc-cdda-48d5-9641-e0d2ea5e4a03" /> 
          <img width="488" alt="image" src="https://github.com/user-attachments/assets/5a7b7ec0-6b7b-483d-80b2-9df7e00900b3" />
          <img width="494" alt="image" src="https://github.com/user-attachments/assets/554dd7db-689d-409d-90ed-6ac5f95c91f5" />
          <img width="493" alt="image" src="https://github.com/user-attachments/assets/8646a212-c180-474a-b3d8-1956e5f15cad" />
          <img width="497" alt="image" src="https://github.com/user-attachments/assets/c9841577-59b4-455f-8e98-75e8f2e1b94b" />
          <img width="262" alt="image" src="https://github.com/user-attachments/assets/753fc772-f939-4d17-9b7a-241e15f5acac" />
          <img width="494" alt="image" src="https://github.com/user-attachments/assets/420cd626-1840-4833-93d5-9bf26085d6f1" />
          <img width="491" alt="image" src="https://github.com/user-attachments/assets/80356d31-1e35-4a76-85ff-40fd699ee09a" />
          <img width="490" alt="image" src="https://github.com/user-attachments/assets/f47506d1-8468-4591-9d15-c93d1ba2c44c" />
          <img width="592" alt="image" src="https://github.com/user-attachments/assets/cd2ecd22-b807-4bb4-bdd7-662b260239fc" />
          <img width="494" alt="image" src="https://github.com/user-attachments/assets/d6a6b4ac-b622-4ffb-b8e1-7904477da4e9" />
      </ul>
       <li>Promote as a DC: Setup a new forest as mydomain.com (can be anything)</li>
     <ul>
       <img width="956" alt="image" src="https://github.com/user-attachments/assets/8c0870dd-5ac7-434e-8f8a-6eec6ad1de36" />
       <img width="216" alt="image" src="https://github.com/user-attachments/assets/d798ca04-adb3-405f-b521-cd5d77ff1379" />
       <img width="478" alt="image" src="https://github.com/user-attachments/assets/5b4acf26-f363-433b-8440-955993c32df9" />
       <img width="474" alt="image" src="https://github.com/user-attachments/assets/22a196fa-629c-4da6-9e4f-8e1c1c5ef00f" />
       <img width="478" alt="image" src="https://github.com/user-attachments/assets/fab1ae36-628e-4160-926b-9ae0e0d72eb7" />
       <img width="478" alt="image" src="https://github.com/user-attachments/assets/b5937559-6407-4520-805c-cd6d85b333e4" />
       <img width="481" alt="image" src="https://github.com/user-attachments/assets/1a9ea0fd-b04e-4734-a44b-312b8fdca18d" />
       <img width="476" alt="image" src="https://github.com/user-attachments/assets/0d82e5b1-98bd-498a-9f91-ee210e9951bf" />
       <img width="481" alt="image" src="https://github.com/user-attachments/assets/f2ce3f84-eaf6-412e-b037-c28ed08714ce" />
       <img width="475" alt="image" src="https://github.com/user-attachments/assets/39aca6ef-b7f7-4f17-b6a2-4f3bf9a7fb87" />
      <li>Computer will restart. After restarting, log back into DC-1 as user: mydomain.com\labuser password: Cyberlab123!</li>
       <img width="259" alt="image" src="https://github.com/user-attachments/assets/c161bae1-e0c8-4fbc-8c79-ac235b4099d6" />
       <img width="254" alt="image" src="https://github.com/user-attachments/assets/05a93807-49fe-4902-a9cd-87a15e93ca75" />
       <img width="281" alt="image" src="https://github.com/user-attachments/assets/da9a879e-685d-4bdc-a11b-d2c50ade3d2e" />
    </ul>
     <li>Create Directories, then Domain Admin user within the domain. Notice "labuser" is listed inside "Users" directory</li>
       <ul>
         <img width="258" alt="image" src="https://github.com/user-attachments/assets/2649af3f-eea7-4685-8c46-bf5abe9a3373" />
         <img width="469" alt="image" src="https://github.com/user-attachments/assets/593aca2f-caf1-4506-bca5-aea6bc2a1d20" />
         <img width="457" alt="image" src="https://github.com/user-attachments/assets/dfacba25-56ed-47e1-b90b-0a226fdcbb1d" />
         <img width="274" alt="image" src="https://github.com/user-attachments/assets/00ea312e-d5a5-4187-bbd5-5e2f77e3b44c" />
         <img width="154" alt="image" src="https://github.com/user-attachments/assets/b74d3558-e34c-40aa-873c-25764d5d1d91" />
         <img width="274" alt="image" src="https://github.com/user-attachments/assets/e90a9b1b-7ad7-4ece-a6e3-22f6f8dc6664" />
         <img width="379" alt="image" src="https://github.com/user-attachments/assets/8628060a-4423-42ed-ad37-1e01db2ab638" />
         <img width="275" alt="image" src="https://github.com/user-attachments/assets/e5c3b203-6810-4e97-8b1b-62854968d105" />
         <img width="278" alt="image" src="https://github.com/user-attachments/assets/c644fbc4-f295-482c-8443-c9d6a676cd41" />
         <img width="272" alt="image" src="https://github.com/user-attachments/assets/13c0ae35-c149-45b0-a9a3-a2cbac6b4804" />
         <img width="471" alt="image" src="https://github.com/user-attachments/assets/a1185341-d434-4d4c-8656-857620d7be7a" />
       </ul>
        <u><li>Add jane_admin to the "Domain Admins" Security Group</li>
         <img width="383" alt="image" src="https://github.com/user-attachments/assets/b7a9d721-eccd-41e3-862f-1e0b13e2ebb5" />
         <img width="256" alt="image" src="https://github.com/user-attachments/assets/1de7c116-0366-4cc0-92f4-ad44beef2dc6" />
         <img width="254" alt="image" src="https://github.com/user-attachments/assets/f856c397-8748-4f57-8978-4f7bb3f419ae" />
         <img width="287" alt="image" src="https://github.com/user-attachments/assets/d1c6f268-2ccd-463a-a126-23968869d044" />
         <img width="287" alt="image" src="https://github.com/user-attachments/assets/fc01ea65-0ac8-4717-829d-a3101a39e7f8" />
         <img width="257" alt="image" src="https://github.com/user-attachments/assets/c63aab57-5c53-486e-ab49-8f207359e0ac" />
         <img width="254" alt="image" src="https://github.com/user-attachments/assets/3e5c19bc-21ec-418c-9711-ff6bb248e14c" />
        <u><li>Log out/close the connection to DC-1 and log back in as "mydomain.com\jane_admin". Use as you admin account from now on</li></u>
         <img width="259" alt="image" src="https://github.com/user-attachments/assets/24556d60-fd95-456e-8e84-835990ea6eb9" />
         <img width="254" alt="image" src="https://github.com/user-attachments/assets/ac75dea5-de5a-4879-81a2-babd863282b0" />
          <ul><li>Password: Cyberlab123!</li></ul>
         <img width="287" alt="image" src="https://github.com/user-attachments/assets/0a4bf9b4-5060-44c7-9308-dc1ab72f041a" />
          <li>Join Client-1 to your domain (mydomain.com)</li>
            <ul><li>Log into Client-1 as the original local admin (labuser), password: Cyberlab 123!</li>
            <li>Join Client-1 to the domain. Computer will restart</li>
            </ul>
         <img width="479" alt="image" src="https://github.com/user-attachments/assets/8569a29f-1b57-4f7e-957a-ce812c3e4ccb" />
         <img width="470" alt="image" src="https://github.com/user-attachments/assets/e590a037-0c11-433d-af13-114fa3d6cf45" />
         <img width="271" alt="image" src="https://github.com/user-attachments/assets/7b17afc1-83de-4bf1-9a97-7950be2a5261" />
         <img width="215" alt="image" src="https://github.com/user-attachments/assets/6605d24f-3a95-47e3-8594-9870a11db0a7" />
         <img width="215" alt="image" src="https://github.com/user-attachments/assets/f0c6490e-c70c-4849-85ec-25b8ceaf93bf" />
         <img width="284" alt="image" src="https://github.com/user-attachments/assets/682a2870-7dec-425d-810d-100038203bf2" />
         <img width="180" alt="image" src="https://github.com/user-attachments/assets/22357124-952e-4b0e-ab27-37001efdb55d" />
         <img width="225" alt="image" src="https://github.com/user-attachments/assets/ff51f1d7-838b-4cc5-8578-354e775c9e07" />
         <img width="227" alt="image" src="https://github.com/user-attachments/assets/d18ef630-bd42-48ac-986d-3161b7f28f90" />
          <ul><li>Log into the Domain Controller and verify Client-1 shows up in ADUC</li>
            <li>Create a new OU named "_CLIENTS" and drag Client-1 into there</li>
          </ul>
         <img width="464" alt="image" src="https://github.com/user-attachments/assets/b26adcbb-cec1-4142-86c3-8ec6b34e0a75" />
         <img width="275" alt="image" src="https://github.com/user-attachments/assets/a87863a0-4f93-4964-94fb-6e7010e13a92" />
         <img width="470" alt="image" src="https://github.com/user-attachments/assets/d811ca9e-6969-4fae-a62c-3d0c0d499bd5" />
      </ul>
  </ol>
<p>
<h2>Step 3: Setup Remote Desktop for Non-Administrative Users</h2>
<p>
  <ol>
     <li>Setup Remote Desktop for non-administrative users on Client-1</li>
       <ul><li>Log into Client-1 as "mydomain.com\jane_admin". Password: Cyberlab123!</li></ul>
        <img width="259" alt="image" src="https://github.com/user-attachments/assets/68169e77-ee1b-402d-8835-e76cf7044b94" />
        <img width="251" alt="image" src="https://github.com/user-attachments/assets/553a1385-c005-423d-a284-31dd648a1a54" />
        <img width="281" alt="image" src="https://github.com/user-attachments/assets/52f7b010-2818-41a0-88ca-1738c043cd9e" />
        <img width="261" alt="image" src="https://github.com/user-attachments/assets/d7722462-d3f7-4f02-b748-326a1e5b8d3b" />
    <li>Open system properties. Click "Remote Desktop"</li>
       <ul><li>Right click Menu -> Systems. Click "Remote Desktop"</li></ul>
         <img width="536" alt="image" src="https://github.com/user-attachments/assets/d898d82c-1159-4d5b-b9fe-14eea54d75c6" />
    <li>Allow "domain users" access to remote desktop</li>
      <ul><li>Under "User Accounts", click "Select users that can remotely access this PC"</li></ul>
        <img width="541" alt="image" src="https://github.com/user-attachments/assets/ea1d93f3-8458-40f9-94f6-b5af7e8ee199" />
        <img width="255" alt="image" src="https://github.com/user-attachments/assets/8d40cb3c-8ffb-41ed-b7ca-8f20e0202a6a" />
        <img width="308" alt="image" src="https://github.com/user-attachments/assets/6f76032e-43bd-49dd-8f37-bc615fe24de0" />
        <img width="302" alt="image" src="https://github.com/user-attachments/assets/e5f84d88-5fe0-4bb7-a36a-5b78a3959d96" />
        <img width="250" alt="image" src="https://github.com/user-attachments/assets/c96aa2d4-df68-42ae-baff-34df7a60778a" />
      <ul><li>All domain users by default should be able to log into this computer</li></ul>
  </ol>
<p>
<h2>Step 4: Creating Users with PowerShell</h2>
<p>
  <ol>
    <li>Log into DC-1 as jane_admin</li>
          <img width="305" alt="image" src="https://github.com/user-attachments/assets/1ebe73b4-04de-4187-8dc3-408da533e823" />
          <img width="308" alt="image" src="https://github.com/user-attachments/assets/89681dc6-83f0-4176-830c-a80d0ed9aa6f" />
          <img width="283" alt="image" src="https://github.com/user-attachments/assets/4aad79a4-2951-42f7-b37a-515b21060e0d" />
          <img width="261" alt="image" src="https://github.com/user-attachments/assets/d23484aa-1e09-4374-94cf-f926e77f5e76" />
    <li>Open PowerShell ISE as an "Administrator"</li>
            <img width="322" alt="image" src="https://github.com/user-attachments/assets/64b8252e-f2dc-4e57-a294-18b0937ccfb1" />
            <img width="290" alt="image" src="https://github.com/user-attachments/assets/5a4a37ef-24a3-4d70-9224-92b0d32fee09" />
    <li>Create a new file and paste the contents of the script (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) into it</li>
            <ul><li>Copy raw file inside file. Save on desktop as "create-users". Note password: Password1</li></ul>
            <img width="248" alt="image" src="https://github.com/user-attachments/assets/c355349d-8c0b-44f1-aedc-3a576eb6f841" />
            <img width="615" alt="image" src="https://github.com/user-attachments/assets/ee0db314-2836-4e98-8e7a-30a7614094bd" />
            <img width="481" alt="image" src="https://github.com/user-attachments/assets/11c37955-6c4b-4087-b38e-e037697ed6fd" />         
            <ul><li>Run the script and observe the accounts being created</li></ul>
            <img width="593" alt="image" src="https://github.com/user-attachments/assets/229c3bda-4695-441d-8252-580dbed493d0" />
            <img width="257" alt="image" src="https://github.com/user-attachments/assets/aa36fbd9-7d3b-464a-9b81-d8264328798e" />
    <li>When finished, open ADUC and observe the accounts in the appropriate OU (_EMPLOYEES)</li>
            <img width="380" alt="image" src="https://github.com/user-attachments/assets/d9540036-185a-4e57-b89f-01a9e4810432" />
            <img width="474" alt="image" src="https://github.com/user-attachments/assets/b9b95535-f561-43e1-aae3-3213dbcfe7f9" />
    <li>Attempt to log into Client-1 with one of the accounts (take note of the password in the script)</li>
            <ul><li>Close Client-1's connection. Then re-open with new user.</li></ul>
            <img width="255" alt="image" src="https://github.com/user-attachments/assets/c378283c-5401-42d7-a900-f45c34cf71c0" />
            <img width="251" alt="image" src="https://github.com/user-attachments/assets/a8022014-067e-400e-8f77-5468b380380b" />
            <img width="334" alt="image" src="https://github.com/user-attachments/assets/f13687d5-4755-4d1a-b24f-c7e2c4ae42f2" />
            <img width="295" alt="image" src="https://github.com/user-attachments/assets/a293f485-6b37-4e47-b0f7-d0ab6c271211" />




    <li>Observing Logs</li>
  </ol>
<p>
<h2>Step 5: Managing Accounts within Azure-hosted AD</h2>
<p>
  <ol>
     <li>Open the Group Policy Management Console (GPMC)</li>
    <li>Create or Edit a Group Policy Object (GPO)</li>
    <li>Navigate to the Account Lockout Policy Settings</li>
    <li>Configure Account Lockout Policy Settings</li>
    <li>Link the GPO to an Organizational Unit (OU)</li>
    <li>Update Group Policy</li>
    <li>Verify the Policy</li>
  </ol>
<p>
<p>
<h2>Step 5: Configuring Group Policy</h2>
<p>
  <ol>
     <li>Open the Group Policy Management Console (GPMC)</li>
    <li>Create or Edit a Group Policy Object (GPO)</li>
    <li>Navigate to the Account Lockout Policy Settings</li>
    <li>Configure Account Lockout Policy Settings</li>
    <li>Link the GPO to an Organizational Unit (OU)</li>
    <li>Update Group Policy</li>
    <li>Verify the Policy</li>
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
