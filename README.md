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
      <li>The Domain Controller VM using Active Directory should be created using the image <b>Windows Server 2022 Datacenter: Azure Edition - x64 Gen2</b></li>
    <br>
    <img width="700" alt="DC Configuration" src="https://github.com/landonbmartin/configure-ad/assets/148168629/5629a0d5-cade-4cc1-8003-769f1f2becc4">
    </br>
    <li>After the VMs are created, we'll set the Domain Controller's IP Address as <i>static</i>. Having it dynamic will make it difficult for the VM to communicate with the client VM.</li>
    <li>Go to the Virtual Machines service within Azure and go to <b>Networking</b>. Then, go to the link listed next to <b>Network Interface</b>. Head to <b>IP Configuration</b> under <b>settings</b> and go to the ipconfig link to open up a window to toggle the IP configuration and allocation to <b>Static</b>.</li>
    <ul>
      <li>IP Configuration for the Domain VM</li>
    <br>
    <img width="2560" alt="NIC Private IP to Static" src="https://github.com/landonbmartin/configure-ad/assets/148168629/a5f694d8-c95f-444c-971a-49fe47664e42">
    </br>
  </ul>
</p>

<br />

<h3>Ensuring Connectivity between the Client and Domain Controller</h3>

<p>
  <ul>
    <li>Logging in to the Client VM, open the Command Prompt and enter the command <b>ping [Domain Controller Private IP Address] -t</b> to endlessly send a ping in order to ensure reachability with the Domain Controller. Connection should time out after the first ping due to the Domain Controller's Firewall Settings.</li>
    <li>Logging into the Domain Controller VM, go to the <b>Windows Defender Firewall with Advanced Security</b>. Head to the <b>Inbound Rules</b> and enable the rules under the protocol <b>ICMPv4</b>, specifically <i>Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In)</i></li>
    
  </ul>
</p>
