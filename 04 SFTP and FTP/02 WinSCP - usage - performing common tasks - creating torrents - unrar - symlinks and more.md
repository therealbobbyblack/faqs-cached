<h1>WinSCP - usage - performing common tasks - creating torrents - unrar - symlinks and more</h1>

        
<br>
WinSCP is an open source SFTP client and FTP client for Windows. Its main function is the secure file transfer between a local and a remote computer. Beyond this, WinSCP offers basic file manager functionality. It uses Secure Shell (SSH) and supports, in addition to Secure FTP, also legacy SCP protocol.<br>
<br>
WinSCP is often disregarded by many users. The purpose of this tutorial, however, is to show you that WinSCP is much more than a file transfer client - it offers a clean, well designed graphic user interface (GUI) for performing many common tasks that otherwise could be confusing for a novice Linux user to achieve in the shell.<br>
<br>
WinSCP is a very useful program for novice users.<br>
<br>
<h3>Getting Familiar with WinSCP</h3><br>
<a href="http://winscp.net/download/winscp517setup.exe">Download</a> the installer version 5.1.7. This installation package has certain explorer like plug-ins for drag and drop.<br>
<br>
<a href="http://winscp.net/download/winscp517.zip">Download</a> Portable version 5.1.7. This way WinSCP will store all its settings in its own directory, not writing anything into the registry. Portable also means you will be able to run it from your USB drive.<br>
<br>
Install or Save the executable to a directory of your choice and launch it. It will display a configuration window. Fill out the fields as suggested in the screen-shot below:<br>
<br>
<img src="http://i31.tinypic.com/3133fdk.jpg"><br>
<br>
You can find your access details <a href="https://www.feralhosting.com/manager/">[b]Slot&#x2F;Servername[&#x2F;b] link in your manager</a> (the Shell Access section).<br>
<br>
Please note that if you followed our tutorial on <a href="https://www.feralhosting.com/faq/view?question=13">setting up public key authentication with PuTTY</a> and created your key pair, you can provide the path to your private key file here â€” in this case there&#x27;s no need to fill out the password field.<br>
<br>
<strong>Important note:</strong> Please be sure to select SFTP as your connection protocol.<br>
<br>
Please note that WinSCP might time out when executing a command â€” this is normal and should not frighten you :) â€” WinSCP displays a time-out warning if it doesn&#x27;t receive a response from the server within 15 seconds, and some commands may need more than that to complete. Just click <strong>OK</strong> and disregard the time-out message â€” the command will complete in the background.<br>
<br>
We suggest however that, while still in the WinSCP Login configuration dialogue, you click on <strong>Connection</strong> in the left pane and set the server response time-out to 600 seconds as shown in the screen-shot below.<br>
<br>
<img src="http://i31.tinypic.com/2isioi1.jpg"><br>
<br>
Click <strong>Save</strong> and give your session a name â€” for example, MY FERAL BOX. Be sure to tick the <strong>Save password</strong> option (even though it says it is not recommended).<br>
<br>
Click <strong>Login</strong>. WinSCP will connect to your Feral box, and you will be presented with a view comparable to that of a regular FTP client â€” local files on the left, and remote files on the right:<br>
<br>
<img src="http://i32.tinypic.com/aew6ye.jpg"><br>
<br>
You can transfer files from your computer to the server and back, but WinSCP tends to be slow at that due to the fact that it encrypts the data on the fly. We suggest you use regular FTP over <a href="https://www.feralhosting.com/faq/view?question=5">VPN</a> for your file transfers, or <a href="https://www.feralhosting.com/faq/view?question=20">download your files via http</a>.<br>
<br>
WinSCP does shine however at helping you perform common server tasks easily and effortlessly â€” something that you might have not felt comfortable with when using SSH.<br>
<br>
Let&#x27;s see how this works.<br>
<br>
Navigate to your rTorrent data dir â€”<br>
<br>
<pre><code>&#x2F;home&#x2F;YOUR_USERNAME&#x2F;private&#x2F;rtorrent&#x2F;data</code></pre><br>
â€” and right-click on any DIR in there. As you can see, among other things, you can:<br>
<br>
- duplicate a DIR (create a copy of the current DIR and move it elsewhere on the server);<br>
<br>
- move a DIR (use <strong>Move to</strong> to move a DIR elsewhere on the server; the <strong>Move</strong> command will move a dir to your local drive);<br>
<br>
- change properties (i.e. chmod);<br>
<br>
- apply custom commands, and that is where the real power is.<br>
<br>
<strong>Configuring Custom Commands</strong><br>
<br>
<strong>When running commands in WinSCP, you may get the following message: &quot;Current SFTP-3 session does not support command you request. Separate shell session may be opened to process the command. Do you want to open a separate shell session?&quot; Tick <strong>&quot;don&#x27;t ask me again&quot;</strong> and click <strong>OK</strong>.</strong><br>
<br>
Right-click on the top toolbar and select <strong>Custom Command Buttons</strong>.<br>
<br>
<img src="http://i32.tinypic.com/n6cnpv.jpg"><br>
<br>
Your custom command bar will have less buttons since we have not configured our custom commands yet. Let&#x27;s do that.<br>
<br>
<strong>CUSTOM COMMAND: CREATING A TORRENT</strong><br>
<br>
Click on the last button in the custom command bar (<strong>Customize Custom Commands</strong> button). Then click <strong>Add</strong>.<br>
<br>
You will see a custom command configuration window:<br>
<br>
<img src="http://i25.tinypic.com/x1wx21.jpg"><br>
<br>
Let&#x27;s create a custom command for creating a torrent for what.cd.<br>
<br>
Give your command a name and fill out the <strong>Description</strong> field. Let&#x27;s call it WHAT.<br>
<br>
<img src="http://i31.tinypic.com/14josaf.jpg"><br>
<br>
Select <strong>Remote command</strong> and <strong>Apply to directories</strong>.<br>
<br>
If you tick the last setting â€” <strong>Show results in terminal</strong> â€” then each time you create a torrent you will be shown the results in the console â€” useful for verifying that everything went well.<br>
<br>
Now let&#x27;s create the command itself:<br>
<br>
<pre><code>mktorrent -a &#x27;<a href="http://YOUR_ANNOUNCE_URL">http:&#x2F;&#x2F;YOUR_ANNOUNCE_URL</a>&#x27; -p -l 21 !&amp;</code></pre><br>
Please note that the announce URL is placed in between single quotes. Substitute the example URL with your actual what.cd announce URL and fill out the custom command field. Click <strong>OK</strong>.<br>
<br>
What exactly does this command do?<br>
<br>
- creates a .torrent with your custom announce URL in it from the data in the selected dir;<br>
<br>
- names the .torrent by the name of the selected dir;<br>
<br>
- marks the .torrent as private;<br>
<br>
- uses the <strong>-l 21</strong> switch for a non-default piece length which will (most probably) result in a different hash as compared to the original .torrent, which in its turn will let you seed the newly created .torrent in rTorrent along with the original one without any <a href="https://www.feralhosting.com/faq/view?question=26">additional hacks</a>;<br>
<br>
- places the newly created .torrent in the current dir; now you can first copy it to you local drive and upload it to what.cd, and then right-click on it, choose <strong>Move to</strong> and move it to your rTorrent watch folder:<br>
<br>
<pre><code>&#x2F;home&#x2F;YOUR_USERNAME&#x2F;private&#x2F;rtorrent&#x2F;watch&#x2F;</code></pre><br>
Please note that WinSCP remembers the paths you supply when running commands. Later on there will be no need to type in full paths again â€” you will just select a previously used path from a dropdown menu. Having a smart GUI can be nice.<br>
<br>
Our first custom command is ready, and you should see a WHAT button in your custom command toolbar. Let&#x27;s test it.<br>
<br>
Navigate to your rTorrent data dir, choose any dir, select it â€” now you can either right-click on it and choose <strong>Custom commands</strong> &gt; <strong>WHAT</strong>, or simply press the WHAT button in your custom commands toolbar to apply the command to the selected dir.<br>
<br>
Repeat these steps&nbsp; to create custom commands for making torrents for other trackers of your choice. You can have as many as you like.<br>
<br>
As you can see, now it is a matter of simply selecting a dir and pressing a button. This is even faster and easier than in uTorrent, and that means something :).<br>
<br>
<strong>CUSTOM COMMAND: UNRAR&#x27;ING</strong><br>
<br>
Here is a pastebin of the Feral Unrar Manual.<br>
<br>
<a href="http://pastebin.com/ds1w4Nzv">Unrar Manual - Usage</a><br>
<br>
another handy link regarding usage:<br>
<br>
<a href="http://linux.die.net/man/1/unrar">Unrar Man Page http:&#x2F;&#x2F;linux.die.net</a><br>
<br>
Follow the same steps to create a new custom command, and call it <strong>UNRAR</strong>.<br>
<br>
<img src="http://i26.tinypic.com/21or9dd.jpg"><br>
<br>
The command you will need to use is:<br>
<br>
This command below uses e (Extract files to current directory) and -r (Recurse subdirectories} if they have the extension .rar<br>
<br>
<pre><code>unrar e -r *.rar</code></pre><br>
This command below will unrar everything that is a rar<br>
<br>
<pre><code>unrar x &quot;*&quot;</code></pre><br>
This command below will unrar everything that is a rar to a specific location, such as a WWW folder you created.<br>
<br>
<pre><code>unrar x &quot;*&quot; Full&#x2F;path&#x2F;to&#x2F;wanted&#x2F;location</code></pre><br>
To run these commands, navigate to any dir containing rar&#x27;ed files, select a .rar and press the <strong>UNRAR</strong> button in your custom command toolbar. There will be a delay of about 1-2 minutes, after which you will be presented a console with the operation results. The unrar&#x27;ed file will be found in the same dir where you ran the command.<br>
<br>
<strong>Creating Symbolic Links (Symlinks)</strong><br>
<br>
If you followed our tutorial on <a href="https://www.feralhosting.com/faq/view?question=20">setting up your public_html folder</a>, you might remember that creating symlinks via SSH requires a good deal of concentration. With WinSCP it&#x27;s just a couple of clicks.<br>
<br>
Use WinSCP to navigate to<br>
<br>
<pre><code>&#x2F;media&#x2F;DISKID&#x2F;home&#x2F;username&#x2F;www&#x2F;username.server.feralhosting.com&#x2F;public_html&#x2F;</code></pre><br>
Right-click anywhere on the empty space in the remote pane and choose <strong>New</strong> &gt; <strong>Link...</strong>.<br>
<br>
<img src="https://raw.github.com/feralhosting/feralfilehosting/master/Feral%20Wiki/SFTP%20and%20FTP/WinSCP%20-%20usage%20-%20performing%20common%20tasks%20-%20creating%20torrents%20-%20unrar%20-%20symlinks%20and%20more/symlink.png"><br>
<br>
Choose a name for your symlink (it can be anything) and supply the path to your rtorrent data dir as shown in the screen-shot above. <br>
<br>
The path to your default rtorrent data folder will be like this for example:<br>
<br>
<pre><code>&#x2F;media&#x2F;DISKID&#x2F;home&#x2F;username&#x2F;private&#x2F;rtorrent&#x2F;data&#x2F;</code></pre><br>
Where <strong>DISKID</strong> is your disk ID and <strong>username</strong> is your Feral username from the <strong>Full Path</strong>.<br>
<br>
Click <strong>OK</strong>. And you&#x27;re done.<br>
<br>
<strong>FLAC to MP3 Conversion and Automatic What.CD .torrent Creation</strong><br>
<br>
Please see the Guide <a href="https://www.feralhosting.com/faq/view?question=38">here</a>.<br>
<br>
