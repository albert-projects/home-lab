---


---

<h2 id="upgrading-ubuntu-18.04-to-22.04-on-microsoft-azure">Upgrading Ubuntu 18.04 to 22.04 on Microsoft Azure</h2>
<h2 id="introduction">Introduction</h2>
<p>I’ve got a requested to upgrade a Ubuntu 18.04 to 22.04, this document outlines the process of upgrading an Ubuntu 18.04 virtual machine (VM) to 22.04 on Microsoft Azure. Recorded the step and command on my Azure Lab Environment.</p>
<h2 id="prerequisites">Prerequisites</h2>
<ul>
<li>An active Microsoft Azure account with an existing Ubuntu 18.04 VM.</li>
</ul>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-1.png" alt="Screenshot-1"></p>
</blockquote>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-2.png" alt="Screenshot-2"></p>
</blockquote>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-3.png" alt="Screenshot-3"></p>
</blockquote>
<ul>
<li>Basic familiarity with the Linux command line.</li>
<li>Administrative access to the Ubuntu 18.04 VM.</li>
</ul>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-4.png" alt="Screenshot-4"></p>
</blockquote>
<h2 id="upgrade-procedure">Upgrade Procedure</h2>
<p>Back Up Your Data:</p>
<ul>
<li>Before initiating the upgrade, ensure you have a complete backup of<br>
your VM data. This includes configuration files, application data,<br>
and user data. Utilize Azure Backup or your preferred backup solution<br>
to secure your data.</li>
</ul>
<hr>
<p>This is an incredibly significant tasks and caution should be exercised. Here’s a brief overview of the necessary steps.</p>
<ul>
<li>Pre-Upgrade Tasks</li>
<li>Upgrade to 20.04</li>
<li>Verify the Upgrade to 20.04</li>
<li>Upgrade to 22.04</li>
<li>Verify the upgrade to 22.04</li>
<li>Post-Upgrade Task</li>
<li></li>
</ul>
<h2 id="pre-upgrade-tasks">Pre-Upgrade Tasks</h2>
<ul>
<li><em>Backup Everything:</em></li>
</ul>
<blockquote>
<p>Don’t skip the backup! Seriously, it’s super important. Losing important server files is a stress you don’t want to feel. So, spend some quality time on backups and figuring out how to bring your systems back to life.</p>
</blockquote>
<ul>
<li><em>Ensure System Health</em></li>
</ul>
<p><code>sudo apt update</code></p>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-5.png" alt="Screenshot-5"><br>
<code>sudo apt upgrade</code><br>
<img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-6.png" alt="Screenshot-6"><br>
<code>sudo apt dist-upgrade</code><br>
<img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-7.png" alt="Screenshot-7"><br>
<code>sudo apt autoremove</code><br>
ScreenRecording1.gif</p>
</blockquote>
<ul>
<li><em>Remove Unnecessary PPAs</em></li>
</ul>
<p>Inspect <code>/etc/apt/sources.list</code> and <code>/etc/apt/sources.list.d/</code> for any additional repositories or PPAs and comment them out.</p>
<ul>
<li><em>Document Installed Softwares (Copy to local machine)</em></li>
</ul>
<p><code>dpkg --get-selections | sudo tee installed-software.txt &gt; /dev/null</code></p>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-10.png" alt="Screenshot-10"></p>
</blockquote>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/ScreenRecording1.gif" alt="ScreenRecording1"></p>
</blockquote>
<h2 id="upgrade-to-v20.04">Upgrade to v20.04</h2>
<blockquote>
<p>Upgrading from one LTS version of Ubuntu to another LTS version typically requires stepping through the intermediate LTS versions, which means going from 18.04 to 20.04, then to 22.04.</p>
</blockquote>
<ul>
<li><em>Start the Upgrade:</em></li>
</ul>
<p><code>sudo apt install update-manager-core</code></p>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-11.png" alt="Screenshot-11"><br>
<code>sudo do-release-upgrade</code></p>
</blockquote>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-12.png" alt="Screenshot-12"></p>
</blockquote>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-13.png" alt="Screenshot-13"></p>
</blockquote>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-14.png" alt="Screenshot-14"></p>
</blockquote>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-15.png" alt="Screenshot-15"></p>
</blockquote>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/ScreenRecording2.gif" alt="ScreenRecording2"></p>
</blockquote>
<ul>
<li><em>Follow the instructions and prompts, review and approve proposed changes:</em></li>
<li><em>Reboot once complete</em></li>
</ul>
<h2 id="verify-the-upgrade-to-v20.04">Verify the upgrade to v20.04</h2>
<ul>
<li><em>Check the Ubuntu version to ensure you’re now on 20.04:</em></li>
</ul>
<p><code>lsb_release -a</code></p>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-16.png" alt="Screenshot-16"></p>
</blockquote>
<ul>
<li><em>Check System Health (repeat of earlier step, but it’s good to ensure all is well):</em></li>
</ul>
<p><code>sudo apt update</code><br>
<code>sudo apt upgrade</code><br>
<code>sudo apt dist-upgrade</code><br>
<code>sudo apt autoremove</code></p>
<h1 id="upgrade-to-v22.04">Upgrade to v22.04</h1>
<ul>
<li><em>Start the Upgrade:</em></li>
</ul>
<p><code>sudo do-release-upgrade</code></p>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-17.png" alt="Screenshot-17"></p>
</blockquote>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/ScreenRecording3.gif" alt="ScreenRecording3"></p>
</blockquote>
<ul>
<li><em>As before, follow the instructions and prompts, review and approve proposed changes:</em></li>
<li><em>Reboot once complete</em></li>
</ul>
<h2 id="verify-the-upgrade-to-v22.04">Verify the upgrade to v22.04</h2>
<ul>
<li><em>Check the Ubuntu version to ensure you’re now on 22.04:</em></li>
</ul>
<p><code>lsb_release -a</code></p>
<blockquote>
<p><img src="https://github.com/albert-projects/linux-administration/blob/33a0b84781fc922aa1fe52f2be55c5dbe7997db4/Upgrading-Ubuntu-18.04-to-22.04/Screenshot-18.png" alt="Screenshot-18"></p>
</blockquote>
<ul>
<li><em>Check System Health</em></li>
</ul>
<p><code>sudo apt update</code><br>
<code>sudo apt upgrade</code><br>
<code>sudo apt dist-upgrade</code><br>
<code>sudo apt autoremove</code></p>
<ul>
<li><strong><em>Re-enable or Re-add PPAs:</em></strong> <em>If you had PPAs or custom repositories, you can now re-add them and install any necessary software. Be sure they are compatible with 22.04 before adding.</em></li>
<li><strong><em>Test Critical Software and Services:</em></strong> <em>Ensure that all the applications and services critical to your operations are functioning as expected.</em></li>
</ul>
<h2 id="post-upgrade-tasks">Post-Upgrade Tasks</h2>
<ul>
<li><em>Clean up old kernels or software no longer in use:</em></li>
<li>Upgrade or find alternatives for software that aren’t working</li>
</ul>
<blockquote>
<p>There’s always a risk when performing a major system upgrade. Make sure your backups are comprehensive and easily restorable.</p>
</blockquote>
<p>Important upgrade note to keep in mind from  <a href="https://help.ubuntu.com/community/UpgradeNotes">Ubuntu</a></p>
<blockquote>
<p><em>General Upgrade Information</em></p>
<p><em>An upgrade is the process of going from an earlier version of Ubuntu to a newer version of Ubuntu with an installed system. An example of this would be going from Ubuntu 17.10 to Ubuntu 18.04 LTS. To avoid damaging your running system, upgrading should only be done from one release to the next release (e.g. Ubuntu 16.04 to Ubuntu 16.10) or from one LTS release to the next (e.g. Ubuntu 16.04 LTS to Ubuntu 18.04 LTS). If you wish to ‘skip’ a version, you can back up your data and do a fresh installation, or progressively upgrade to each successive version. For example, to upgrade from Ubuntu 16.10 to Ubuntu 17.10, first upgrade to 17.04, then upgrade 17.04 to 17.10.</em></p>
</blockquote>

