<h1>Changing Passwords</h1>

        
<br>
<h2>Account Manager</h2><br>
 <strong>Important note:</strong> To prevent emails going into your spam folder you can white-list the domain <code>@feralhosting.com</code> to be safe before asking for a password reset. <br>
<br>
<a href="http://onlinegroups.net/blog/2014/02/25/how-to-whitelist-an-email-address/">How to whitelist an email address with Gmail, Outlook.com or Yahoo! Mail</a><br>
<br>
To change your password for the <code>www.feralhosting.com</code> website and Account Manager, please use this method:<br>
<br>
<a href="https://www.feralhosting.com/recover-account">Recover account</a><br>
<br>
<h2>Installable Software</h2><br>
A few of these methods will require you to use the command-line. <a href="https://www.feralhosting.com/faq/view?question=12">SSH guide basics - PuTTy</a> or <a href="https://www.feralhosting.com/faq/view?question=217">SSH guide basics - OS X</a><br>
<br>
<h2>Deluge WebUi password</h2><br>
To change the Deluge WebUI password:<br>
<br>
<strong>1:</strong> Login to the WebUI using the details in your Software Status page<br>
<br>
<strong>2:</strong> Click on <code>Preferences</code><br>
<br>
<strong>3:</strong> Click on <code>Interface</code> in the menu that opens up<br>
<br>
<strong>4:</strong> Enter your old password<br>
<br>
<strong>5:</strong> Enter the new password you wish to use!<br>
<br>
<img src="https://raw.github.com/feralhosting/feralfilehosting/master/Feral%20Wiki/General/Changing%20passwords/deluge.png"><br>
<br>
<h2>Transmission Password</h2><br>
 <strong>Important note:</strong> Transmission cannot manually restarted by the user. After killing the process allow up to 5 minutes for it to automatically to restart.<br>
<br>
To change the password in Transmission, you will need to <a href="https://www.feralhosting.com/faq/view?question=12">SSH</a> into your slot and stop it with this command:<br>
<br>
<pre><code>killall -9 -u $(whoami) transmission-daemon</code></pre><br>
Then edit the file using this command:<br>
<br>
Optionally, you can edit it over FTP, please read and understand this FAQ first - <a href="https://www.feralhosting.com/faq/view?question=219">Text editing - Over FTP or SFTP</a><br>
<br>
<pre><code>nano -w ~&#x2F;.config&#x2F;transmission-daemon&#x2F;settings.json</code></pre><br>
You need to edit a setting called:<br>
<br>
<pre><code>rpc-password</code></pre><br>
It iwll look something like this (hashed):<br>
<br>
<pre><code>&quot;rpc-password&quot;: &quot;{1ef8c83d004191e6db813e30be525c4b116e3da4BE6155cy&quot;,</code></pre><br>
You enter your new password in plain text like this.<br>
<br>
<pre><code>&quot;rpc-password&quot;: &quot;NEChu=@f%o9i*MH$SUZs&quot;,</code></pre><br>
Transmission will hash your password automatically when it starts.<br>
<br>
Then press and hold <code>CTRL</code> and then press <code>x</code> to save. Press <code>y</code> to confirm.<br>
<br>
 <strong>Important note:</strong> Make sure you finish editing the file before it starts back otherwise things might not work.<br>
<br>
<h2>ruTorrent WebUI Password</h2><br>
<strong>Important Note:</strong> Please also see this FAQ to manage your rutorrent password and users: <a href="https://www.feralhosting.com/faq/view?question=22">Password protect your WWW folder</a><br>
<br>
If you simply want to reset your ruTorrent password, you can request a reinstall from the <code>Install Software</code> link in your <a href="https://www.feralhosting.com/manager/">Account Manager</a> to get a new system-generated password for your ruTorrent web interface. <br>
<br>
If you want to set a custom password for your ruTorrent WebUI, you will need to <a href="https://www.feralhosting.com/faq/view?question=12">SSH</a> to your slot, and make changes to the <code>.htpasswd</code> file, using the <code>htpasswd</code> program. Again, please see this FAQ: <a href="https://www.feralhosting.com/faq/view?question=22">Password protect your WWW folder</a><br>
<br>
By default, when rutorrent is installed from the <code>Install Software</code> link in your <a href="https://www.feralhosting.com/manager/">Account Manager</a>, it places the <code>.htpasswd</code> file in the rutorrent folder.<br>
<br>
To use <code>htpasswd</code>, you need to execute the following set of commands in the shell:<br>
<br>
<pre><code>htpasswd -m ~&#x2F;www&#x2F;$(whoami).$(hostname -f)&#x2F;public_html&#x2F;rutorrent&#x2F;.htpasswd username</code></pre><br>
 <strong>Important note:</strong> Replace <code>username</code> with your Feral user name. This will prompt to update the password for this user if they exist instead of creating a new one.<br>
<br>
At this point, you will be prompted to enter your new password, and then verify.<br>
<br>
If you get the following error, it means that the location of your <code>.htpasswd</code> file is not where you specified:<br>
<br>
<pre><code>htpasswd: cannot modify file &#x2F;media&#x2F;DiskID&#x2F;home&#x2F;username&#x2F;www&#x2F;username.server.feralhosting.com&#x2F;public_html&#x2F;rutorrent&#x2F;.htpasswd; use &#x27;-c&#x27; to create it</code></pre><br>
Then please adjust the command line accordingly, from inside the rutorrent folder:<br>
<br>
<pre><code>htpasswd -cm ~&#x2F;www&#x2F;$(whoami).$(hostname -f)&#x2F;public_html&#x2F;rutorrent&#x2F;.htpasswd username</code></pre><br>
This will create a new and hashed <code>.htpasswd</code> file.<br>
<br>
You can get more info about the <code>htpasswd</code> command by typing <code>htpasswd</code> in SSH without any options. It will then display the options and switches you can use.<br>
<br>
<h3>Shell (SSH), FTP, and SFTP Passwords</h3><br>
Changing the default shell access password complicates things for the support team when you need their help. You will need to include your new password with every support ticket. If you are just looking to simplify your login procedure in PuTTY, consider <a href="https://www.feralhosting.com/faq/view?question=13">setting up publickey authentication</a>.<br>
<br>
FTP and your SFTP password can be changed by logging in via PuTTY and typing this command:<br>
<br>
<pre><code>passwd</code></pre><br>
You should then see on-screen prompts asking you for your current and new passwords.<br>
<br>
If you don&#x27;t remember your originally assigned password, please go to the <a href="https://www.feralhosting.com/manager/slot/">Slot Details</a> for your slot, and it will be listed there.<br>
<br>
<img src="https://raw.github.com/feralhosting/feralfilehosting/master/Feral%20Wiki/0%20Generic/slot_detail_link.png"><br>
<br>
<img src="https://raw.github.com/feralhosting/feralfilehosting/master/Feral%20Wiki/0%20Generic/slot_detail_ssh.png"><br>
<br>
