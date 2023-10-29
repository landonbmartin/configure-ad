<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1 align="center">Configuring Active Directory in Microsoft Azure</h1>
This lab demonstrates how to install and configure Active Directory using Microsoft Azure. I will be using two VMs on Azure that are on the same virtual network. One VM will be installed with Active Directory and configured to be the <b>Domain Controller</b> and the other VM will be used as a <b>client</b>. Then, I will configure the Active Directory to allow the Client to join the domain as well as creating user accounts using a Powershell script.

<br />

<h2>Environments and Technologies Used</h2>
<ul>
  <li>Microsoft Azure (Virtual Machines/Compute)</li>
  <li>Remote Desktop</li>
  <li>Active Directory Domain Servies</li>
  <li>Powershell</li>
</ul>

<br />

<h2>Operating Systems Used</h2>
<ul>
  <li>Windows Server 2022</li>
  <li>Windows 10 Pro (22H2)</li>
</ul>

<br />

<h2>Installation Steps</h2>

<h3>Setting up Resources in Azure</h3>

<p>
  <ul>
    <li>The Client VM should be installed normally using the Windows 10 image (OS)</li>
    <ul>
      <li>Tutorial on how to install VMs and to access them using Remote Desktop can be found <b><a href ="https://github.com/landonbmartin/vm-network">here</a></b></li>
    </ul>
      <li>The Domain Controller VM using Active Directory should be created using <b>Windows Server 2022 Datacenter: Azure Edition - x64 Gen2</b></li>
    <br>
    <img width="800" alt="DC Configuration" src="https://github.com/landonbmartin/configure-ad/assets/148168629/5629a0d5-cade-4cc1-8003-769f1f2becc4">
    </br>
    <li>After the VMs are created, we'll set the Domain Controller's IP Address as <i>static</i>. Having it dynamic will make it difficult for the VM to communicate with the client VM.</li>
    <li>Go to the Virtual Machines service within Azure and go to <b>Networking</b>. Then, go to the link listed next to <b>Network Interface</b>. Head to <b>IP Configuration</b> under <b>settings</b> and go to the ipconfig link to open up a window to toggle the IP configuration and allocation to <b>Static</b>.</li>
    <ul>
      <li>IP Configuration for the Domain VM</li>
    <br>
    <img width="800" alt="NIC Private IP to Static" src="https://github.com/landonbmartin/configure-ad/assets/148168629/a5f694d8-c95f-444c-971a-49fe47664e42">
    </br>
  </ul>
</p>

<br />

<h3>Ensuring Connectivity between the Client and Domain Controller</h3>

<p>
  <ul>
    <li>Logging in to the Client VM, open the Command Prompt and enter the command <b>ping [Domain Controller Private IP Address] -t</b> to endlessly send a ping in order to ensure reachability with the Domain Controller. Connection should time out after the first ping due to the Domain Controller's Firewall Settings.</li>
    <br>
    <img width="800" alt="ping timed out" src="https://github.com/landonbmartin/configure-ad/assets/148168629/c5fca500-9914-49ca-8955-a829b9b49b12">
    </br>
    <li>Logging into the Domain Controller VM, go to the <b>Windows Defender Firewall with Advanced Security</b>. Head to the <b>Inbound Rules</b> and enable the rules under the protocol <b>ICMPv4</b>, specifically <i>Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In)</i></li>
    <br>
    <img width="800" alt="Enable Windows Defender Firewall" src="https://github.com/landonbmartin/configure-ad/assets/148168629/9f2a637a-5fba-40f7-9fb8-ca7018483c53">
    </br>
    <li>Head back to the Client VM, and you should start to see replies from the perpetual ping</li>
    <br>
    <img width="800" alt="ping resumes" src="https://github.com/landonbmartin/configure-ad/assets/148168629/e432ddda-2ec2-4a43-8ad3-2380f9b29cdc">
    </br>
  </ul>
