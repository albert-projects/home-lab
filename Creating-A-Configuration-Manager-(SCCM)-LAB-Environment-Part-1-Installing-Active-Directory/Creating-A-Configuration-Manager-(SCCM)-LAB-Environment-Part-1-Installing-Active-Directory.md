---


---

<h3 id="overview">Overview</h3>
<p>In these series of posts, we will go through the steps required to install Configuration Manager in a simple LAB environment. The LAB environment will be referenced in future posts as we explore Configuration Manager further. Since Hyper-V is nicely integrated in Windows 10 we will be using that to create the Virtual Machines, but it’s possible to use other software such as VMWare Workstation, VirtualBox, Parallels etc. All of the Configuration Manager Roles and SQL Server will be installed on the same server, this is also a possible scenario that could be used in production for smaller environments. Below is a quick overview of how the LAB will be setup.</p>
<p>Quick Jump:<br>
<a href="">Part 1 – Overview and Domain Controller installation</a><br>
<a href="">Part 2 – Management Server Installation</a><br>
<a href="">Part 3 – Installing SQL Server</a><br>
<a href="">Part 4 – Configuration Manager Prerequisites</a><br>
<a href="">Part 5 – Installing Configuration Manager</a></p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo1.png" alt=""></p>
<p>The lab will consist of the following virtual machines:</p>
<p>LABRTR01 – Router</p>
<ul>
<li>1x CPU</li>
<li>1GB RAM</li>
<li>Disk 10GB (OS)</li>
<li>Interface 1 (LAN):
<ul>
<li>IP: 192.168.3.1</li>
<li>Mask: 255.255.255.0</li>
<li>DNS: 192.168.3.20</li>
<li>Gateways: none</li>
</ul>
</li>
<li>Interface 2 (WAN):
<ul>
<li>DHCP</li>
</ul>
</li>
</ul>
<p><strong>Note:</strong> Installing the Router is optional. In my case I am running a basic pfSense router that can be freely obtained at  <a href="http://www.pfsense.org/download">www.pfsense.org/download</a>. Setup of this will be covered in a later post.</p>
<p>LABDC01 – Domain Controller</p>
<ul>
<li>2x CPU</li>
<li>4GB RAM</li>
<li>Disks: OS (127GB)</li>
<li>Interface 1 (LAN)
<ul>
<li>IP: 192.168.3.20</li>
<li>Mask: 255.255.255.0</li>
<li>DNS: 192.168.3.20</li>
<li>Gateway: 192.168.3.1</li>
</ul>
</li>
</ul>
<p>LABCM01 – Configuration Manager Server</p>
<ul>
<li>4x CPU</li>
<li>8GB RAM</li>
<li>Disks: OS (127GB), Data/SCCM (60GB), DP (50GB), Content (50GB)</li>
<li>Interface 1 (LAN)
<ul>
<li>IP: 192.168.3.35</li>
<li>Mask: 255.255.255.0</li>
<li>DNS: 192.168.3.20</li>
<li>Gateway: 192.168.3.1</li>
</ul>
</li>
</ul>
<p><strong>Note</strong>: A best practice is to install Configuration Manager and SQL on its own disk, especially in enterprise environments. In the guide we will simulate different disks by having a total of 4 virtual hard drives in the VM but in reality, they are all running from the same physical drive.</p>
<p>LABADM01 – Administration Server</p>
<ul>
<li>2x CPU</li>
<li>4GB RAM</li>
<li>Disks: OS (127GB)</li>
<li>Interface 1 (LAN)
<ul>
<li>IP: 192.168.3.30</li>
<li>Mask: 255.255.255.0</li>
<li>DNS: 192.168.3.20</li>
<li>Gateway: 192.168.3.1</li>
</ul>
</li>
</ul>
<p><strong>Note</strong>: Installing the Administration server is optional, however it lets us manage our entire environment from a single server.</p>
<h3 id="obtaining-software">Obtaining Software</h3>
<p>All the software used in this guide can be downloaded from  <a href="https://www.microsoft.com/en-us/evalcenter">Microsoft’s Evaluation Center</a>  and be used free of charge for up to 180 days, which is perfect for a LAB like this. If you have a Volume License, MSDN or Visual Studio Subscription the software can be obtained from the respective locations, including license keys.</p>
<p><strong>Windows Server</strong></p>
<p>We will be using  <a href="https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2019">Windows Server 2019</a>  in the guide, on a general basis the latest version is always recommended. If for any reason you prefer Windows Server 2016 it makes little difference in the terms of this guide. If you are downloading the software from Microsoft’s Evaluation Center, then make sure you either download the ISO or VHDX file, I prefer the ISO files as they can be reused on other virtualization platforms other than Hyper-V.</p>
<p><strong>Configuration Manager</strong></p>
<p>System Center Configuration Manager (SCCM / ConfigMgr) or Microsoft Endpoint Configuration Manager (MECM), as it is now called after Microsoft’s rebranding at Ignite 2019, comes in two variants. The first is <a href="https://www.microsoft.com/en-us/evalcenter/evaluate-system-center-configuration-manager-and-endpoint-protection">Current Branch</a> which is the version you would use in a production environment, and is available for use up to 180 days without a license (upgradable to the full version). The <a href="https://www.microsoft.com/en-us/evalcenter/evaluate-system-center-configuration-manager-and-endpoint-protection-technical-preview">Technical Preview Branch</a> is actually free to use and does not require a license, however it only supports a maximum of 10 clients, does not allow for import or export of data and must be upgraded within 90 days of each monthly release. The Preview Branch also contains new features that may or may not end up in the final product and is intended for LAB purposes only. Microsoft has more information on the differences here: <a href="https://docs.microsoft.com/en-us/configmgr/core/understand/which-branch-should-i-use">https://docs.microsoft.com/en-us/configmgr/core/understand/which-branch-should-i-use</a>.</p>
<p>In this guide we will be installing the Current Branch version, but the install procedure will be identical for the Technical Preview Branch. In addition we will need to download additional components such as the Windows 10 Assessment and Development Kit (ADK) but we will cover that later.</p>
<p><strong>SQL Server</strong></p>
<p>At the time of writing I would suggest using either SQL Server 2016 or SQL Server 2017. In order to support SQL Server 2019, we need to be running Configuration Manager Current Branch 1910 or newer and the latest setup files in the Evaluation Center are 1902. We will upgrade to the latest version of Configuration Manager post installation and can migrate our installation to SQL 2019 later.</p>
<p><a href="https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016">SQL Server 2016</a><br>
<a href="https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2017">SQL Server 2017</a><br>
<a href="https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2019">SQL Server 2019</a></p>
<h3 id="basic-server-configuration">Basic Server Configuration</h3>
<p>Before we start installing and configuring our first Domain Controller, we need to 4 simple tasks:</p>
<ul>
<li>Change the computer name</li>
<li>Assign a static IP address</li>
<li>Disable IE Enhanced Security Configuration</li>
<li>Install the latest Windows Updates</li>
</ul>
<p>Logon to LABDC01 which should be a freshly installed Windows Server 2019 installation, wait for Server Manager to appear. All of the prerequisites tasks can be completed straight from Server Manager. If you prefer to use PowerShell, the script below will make the required changes (except running Windows Update) and restart the server.</p>
<pre class=" language-powershell"><code class="prism  language-powershell"><span class="token function">Rename-Computer</span> <span class="token operator">-</span>NewName LABDC01
<span class="token function">Write-Host</span> <span class="token string">"Computer Name Changed"</span>
<span class="token variable">$AdminKey</span> = <span class="token string">"HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}"</span>
<span class="token variable">$UserKey</span> = <span class="token string">"HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}"</span>
<span class="token function">Set-ItemProperty</span> <span class="token operator">-</span>Path <span class="token variable">$AdminKey</span> <span class="token operator">-</span>Name <span class="token string">"IsInstalled"</span> <span class="token operator">-</span>Value 0
<span class="token function">Set-ItemProperty</span> <span class="token operator">-</span>Path <span class="token variable">$UserKey</span> <span class="token operator">-</span>Name <span class="token string">"IsInstalled"</span> <span class="token operator">-</span>Value 0
<span class="token function">Write-Host</span> <span class="token string">"Disabled IE Enhanced Security Configuration"</span>
New<span class="token operator">-</span>NetIPAddress <span class="token operator">-</span>InterfaceAlias <span class="token string">"Ethernet"</span> <span class="token operator">-</span>AddressFamily IPv4 <span class="token operator">-</span>IPAddress 192<span class="token punctuation">.</span>168<span class="token punctuation">.</span>3<span class="token punctuation">.</span>20 <span class="token operator">-</span>PrefixLength 24 <span class="token operator">-</span>DefaultGateway 192<span class="token punctuation">.</span>168<span class="token punctuation">.</span>3<span class="token punctuation">.</span>1
<span class="token function">Set</span><span class="token operator">-</span>DnsClientServerAddress <span class="token operator">-</span>InterfaceAlias <span class="token string">"Ethernet"</span> <span class="token operator">-</span>ServerAddresses 192<span class="token punctuation">.</span>168<span class="token punctuation">.</span>3<span class="token punctuation">.</span>20
<span class="token function">Write-Host</span> <span class="token string">"IP address and DNS set"</span>
<span class="token function">Set-ItemProperty</span> <span class="token operator">-</span>Path <span class="token string">"HKLM:\System\CurrentControlSet\Control\Terminal Server"</span> <span class="token operator">-</span>Name <span class="token string">"fDenyTSConnections"</span> –Value 0
Enable<span class="token operator">-</span>NetFirewallRule <span class="token operator">-</span>DisplayGroup <span class="token string">"Remote Desktop"</span>
<span class="token function">Write-Host</span> <span class="token string">"Enabled Remote Desktop"</span>
<span class="token function">Restart-Computer</span>
</code></pre>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo2.png" alt=""></p>
<p>Our first task is to change the computer name to LABDC01.</p>
<ol>
<li>Click on the current computer name and press the Change button and enter LABDC01 as the new name. Hit Apply and close the dialogs. If you are asked to restart, select No.</li>
</ol>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo3.png" alt=""></p>
<ol start="2">
<li>Click on the Ethernet setting and change the IP address and DNS settings.</li>
</ol>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo5.png" alt=""></p>
<ol start="3">
<li>Click on the IE Enhanced Security Configuration and select Off for both Users and Administrators. This step is optional, but I prefer to disable it.</li>
</ol>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo4.png" alt=""></p>
<ol start="4">
<li>I would always recommend updating the operating system with the latest security patches before starting any installation. However we do not have internet connectivity at this point so updates will be installed after completing the Domain Controller installation.</li>
</ol>
<p><strong>Note:</strong>  If you would like to update your VM with the latest updates at this point it might be required to change the DNS settings. If so, go back into the IP settings and change the DNS server to something like 1.1.1.1 or 8.8.8.8. Once the server is patched change the DNS setting back to 192.168.3.20.</p>
<p>Now that basic configuration has been completed, we can continue installing the Domain Controller. Make sure you restart the server before continuing.</p>
<h3 id="installing-the-first-domain-controller">Installing the first Domain Controller</h3>
<p>After the server has been restarted it’s time to install the Domain Controller itself. Since there are quite a lot of dialogues the time this takes can be significantly reduced by using the following two lines of PowerShell code. Make sure to change the SafeModeAdminstratorPassword, this is a recovery password that can be used in the event that there is a problem with the Domain Controller. The server will automatically restart once complete and you can logon using your LAB\Administrator account. The password for the LAB\Administrator account will be the same as the local Administrator account (the password was set during Windows installation).</p>
<pre class=" language-powershell"><code class="prism  language-powershell">Install<span class="token operator">-</span>WindowsFeature <span class="token operator">-</span>Name AD<span class="token operator">-</span>Domain<span class="token operator">-</span>Services <span class="token operator">-</span>IncludeManagementTools <span class="token operator">-</span>Verbose
Install<span class="token operator">-</span>ADDSForest <span class="token operator">-</span>DomainName lab<span class="token punctuation">.</span>local <span class="token operator">-</span>SafeModeAdministratorPassword <span class="token punctuation">(</span>Convertto<span class="token operator">-</span>SecureString <span class="token operator">-</span>AsPlainText <span class="token string">"Password1!"</span> <span class="token operator">-</span>Force<span class="token punctuation">)</span> <span class="token operator">-</span>Verbose <span class="token operator">-</span>Force
</code></pre>
<p>Should you want to install the Domain Controller manually here are the steps:</p>
<p>Open Server Manger and select Add Roles and Features.</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo6.png" alt=""></p>
<p>Select Next to continue the wizard.</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo7.png" alt=""></p>
<p>Select Role Based or feature based installation.</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo8.png" alt=""></p>
<p>Select the Server (you should have only one).</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo9.png" alt=""></p>
<p>Select Active Directory Domain Services</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo10.png" alt=""></p>
<p>Once Active Directory Domain Services is selected you need add the management tools, click Add Features and click Next</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo11.png" alt=""></p>
<p>Since all the required features were added in the previous step we can just click Next.</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo12.png" alt=""></p>
<p>Review the notes for Active Directory Domain Services and click Next.</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo13.png" alt=""></p>
<p>Press the install button to begin install the required tools. Automatically restart can be selected if desired, but a restart will not be required.</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo14.png" alt=""></p>
<p>Once the installation is complete, click “Promote this server to a domain controller”. If you accidentally closed the window, it can be opened again from Server Manager by clicking on the Flag at the top of the screen.</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo15.png" alt=""></p>
<p>Select Add a new Forest and enter lab.local or any other domain name that you wish to use. This cannot be changed after installation.</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo16.png" alt=""></p>
<p>Keep the defaults and specify a password that can be used to recover the server (this is not the administrator password).</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo17.png" alt=""></p>
<p>Leave the DNS options and select Next.</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo18.png" alt=""></p>
<p>Specify the NETBIOS name or keep the defaults (recommended)</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo19.png" alt=""></p>
<p>Specify a location to store the Active Directory Database. Unless you have a very good reason just keep the defaults.</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo20.png" alt=""></p>
<p>Review your settings and click Next</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo21.png" alt=""></p>
<p>Click Install to begin.</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo22.png" alt=""></p>
<p>After the install is completed the server should restart automatically and you should be able to logon with your LAB\Administrator account. Congratulations you have now installed Active Directory Domain Services (ADDS). Before we wrap things up we need to make one change on our Domain Controller so that we can resolve external domains (reach the internet) by adding one or more forwarder in DNS. This can be done with the following Powershell command:</p>
<pre class=" language-powershell"><code class="prism  language-powershell"><span class="token function">Set</span><span class="token operator">-</span>DnsServerForwarder <span class="token operator">-</span>IPAddress 1<span class="token punctuation">.</span>1<span class="token punctuation">.</span>1<span class="token punctuation">.</span>1<span class="token punctuation">,</span> 8<span class="token punctuation">.</span>8<span class="token punctuation">.</span>8<span class="token punctuation">.</span>8
</code></pre>
<p>Alternatively here is how to make the change manually: In Server Manager select DNS from the Tools menu (upper right) to open the DNS Manager. DNS Manager can also be opened from Administrative Tools in the Control Panel or Start Menu.</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo23.png" alt=""></p>
<p>Next, right click on the server name and select Properties. Select the Forwarders tab and add one or more external DNS servers. In this example Cloudflare and Google’s public DNS servers are used, but its possible to use others such as your ISP.</p>
<p><img src="https://github.com/albert-projects/homelab/blob/58cf4a1dc824ea2e9a49bf57549bf656d92bf983/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/CreatingaCo24.png" alt=""></p>
<p>Verify that DNS is now working by attempting to resolve an external domain such as <a href="http://google.com">google.com</a>, this can be done by issuing the command: nslookup <a href="http://google.com">google.com</a> or ping <a href="http://google.com">google.com</a> from a command prompt.</p>
<p>That’s it for this post,  <a href="">in the next part we will be installing the Management Server LABADM01</a>, joining it to the domain and installing our management tools so that we can use LABADM01 to administer the other servers in our domain.</p>

