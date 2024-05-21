---


---

<p>In these series of posts, we will go through the steps required to install Configuration Manager in a simple LAB environment. The LAB environment will be referenced in future posts as we explore SCCM further. See  <a href="../Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/Installing-Active-Directory.md">Part 1</a>  for an overview of the LAB environment.</p>
<p>In this final post we will extend the Active Directory Schema and install Configuration Manger.</p>
<p>Quick Jump:<br>
<a href="../Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/Installing-Active-Directory.md">Part 1 – Overview and Domain Controller installation</a><br>
<a href="../Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-2-Installing-A-Management-Server/Installing-A-Management-Server.md">Part 2 – Management Server Installation</a><br>
<a href="../Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-3-Installing-SQL-Server/Installing-SQL-Server.md">Part 3 – Installing SQL Server</a><br>
<a href="../Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-4-Configuration-Manager-Prerequisites/Configuration-Manager-Prerequisites.md">Part 4 – Configuration Manager Prerequisites</a><br>
<a href="../Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/Installing-Configuration-Manager.md">Part 5 – Installing Configuration Manager</a></p>
<h3 id="obtain-software">Obtain Software</h3>
<p>In  <a href="https://github.com/albert-projects/home-lab/blob/f2fb331f8a77373e8e5ed5e4cbe77318c04fcf45/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-1-Installing-Active-Directory/Installing-Active-Directory.md">Part 1</a>  it was mentioned where software can be obtained. In case you have not acquired the Configuration Manager setup files, they are available from  <a href="https://www.microsoft.com/en-us/evalcenter/evaluate-system-center-configuration-manager-and-endpoint-protection">Microsoft Evaluation Center</a>.</p>
<p>Open the SC_Configmgr_SCEP_1902.exe self-extracting file and unzip the files to the Content (F:) drive.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr1.png" alt=""></p>
<h3 id="cmtrace">CMTrace</h3>
<p>CMTrace is a really nice utility for reading log files and will be your best friend when it comes to reading Configuration Manager logs. I strongly recommend to copy the utility to all your servers and clients. By copying the CMtrace.exe file into the Windows directory will allows us to type CMtrace in any command prompt or PowerShell window for easy access. The command below copies CMTrace from the installasjon media to the local Windows folder.</p>
<pre class=" language-powershell"><code class="prism  language-powershell"><span class="token function">Copy-Item</span> F:\SC_Configmgr_SCEP_1902\SMSSETUP\TOOLS\CMTrace<span class="token punctuation">.</span>exe C:\Windows\System32\
</code></pre>
<p>Tip: Open CMTrace and when asked to be the default application for .log files click Yes. When doing troubleshooting and reading logs later the log files will automatically open in CMTrace.</p>
<h3 id="extend-the-active-directory-schema">Extend the Active Directory Schema</h3>
<p>In order to populate Active Directory with a few attributes specific to Configuration Manager, we will need to extend the Active Directory Schema. This is an optional step but is required in order to publish site information to the System Management Container we created in the previous post.</p>
<p><strong>Note:</strong>  This operation must be done by a member of the Schema Admins Active Directory group. By default the domain Administrator (LAB\Administrator) has this permission.</p>
<p>Open a Command Prompt as Administrator and navigate to the F:\SC_Configmgr_SCEP_1902\SMSSETUP\BIN\X64 directory where F:\ is the drive where the Configuration Manger setup files were extracted. Then type extadsch.exe to start extending the Active Directory Schema.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr2.png" alt=""></p>
<h3 id="install-configuration-manager">Install Configuration Manager</h3>
<p>Open the SC_Configmgr_SCEP_1902 folder on the Content drive and run the file called splash.hta, this will start the Configuration Manager install wizard.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr3.png" alt=""></p>
<p>Select “Install a Configuration Manager primary site” and click “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr4.png" alt=""></p>
<p>If you have a product key enter it here, else select evaluation and click “Next”. It is possible to upgrade from an evaluation to the full version later.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr5.png" alt=""></p>
<p>Configuration Manager needs to download a couple of extra files (primarily language files), specify a folder to download these and click “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr6.png" alt=""></p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr7.png" alt=""></p>
<p>Select the language that should be used in the Configuration Manager console and any reports generated and click “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr8.png" alt=""></p>
<p>Select the language the Configuration Manger client will support and click “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr9.png" alt=""></p>
<p>Specify a site code, site name and installation path and click “Next”. Note that the site code and site name cannot be changed later. Use the ConfigMgr (D:) disk for installation.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr10.png" alt=""></p>
<p>Since we dont have an existing hierarchy the server will be installed as a stand-alone site.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr11.png" alt=""></p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr12.png" alt=""></p>
<p>On the database page just keep the defaults. If the SQL Server has been installed on another server or if you want to change the database name, make the required changes here and click “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr13.png" alt=""></p>
<p>For the SQL log files just keep the default and click “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr14.png" alt=""></p>
<p>Enter the server name where the SMS provider will be installed, keep the defaults and click “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr15.png" alt=""></p>
<p>We haven’t implemented any PKI (Public Key Infrastructure) in the LAB environment yet so we cant use HTTPS communication. We will change from HTTP to HTTPS in a later post.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr16.png" alt=""></p>
<p>Check the boxes to install a Management Point and Distribution Point and click “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr17.png" alt=""></p>
<p>In order to receive Configuration Manger updates we need a service connection point. Click “Yes” and click “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr18.png" alt=""></p>
<p>Review the settings and click “Next”</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr19.png" alt=""></p>
<p>At the prerequisite check page there might be a couple of warnings. In this example the first warning is telling us that there could be a problem with the permissions for the System Management Container, but the warning can be ignored if we verified the permissions. The SQL warning appears because our SQL Server is configured with less than 8GB of memory. In this case both warnings can be dismissed and we can click the “Begin Install” button.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr20.png" alt=""></p>
<p>The Configuration Manager install can take sometime to complete. Allow as much as 30-90 minutes depending on hardware.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr21.png" alt=""></p>
<p>Click view to open the ConfigMgrSetup.log file the file will open automatically if CMTrace is set as the default application for opening log files.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr22.png" alt=""></p>
<p>Once the installation completes open the Configuration Manager Console from the start menu.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr23.png" alt=""></p>
<p>To check the Configuration Manager version click on the blue button in the top left corner then select “About Configuration Manager”.</p>
<p><img src="https://github.com/albert-projects/home-lab/blob/9443a0eb6953bc0de1ef3b02daa4b04953609a1a/Creating-A-Configuration-Manager-(SCCM)-LAB-Environment-Part-5-Installing-Configuration-Manager/ConfigMgr24.png" alt=""></p>
<p>Congratulations! We now have Configuration Manager installed. Even though the installation wizard is complete, Configuration Manager is still installing components under the hood. The completion of these components could take as much as an hour or more to complete. Dont be afraid of using the console during this time, but its something to be aware of. A good next step is to  <a href="">install the Configuration Manager Console on LABADM01</a>.</p>
<p>This concludes the installation series but in future posts we will be doing basic configuration, upgrading to the latest version, creating applications and packages, convert from evaluation to a full install, convert from HTTP to HTTPS communication and adding new roles to our existing installation to mention a few.</p>