</p>

<br />

<h3>Installing Active Directory on the Domain Controller</h3>

<p>
  <ul>
    <li>In the Domain Controller VM, go to the Server Manager Dashboard and click on <b>Add Roles and Features</b>. Go through the installation process and upon getting to <b>Server Roles</b>, check the box for <b>Active Directory Domain Services</b>.</li>
    <br>
    <img width="800" alt="Add Roles and Features" src="https://github.com/landonbmartin/configure-ad/assets/148168629/27161f1b-ffbd-4160-bf2b-a40226530017">
    </br>
    Once installed, promote the server into the domain controller. There will be a <b>warning notification</b> on the top right where the flag icon is. Click on the flag and hit <b>Promote this server to a domain controller</b>. Click on <b>Add a new forest</b> and specify a domain name. For this tutorial, we'll name the domain <b>mydomain.com</b>, specify the password, and continue with the installation.
      <ul>
      <li>NOTE: You will be automatically signed out, re-logged in through Remote Desktop, and installation will be fully complete.</li>
      </ul>
    <br>
    <img width="350" alt="Promote this server to a DC" src="https://github.com/landonbmartin/configure-ad/assets/148168629/6e129687-28c1-43b4-98e0-b978ec6b3747">
    </br>
    <br>
    <img width="700" alt="Root domain name" src="https://github.com/landonbmartin/configure-ad/assets/148168629/e7ac1237-6fe2-48b5-b759-8f73c51fddd9">
    </br>
  </ul>
</p>

<br />

<h3>Important Log In Step</h3>

<p>
  <ul>
    <li>When logging back in to the domain controller VM, it is important to log in with the <b>context of the domain</b>.</li>
    <li>Type out the domain path and then the name of the user. Example: <b>mydomain.com\labuser</b></li>
    <br>
    <img width="450" alt="remote desktop - mydomain com labuser" src="https://github.com/landonbmartin/configure-ad/assets/148168629/b545ed50-1284-46fb-bf97-9280cab529ac">
    </br>
  </ul>
</p>

<br />

<h2>Configuration Steps</h2>

<h3>Creating Organizational Units (OUs) and Users</h3>

<p>
  <ul>
    <li>OUs act like folders that hold information, privileges, and login access of users in the directory</li>
    <li>In the Server Manager Dashboard, go to the <b>Tools</b> tab to open the Active Directory Users and Computers Console, right click on the domain (mydomain.com) and make 2 OUs, <b>_ADMIN</b> and <b>_EMPLOYEES</b>.</li>
    <ul>
      <li>The OUs names are needed for a later step where we create multiple accounts</li>
    </ul>
    <li>In the _ADMIN OU, create the user <b>Jane Doe</b> with the user name <b>jane_admin</b> and a password</li>
    <br>
    <img width="700" alt="jane_doe admin" src="https://github.com/landonbmartin/configure-ad/assets/148168629/6a3ce845-b9ba-4a44-866a-0563c4e2404a">
    </br>
    <li>Grant Jane admin prvileges using the <b>Security Group</b>. Right click on the user and open the <b>Properties</b>. Click on Member Of, then Add, to apply the security group.</li>
    <br>
    <img width="450" alt="members of - jane doe admin" src="https://github.com/landonbmartin/configure-ad/assets/148168629/2a485d4e-07fb-48a0-8a35-9ffacb6c38da">
    </br>
    <li>The user Jane will now be used to log in from here on, using the login username: <b>jane_admin</b></li>
    <br>
    <img width="450" alt="jane_admin who am i" src="https://github.com/landonbmartin/configure-ad/assets/148168629/5955a16e-5c12-4f31-99f6-7438884b4377">
    </br>
  </ul>
</p>

<br />

<h3>Joining the Client to the Domain</h3>

