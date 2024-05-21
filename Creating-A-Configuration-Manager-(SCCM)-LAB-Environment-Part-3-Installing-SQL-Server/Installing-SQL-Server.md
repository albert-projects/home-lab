---


---

<p>In these series of posts, we will go through the steps required to install Configuration Manager in a simple LAB environment. The LAB environment will be referenced in future posts as we explore Configuration Manager further. See  <a href="../Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/Installing-Active-Directory.md">Part 1</a>  for an overview of the LAB environment.</p>
<p>Quick Jump:<br>
<a href="../Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/Installing-Active-Directory.md">Part 1 – Overview and Domain Controller installation</a><br>
<a href="../Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-2-Installing-A-Management-Server/Installing-A-Management-Server.md">Part 2 – Management Server Installation</a><br>
<a href="../Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/Installing-SQL-Server.md">Part 3 – Installing SQL Server</a><br>
<a href="../Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-4-Configuration-Manager-Prerequisites/Configuration-Manager-Prerequisites.md">Part 4 – Configuration Manager Prerequisites</a><br>
<a href="../Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/Installing-Configuration-Manager.md">Part 5 – Installing Configuration Manager</a></p>
<h3 id="basic-server-configuration">Basic Server Configuration</h3>
<p>In this post we will install SQL Server which will later be used to host the Configuration Manager database. Logon to LABCM01 and lets kick of with some basic configuration.</p>
<pre class=" language-powershell"><code class="prism  language-powershell"><span class="token function">Rename-Computer</span> <span class="token operator">-</span>NewName LABCM01
<span class="token function">Write-Host</span> <span class="token string">"Computer Name Changed"</span>
<span class="token variable">$AdminKey</span> = <span class="token string">"HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}"</span>
<span class="token variable">$UserKey</span> = <span class="token string">"HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}"</span>
<span class="token function">Set-ItemProperty</span> <span class="token operator">-</span>Path <span class="token variable">$AdminKey</span> <span class="token operator">-</span>Name <span class="token string">"IsInstalled"</span> <span class="token operator">-</span>Value 0
<span class="token function">Set-ItemProperty</span> <span class="token operator">-</span>Path <span class="token variable">$UserKey</span> <span class="token operator">-</span>Name <span class="token string">"IsInstalled"</span> <span class="token operator">-</span>Value 0
<span class="token function">Write-Host</span> <span class="token string">"Disabled IE Enhanced Security Configuration"</span>
New<span class="token operator">-</span>NetIPAddress <span class="token operator">-</span>InterfaceAlias <span class="token string">"Ethernet"</span> <span class="token operator">-</span>AddressFamily IPv4 <span class="token operator">-</span>IPAddress 192<span class="token punctuation">.</span>168<span class="token punctuation">.</span>3<span class="token punctuation">.</span>35 <span class="token operator">-</span>PrefixLength 24 <span class="token operator">-</span>DefaultGateway 192<span class="token punctuation">.</span>168<span class="token punctuation">.</span>3<span class="token punctuation">.</span>1
<span class="token function">Set</span><span class="token operator">-</span>DnsClientServerAddress <span class="token operator">-</span>InterfaceAlias <span class="token string">"Ethernet"</span> <span class="token operator">-</span>ServerAddresses 192<span class="token punctuation">.</span>168<span class="token punctuation">.</span>3<span class="token punctuation">.</span>20
<span class="token function">Write-Host</span> <span class="token string">"IP address and DNS set"</span>
<span class="token function">Set-ItemProperty</span> <span class="token operator">-</span>Path <span class="token string">"HKLM:\System\CurrentControlSet\Control\Terminal Server"</span> <span class="token operator">-</span>Name <span class="token string">"fDenyTSConnections"</span> –Value 0
Enable<span class="token operator">-</span>NetFirewallRule <span class="token operator">-</span>DisplayGroup <span class="token string">"Remote Desktop"</span>
<span class="token function">Write-Host</span> <span class="token string">"Enabled Remote Desktop"</span>
<span class="token function">Restart-Computer</span>
</code></pre>
<p>After the server has been restarted, then lets join it to the domain by executing the following PowerShell command.</p>
<pre class=" language-powershell"><code class="prism  language-powershell"><span class="token function">Add-Computer</span> <span class="token operator">-</span>DomainName <span class="token string">"lab.local"</span> <span class="token operator">-</span>Restart
</code></pre>
<p>Unlike the Domain Controller and Management Server that we setup previously, the Configuration Manager server has multiple disks that need to be setup. Use the quick launch menu (Windows + X, or right click on the start menu) to launch Disk Management.</p>
<ol>
<li>Right click Disk 1 and select “Online”</li>
<li>Right click Disk 1 again and select “Initialize Disk”</li>
<li>Select GPT as the partition layout and click “OK”.</li>
<li>Right click the Unallocated space and select “New Simple Volume”</li>
<li>On the Volume Size page, keep the defaults and click “Next”</li>
<li>On the Drive Letter page, just keep the defaults and click “Next”</li>
<li>On the Format Partition keep the Defaults, but change the label to ConfigMgr</li>
</ol>
<p>Repeat the process for Disk’s 1-3 but change the name/label so that Disk 1 = ConfigMgr, Disk 2 = DP and Disk 3 = Content. Drive letters can also be changed to keep things tidy, but really doesn’t matter.</p>
<p>The entire process can be automated by using diskpart. Copy the commands below into notepad and save it in a convenient location (for example c:\temp\disks.txt). Open a Command Prompt as an Administrator and navigate to the location of the text file.</p>
<p>Type diskpart /s c:\temp\disks.txt</p>
<p><strong>Note:</strong>  Make sure this script is only run in the LAB environment. The script will make changes to your drives and modification to work with your setup might be required, use at your own risk.</p>
<pre class=" language-bash"><code class="prism  language-bash">REM Change the CDROM drive letter to G:
<span class="token keyword">select</span> volume 0
assign letter<span class="token operator">=</span>G