<p>
  <ul>
    <li>First, configure the Domain Name System (DNS) server. Go to the Domain Controller VM in the Microsoft Azure Portal and go to <b>Networking</b>. Then, go to the link listed next to <b>Network Interface</b>. Head to <b>DNS Servers</b> under <b>settings</b>, and set the DNS Server to <b>Custom</b>. Then, enter the domain controller's private IP address and save the changes. Restart the client VM in order to ensure the DNS changes are saved and to flush DNS cache.</li>
    <br>
    <img width="700" alt="Change client 1 DNS" src="https://github.com/landonbmartin/configure-ad/assets/148168629/27b6ec7a-f3f5-44fa-8258-2fbe6d8dad1c">
    </br>
    <li>In the System menu of the Client VM, click on <b>Rename this PC (Advanced)</b> and Change.</li>
    <li>Enter the domain and necessary credentials in order to let the client join the domain (logging in as <b>jane_admin</b>). It is important to note that the login credentials have to be input within the context of the domain path (mydomain.com\jane_admin)</li>
    <br>
    <img width="800" alt="Connecting jane_admin to DC" src="https://github.com/landonbmartin/configure-ad/assets/148168629/783271d4-949e-4e80-8f3d-02bedbd826e7">
    </br>
    <li>The Client VM now will be part of the domain. A popup should appear welcoming you to the domain.
  </ul>
</p>

<br />

<h3>Setup Remote Desktop for non-administrative users on Client VM</h3>

<p>
  <ul>
    <li>Before users in the domain can use the client computer, Remote Desktop has to be enabled for non-administrative users</li>
    <li>While logged in as the administrator (jane_admin), open <b>System Properties</b>. Click on <b>Remote Desktop</b> and select users that can remotely access this PC</li>
    <li>Add <b>Domain Users</b> access to Remote Desktop. Non-administrative users can now log in to the Client</li>
    <br>
    <img width="400" alt="Add domain users" src="https://github.com/landonbmartin/configure-ad/assets/148168629/aef0ced4-a14c-41da-a250-4e0557433dbf">
    </br>
  </ul>
</p>

<br />

<h3>Creating Users and attempt to log into the Client VM with one of the users</h3>

<p>
  <ul>
    <li>In the Domain Controller VM logged in as (jane_admin), open <b>Powershell ISE</b> <i>as an administrator</i></li>
    <li>Using this <a href = "https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1">powershell script</a>, we will create thousands of randomly generated accounts all with the password, <i>Password1</i></li>
    <li>In Powershell ISE, create a new file and copy-and-paste the powershell script into the file and run the script</li>
    <br>
    <img width="800" alt="Create Users" src="https://github.com/landonbmartin/configure-ad/assets/148168629/d097869e-deb8-40ad-8228-6161134a987f">
    </br>
    <li>All of these generated users will be put into the <b>_EMPLOYEES</b> Organizational Unit in the Active Directory</li>
    <br>
    <img width="600" alt="Create Users _employees" src="https://github.com/landonbmartin/configure-ad/assets/148168629/83448007-8d68-41a8-b0ba-f1af124b356b">
    </br>
    <li>Go to the Active Directory Users and Computers console and select a random username and obtain the login information by going to <b>Properties</b> and in the <b>Account</b> tab
    <ul>
      <li>The username will appear as <b>[first name].[last name]</b>, in this image the user is selecting "XXXXX.XXXXX"
    </ul>
    <br>
    <img width="600" alt="User Bew Fed" src="https://github.com/landonbmartin/configure-ad/assets/148168629/cfe9a7f3-e0c3-4bd0-b448-d1ef23b3878b">
    </br>
    <li>Attempt to log in to the Client VM using the generated username you have selected. The username to log in will look like <b>mydomain.com\username</b> and the password will be <b>Password1</b>
  </ul>
</p>

<br />

<h3 align = "right">Next Tutorial - <a href="https://github.com/landonbmartin/DNS">Understanding & Building Intuition for DNS</a></h3>