REM Partition and <span class="token function">format</span> ConfigMgr Drive
<span class="token keyword">select</span> disk 1
attributes disk <span class="token function">clear</span> <span class="token function">readonly</span>
online disk
create partition primary
<span class="token function">format</span> fs<span class="token operator">=</span>ntfs label<span class="token operator">=</span><span class="token string">"ConfigMgr"</span> quick
assign letter<span class="token operator">=</span>D

REM Partition and <span class="token function">format</span> Distribution Point Drive
<span class="token keyword">select</span> disk 2
attributes disk <span class="token function">clear</span> <span class="token function">readonly</span>
online disk
create partition primary
<span class="token function">format</span> fs<span class="token operator">=</span>ntfs label<span class="token operator">=</span><span class="token string">"DP"</span> quick
assign letter<span class="token operator">=</span>E

REM Partition and <span class="token function">format</span> ConfigMgr Content Drive
<span class="token keyword">select</span> disk 3
attributes disk <span class="token function">clear</span> <span class="token function">readonly</span>
online disk
create partition primary
<span class="token function">format</span> fs<span class="token operator">=</span>ntfs label<span class="token operator">=</span><span class="token string">"Content"</span> quick
assign letter<span class="token operator">=</span>F
</code></pre>
<h3 id="sql-service-users">SQL Service Users</h3>
<p>With some basic server setup out of the way we can move on the the SQL tasks. Before we start with the SQL installation itself we need to create a couple of service users in Active Directory. Below is a script that will generate 3 Service Users with a 40 character random password. Two underlines are used as a prefix to identify service users, some might prefer svc or some other prefix. Modify the script to suit your needs. The password will be output in the PowerShell session but will also be written in the description field so it won’t be forgotten. This is of course a horrible practice for production environments where service accounts should be documented properly. Since this is a LAB everything is allowed 🙂</p>
<pre class=" language-powershell"><code class="prism  language-powershell"><span class="token variable">$userList</span> = <span class="token string">"__SQLDatabaseEngine"</span><span class="token punctuation">,</span> <span class="token string">"__SQLReporting"</span><span class="token punctuation">,</span> <span class="token string">"__SQLServerAgent"</span>

<span class="token keyword">function</span> New<span class="token operator">-</span>Password
<span class="token punctuation">{</span>
	<span class="token variable">$char</span> = <span class="token string">"abcdefghijkmnopqrstuvwxyzABCEFGHJKLMNPQRSTUVWXYZ0123456789"</span>
	<span class="token variable">$pwlength</span> = 40
	<span class="token variable">$password</span> = <span class="token string">""</span>
	<span class="token variable">$random</span> = <span class="token function">New-Object</span> System<span class="token punctuation">.</span>Random
				
	<span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token variable">$i</span> = 0<span class="token punctuation">;</span> <span class="token variable">$i</span> <span class="token operator">-le</span> <span class="token variable">$pwlength</span><span class="token punctuation">;</span> <span class="token variable">$i</span>+<span class="token operator">+</span><span class="token punctuation">)</span>
	<span class="token punctuation">{</span>
		<span class="token variable">$password</span> = <span class="token variable">$password</span> <span class="token operator">+</span> <span class="token variable">$char</span><span class="token punctuation">[</span><span class="token variable">$random</span><span class="token punctuation">.</span>Next<span class="token punctuation">(</span>0<span class="token punctuation">,</span> <span class="token variable">$char</span><span class="token punctuation">.</span>Length<span class="token punctuation">)</span><span class="token punctuation">]</span>
	<span class="token punctuation">}</span>
	<span class="token keyword">return</span> <span class="token variable">$password</span>	
<span class="token punctuation">}</span>

<span class="token keyword">foreach</span> <span class="token punctuation">(</span><span class="token variable">$user</span> in <span class="token variable">$userList</span><span class="token punctuation">)</span>
<span class="token punctuation">{</span>
    <span class="token keyword">if</span> <span class="token punctuation">(</span>Get<span class="token operator">-</span>ADUser <span class="token operator">-</span><span class="token keyword">Filter</span> <span class="token punctuation">{</span> sAMAccountName <span class="token operator">-eq</span> <span class="token variable">$user</span> <span class="token punctuation">}</span><span class="token punctuation">)</span>
	<span class="token punctuation">{</span>
		<span class="token function">Write-Host</span> <span class="token string">"<span class="token variable">$user</span> allready exists"</span>
	<span class="token punctuation">}</span>
	<span class="token keyword">else</span>
	<span class="token punctuation">{</span>
		<span class="token keyword">try</span>
		<span class="token punctuation">{</span>
			<span class="token variable">$pw</span> = New<span class="token operator">-</span>Password
			<span class="token variable">$pw_secure</span> = ConvertTo<span class="token operator">-</span>SecureString <span class="token operator">-</span>String <span class="token variable">$pw</span> <span class="token operator">-</span>AsPlainText <span class="token operator">-</span>Force
			New<span class="token operator">-</span>ADUser <span class="token operator">-</span>SamAccountName <span class="token variable">$user</span> <span class="token operator">-</span>Name <span class="token variable">$user</span> <span class="token operator">-</span>Description <span class="token string">"pw: <span class="token variable">$pw</span>"</span> <span class="token operator">-</span>AccountPassword <span class="token variable">$pw_secure</span> <span class="token operator">-</span>PassThru <span class="token punctuation">|</span> Enable<span class="token operator">-</span>ADAccount
			<span class="token function">Write-Host</span> <span class="token string">"Created User: <span class="token variable">$user</span> with password: <span class="token variable">$pw</span>"</span>
		<span class="token punctuation">}</span>
		<span class="token keyword">catch</span>
		<span class="token punctuation">{</span>
			<span class="token function">Write-Host</span> <span class="token string">"Failed to create User: <span class="token variable">$user</span> with password: <span class="token variable">$pw</span>."</span>
		<span class="token punctuation">}</span>	
	<span class="token punctuation">}</span>	
<span class="token punctuation">}</span>
</code></pre>
<p>After the script is run, 3 SQL Service accounts should be present in Active Directory. The service users should be moved into the Service Users OU that was created earlier.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLSvcAccounts.png" alt=""></p>
<h3 id="install-sql-server">Install SQL Server</h3>
<p>In  <a href="https://github.com/albert-projects/home-lab/blob/f2fb331f8a77373e8e5ed5e4cbe77318c04fcf45/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/Installing-Active-Directory.md">Part 1</a>  it was mentioned where software can be obtained, in case you have not acquired the SQL setup files, they are available from  <a href="https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016">Microsoft Evaluation Center</a>. SQL Server 2019 is supported starting in Configuration Manager 1910 and at the time of writing Configuration Manager 1902 is the only version available in the Evaluation Center, which is the reason we will be installing SQL Server 2016 and migrating to SQL Server 2019 in a later post.</p>
<p>After downloading the SQL Server 2016 Evaluation Setup form the Evaluation Center, run the SQLServer2016-SSEI-Eval.exe file and the following screen will appear. Select Custom.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_1.png" alt=""></p>
<p>Select a folder to download the SQL Setup files. I would recommend using the Content drive for this purpose. Select a folder and select Install to begin the download.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_2.png" alt=""></p>
<p>The SQL download is rather large so it could take a few minutes depending on your internet connection.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_3.png" alt=""></p>
<p>Once the SQL files have been downloaded and extracted the SQL Server Installation Center should appear. On the left hand side select “Installation” and select the first option “New SQL Server Stand-alone Installation”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_4.png" alt=""></p>
<p>Enter your license key if you have one, otherwise select Evaluation and press “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_5.png" alt=""></p>
<p>Accept the license terms and press “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_6.png" alt=""></p>
<p>The SQL installer will do some checks and you might see a warning on Windows Firewall, this can be safely ignored. Press “Next”.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_8.png" alt=""></p>
<p>Select the box to use Windows Updates and press “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_7.png" alt=""></p>
<p>Select the SQL features to install. For our purposes we only need the Database Engine and Reporting Services. Also make sure to change the SQL installation directory to the ConfigMgr drive (D:). Even though it is considered a best practice to install Configuration Manager and SQL on separate drives we won’t do this here as our virtual drives are on the same physical drive anyway.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_9.png" alt=""></p>
<p>Unless there is a specific reason keep the default instanceID and press “Next”.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_10.png" alt=""></p>
<p>On the Server Configuration Screen enter the Service Accounts and passwords that we created earlier and change the startup type to Automatic.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_11.png" alt=""></p>
<p>On the Collation tab select Customize and select SQL_Latin1_General_CP1_CI_AS and press “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_12.png" alt=""></p>
<p>In the Database Engine Configuration select “Add Current User” to add the LAB\Administrator user. Then select “Add” and add the computer account LABCM01 and press “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_13.png" alt=""></p>
<p>For the Reporting Services select “Install and Configure” and press “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_14.png" alt=""></p>
<p>Review the selected configuration and press “Install”.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_15.png" alt=""></p>
<p>Wait for the installation to complete.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_16.png" alt=""></p>
<p>Once completed verify that all the components where installed successfully</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_17.png" alt=""></p>
<p>SQL Server 2016 is now installed and this would be a good time to install any SQL updates from Windows Update. Close the SQL Server installation wizard and restart the server before and after installing updates.</p>
<h3 id="sql-management-studio">SQL Management Studio</h3>
<p>Next we need to install the SQL Management Studio so we can manage our SQL Server. This should also be done on our management server LABADM01 later so that we can manage our database from that server as well.</p>
<p>The SQL Server Management tools is a separate download and be directly, or open the download page by clicking the link from the SQL Server Installation Center. Save the downloaded SSMS-Setup-ENU.exe file on the Content drive (F:).</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_18.png" alt=""></p>
<p>Specify a location to install SQL Server Management Studio, in this case i am using the ConfigMgr drive (D:) for this purpose.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_19.png" alt=""></p>
<p>Wait for the installasjon to complete, this could take a few minutes.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_20.png" alt=""></p>
<p>Once complete the server needs to be restarted, click “Restart” to continue.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_21.png" alt=""></p>
<p>Once the server has been restarted, open SQL Server Management Studio from the start menu and click the “Connect” button.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_22.png" alt=""></p>
<p>Once connected right click LABCM01 and select properties, then on the Memory tab specify a minimum and maximum server memory depending on how much memory is available. As noted below Microsoft recommends allocating 50-80% when Configuration Manager is installed on the same server as the SQL Server, as it will be in our case. Microsoft states that 8GB of RAM is used for the SQL server, although it works with less. Read all the SQL recommendations  <a href="https://docs.microsoft.com/en-us/previous-versions/system-center/system-center-2012-R2/gg682077(v=technet.10)#BKMK_SupConfigSQLSrvReq">here</a>.</p>
<p><em>When you use a database server that is co-located with the site server, limit the memory for SQL Server to 50 to 80 percent of the available addressable system memory.</em>  <em>When you use a dedicated SQL Server, limit the memory for SQL Server to 80 to 90 percent of the available addressable system memory. Configuration Manager requires SQL Server to reserve a minimum of 8 gigabytes (GB) of memory in the buffer pool used by an instance of SQL Server for the central administration site and primary site and a minimum of 4 gigabytes (GB) for the secondary site. This memory is reserved by using the Minimum server memory setting under Server Memory Options and is configured by using SQL Server Management Studio.</em></p>
<p>In my case LABCM01 has a total of 8GB RAM so we will set minimum and maximum server memory to 4GB as we need the rest for SCCM later.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/1569c66c039b3a884eb9a2910a3e7f8d428d27b4/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/SQLInstall_23.png" alt=""></p>
<p>Once the server minimum and maximum server memory has been set, restart the SQL server by right clicking LABCM01 and selecting “Restart”. The SQL server is now ready to host the Configuration Manager database. In the next post we will be  <a href="../Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-4-Configuration-Manager-Prerequisites/Configuration-Manager-Prerequisites.md">installing and configuring all the Configuration Manager prerequisites</a>.</p>

