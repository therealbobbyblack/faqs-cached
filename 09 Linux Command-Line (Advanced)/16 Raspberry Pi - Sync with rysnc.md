<h1>Raspberry Pi - Sync with rysnc</h1>

        
This FAQ will explain, how you can sync specific folders to a raspberry pi, using cron and rsync. Furthermore, this guide stands out, when we come to the cron part. In most setups, you will not be able to see the ETA &#x2F; download speed, if it&#x27;s a cronjob running it. This was made possible, from the help of <strong>konichiwa</strong> and <strong>ozymandias</strong>. In addition, we will be running the crontab in sudo, as this can come in handy, if you want to add different jobs, like restarting the pi. However, this is optional. <br>
<br>
<h2>Step 1: SSH into your pi</h2><br>
I strongly recommend to SSH into your pi, so you can just copy and paste the code, directly to the pi.<br>
<br>
<strong>1:</strong> First, we want to open the SSH from outside sources. To do that, you will have to connect your pi to your monitor, and plug in a keyboard. (Mouse not needed). Log into your pi by using:<br>
<br>
<strong>Username:</strong> <code>pi</code><br>
<br>
<strong>Password default:</strong> <code>raspberry</code><br>
<br>
To get into the config of the pi, type:<br>
<br>
<pre><code>sudo raspi-config</code></pre><br>
That will get you into a GUI.<br>
<br>
A super good idea, is first to <code>expand filesystem</code> then <code>Change User Password</code>. Now <code>Add to Rastrack</code> and <code>Internationalisation Options</code>. Here you can change the language if you want to, as well as the keyboard layout, and time zone. It is also safe, to enter the <code>Overclock</code> settings, and chose the preset <code>medium</code>. If you do this, you will also have to reboot the pi, before we continue. You are prompted for that. If not, type:<br>
<br>
<pre><code>sudo reboot</code></pre><br>
Now to enable the SSH access.&nbsp; Enter <code>Advanced Options</code>, then <code>SSH</code>, and <code>Enable</code>. <br>
<br>
Before we exit, go into <code>Advanced Options</code> again, and choose <code>Update</code>. This will update the entire pi. It will take <code>3</code> to <code>10</code> min, depending on overclock, and your SD card&#x27;s speed class. <br>
<br>
An SD card speed class 10 can have a noticeable difference difference in large file transfers. Just as big as going from HDD to SSD on a computer, if not bigger.<br>
<br>
Reboot your pi, by using:<br>
<br>
<pre><code>sudo reboot</code></pre><br>
Go ahead, and unplug the pi form you monitor, as well as your keyboard.<br>
<br>
<strong>2:</strong> Now on your regular computer, you will need to connect to the pi.<br>
<br>
To do that, follow this guide: <a href="https://www.feralhosting.com/faq/view?question=12">SSH</a>. Just replace <code>server.feralhosting.com</code>, with the IP of your raspberry pi.<br>
<br>
<h2>Step 2: Installing rsync</h2><br>
Since I want to make this using <code>sudo</code>, i will do this:<br>
<br>
<pre><code>sudo crontab -e</code></pre><br>
and not just using this command:<br>
<br>
<pre><code>crontab -e</code></pre><br>
I will be running everything with <code>sudo</code> in front of every command. This is especially important, when we make the SSH-keys, since <code>sudo</code> will store the keys in different places.<br>
<br>
If you don&#x27;t want to use this command:<br>
<br>
<pre><code>sudo crontab -e</code></pre><br>
but prefer to only use this version:<br>
<br>
<pre><code>crontab -e</code></pre><br>
Then don&#x27;t use <code>sudo</code> at all.<br>
<br>
<strong>1: rsync</strong> To install rsync, SSH into your pi, and run this command:<br>
<br>
<pre><code>sudo apt-get install rsync</code></pre><br>
We will leave that for now, since we will make the SSH-keys first.<br>
<br>
<h2>Step 3: Install SSH-keys</h2><br>
We want SSH-keys with no passphrase, since it is supposed to do the sync by its own, automatically.<br>
<br>
<strong>1: Making the keys</strong><br>
<br>
First, go to the SSH screen of the pi.<br>
Remember, if you want to use <br>
<br>
<pre><code>sudo crontab -e</code></pre><br>
Use sudo, or if you chose not to use sudo:<br>
<br>
<pre><code>crontab -e</code></pre><br>
Then run this command:<br>
<br>
<pre><code>sudo ssh-keygen -q -t rsa -b 2048 -N &#x27;&#x27;</code></pre><br>
You can specify a name and path for this file if you want using the <code>-f</code> argument:<br>
<br>
<pre><code>sudo ssh-keygen -q -f &quot;&#x2F;root&#x2F;.ssh&#x2F;mykey.pub&quot; -t rsa -b 2048 -N &#x27;&#x27;</code></pre><br>
This will create a key, with no passphrase. It will hang for some time. When is asks where to save it, just press enter, since we want it in the default directory.<br>
<br>
Now we want to transfer the public key, to feralhosting.<br>
<br>
Type this command:<br>
<br>
<pre><code>sudo ssh-copy-id -i &#x2F;root&#x2F;.ssh&#x2F;id_rsa.pub user@server.feralhosting.com</code></pre><br>
Done with the SSH-keys.<br>
<br>
<h2>Step 4: Making the rsync script</h2><br>
We want to make many of these commands, and we want them to be run every 5 min. But if we simply put this into cron, cron will run it every 5 min. Since it&#x27;s unlikely that the first will be done in 5 min, multiple rsync&#x27;s will begin, downloading the same file. Then finally crash the pi. We would very much wan, to avoid this:<br>
<br>
There is this thing called flock. It will create a <code>flocktmp.lock</code> file, denying any new rsync&#x27;s beginning, until the first one is done. We can simply not use this for all the rsync commands. That&#x27;s why we are going to put all the rsync&#x27;s, into one script.<br>
<br>
<strong>1: The script</strong><br>
<br>
In the pi&#x27;s terminal, type:<br>
<br>
<pre><code>sudo mkdir $HOME&#x2F;scripts&#x2F;</code></pre><br>
Now we have the directory, and we now want to make the script. Type:<br>
<br>
<pre><code>sudo nano $HOME&#x2F;scripts&#x2F;SB_sync</code></pre><br>
Since we want to make a shell script, type this at the top:<br>
<br>
<pre><code> #!&#x2F;bin&#x2F;sh</code></pre><br>
<strong>2: Making the rsync command</strong><br>
<br>
On the FOURTH line (keep two empty lines under <code>#!&#x2F;bin&#x2F;sh</code>), we are now ready to make the rsync command. Here is an example:<br>
<pre><code>&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;path&#x2F;to&#x2F;remote&#x2F;dir&#x2F;&quot;&#x27; &quot;&#x2F;path&#x2F;to&#x2F;store&#x2F;on&#x2F;pi&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;1.name.of.folder.log </code></pre><br>
I&#x27;ve already set up my hard drive to my pi, and I want to sync my folder called <code>iRL HD</code><br>
So MY rsync command would look like this:<br>
<br>
<pre><code>&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;private&#x2F;rtorrent&#x2F;data&#x2F;iRL HD&#x2F;&quot;&#x27; &quot;&#x2F;media&#x2F;HDD01&#x2F;shares&#x2F;SB&#x2F;iRL HD&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;1.iRL.HD.log</code></pre><br>
For now, exit using <code>ctrl+x</code>, then <code>y</code> and enter, to save the script.<br>
We also want this to be executable.<br>
To do that, type:<br>
<br>
<pre><code>sudo chmod +x $HOME&#x2F;scripts&#x2F;SB_sync</code></pre><br>
What this basically does <code>--chmod=a+rwx</code>, is setting the permissions on the downloaded files, so EVEYONE gets access to it. The permission are equal to <code>chmod 777</code>. <br>
What the -delete parameter does, is to delete the files on the pi, if they are deleted from the seedbox. If you don&#x27;t want this, you can simply remove the <code>-delete</code> parameter, and the pi won&#x27;t delete anything, only download.<br>
What is the <code>2&gt;&amp;1 &gt;</code> for? That I will explain later. But it&#x27;s related to our little cron trick. <br>
<br>
<h2>Step 5: The cron trick</h2><br>
I will first explain what exactly it does. The <code>2&gt;&amp;1 &gt;</code> is a part of a log writing progress. If you write the rsync directly in the terminal, you would see the download speed, &#x2F; size &#x2F; ETA. We still want to be able to do this, even though it is being run automatically. <code>2&gt;&amp;1 &gt;</code> will write a log, to <code>&#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;1.name.of.folder.log</code>, containing the same info, as if it was being run manually in the terminal. So we will be able to see, how long the individual download is. Since the <code>.log</code> is being kept writing to, just close the log, and open it again, and it will be updated. The &gt; at the end, means that it will overwrite the .log file, when it is being run again. That way we wont get a super long log file. IF you want a super long log file, just add an extra <code>&gt;</code>. So it would be&nbsp; <code>2&gt;&amp;1 &gt;&gt;</code>. I strongly recommend, not doing this though.<br>
<br>
This gives us an individual detailed log, for every rsync you make. We also want a log, just showing the overall progress, of which rsync it is doing right now right?<br>
<br>
To do this, go ahead and open up your script again.<br>
<br>
<strong>1: Completing the script</strong><br>
<br>
To open your script, type:<br>
<br>
<pre><code>sudo nano $HOME&#x2F;scripts&#x2F;SB_sync</code></pre><br>
Right under <code>#!&#x2F;bin&#x2F;sh</code>, type: <code>mkdir -p &#x2F;home&#x2F;pi&#x2F;SB_sync_logs</code>. This will make a directory, for all the small logs.<br>
<br>
On the third line, type: <code>echo &quot;Syncing my rsync&quot; &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log</code>. This will be the text showing up, in the <code>overall status</code> log.<br>
<br>
Your script should now look like this:<br>
<br>
<pre><code>#!&#x2F;bin&#x2F;sh
mkdir -p &#x2F;home&#x2F;pi&#x2F;SB_sync_logs
echo &quot;Syncing my rsync&quot; &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log
&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;path&#x2F;to&#x2F;remote&#x2F;dir&#x2F;&quot;&#x27; &quot;&#x2F;path&#x2F;to&#x2F;store&#x2F;on&#x2F;pi&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;1.name.of.folder.log</code></pre><br>
Repeat this progress, if you want another rsync in this script.<br>
<br>
My whole script looks like this:<br>
<br>
<pre><code>#!&#x2F;bin&#x2F;sh
mkdir -p &#x2F;home&#x2F;pi&#x2F;SB_sync_logs
echo &quot;Syncing iRL HD&quot; &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log
&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;private&#x2F;rtorrent&#x2F;data&#x2F;iRL HD&#x2F;&quot;&#x27; &quot;&#x2F;media&#x2F;HDD01&#x2F;shares&#x2F;SB&#x2F;iRL HD&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;1.iRL.HD.log
echo &quot;Syncing SUBLiME HD&quot; &gt;&gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log
&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;private&#x2F;rtorrent&#x2F;data&#x2F;SUBLiME HD&#x2F;&quot;&#x27; &quot;&#x2F;media&#x2F;HDD01&#x2F;shares&#x2F;SB&#x2F;SUBLiME HD&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;2.SUBLiME.HD.log
echo &quot;Syncing Mikkel&quot; &gt;&gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log
&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;private&#x2F;rtorrent&#x2F;data&#x2F;Mikkel&#x2F;&quot;&#x27; &quot;&#x2F;media&#x2F;HDD01&#x2F;shares&#x2F;SB&#x2F;Mikkel&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;3.Mikkel.log
echo &quot;Syncing Far&quot; &gt;&gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log
&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;private&#x2F;rtorrent&#x2F;data&#x2F;Far&#x2F;&quot;&#x27; &quot;&#x2F;media&#x2F;HDD01&#x2F;shares&#x2F;SB&#x2F;Far&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;4.Far.log
echo &quot;Syncing Fannie&quot; &gt;&gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log
&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;private&#x2F;rtorrent&#x2F;data&#x2F;Fannie&#x2F;&quot;&#x27; &quot;&#x2F;media&#x2F;HDD01&#x2F;shares&#x2F;SB&#x2F;Fannie&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;5.Fannie.log
echo &quot;Syncing Agents Of S.H.I.E.L.D&quot; &gt;&gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log
&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;private&#x2F;rtorrent&#x2F;data&#x2F;TV - Agents Of S.H.I.E.L.D&#x2F;&quot;&#x27; &quot;&#x2F;media&#x2F;HDD01&#x2F;shares&#x2F;SB&#x2F;TV - Agents Of S.H.I.E.L.D&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;6.Agents.Of.S.H.I.E.L.D.log
echo &quot;Syncing Arrow&quot; &gt;&gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log
&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;private&#x2F;rtorrent&#x2F;data&#x2F;TV - Arrow&#x2F;&quot;&#x27; &quot;&#x2F;media&#x2F;HDD01&#x2F;shares&#x2F;SB&#x2F;TV - Arrow&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;7.Arrow.log
echo &quot;Syncing Dexter&quot; &gt;&gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log
&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;private&#x2F;rtorrent&#x2F;data&#x2F;TV - Dexter&#x2F;&quot;&#x27; &quot;&#x2F;media&#x2F;HDD01&#x2F;shares&#x2F;SB&#x2F;TV - Dexter&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;8.Dexter.log
echo &quot;Syncing The Walking Dead&quot; &gt;&gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log
&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;private&#x2F;rtorrent&#x2F;data&#x2F;TV - The Walking Dead&#x2F;&quot;&#x27; &quot;&#x2F;media&#x2F;HDD01&#x2F;shares&#x2F;SB&#x2F;TV - The Walking Dead&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;9.The.Walking.Dead.log
echo &quot;Syncing Revolution&quot; &gt;&gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log
&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;private&#x2F;rtorrent&#x2F;data&#x2F;TV - Revolution&#x2F;&quot;&#x27; &quot;&#x2F;media&#x2F;HDD01&#x2F;shares&#x2F;SB&#x2F;TV - Revolution&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;10.Revolution.log
echo &quot;Syncing The Big Bang Theory&quot; &gt;&gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log
&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;private&#x2F;rtorrent&#x2F;data&#x2F;TV - The Big Bang Theory&#x2F;&quot;&#x27; &quot;&#x2F;media&#x2F;HDD01&#x2F;shares&#x2F;SB&#x2F;TV - The Big Bang Theory&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;11.Big.Bang.Theory.log
echo &quot;Syncing What.cd&quot; &gt;&gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log
&#x2F;usr&#x2F;bin&#x2F;rsync -avhPS --chmod=a+rwx --delete user@server.feralhosting.com:&#x27;&quot;private&#x2F;rtorrent&#x2F;data&#x2F;What.cd&#x2F;&quot;&#x27; &quot;&#x2F;media&#x2F;HDD01&#x2F;shares&#x2F;SB&#x2F;What.cd&quot; 2&gt;&amp;1 &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync_logs&#x2F;12.What.cd.log
echo &quot;Syncing Complete&quot; &gt;&gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log</code></pre><br>
Please notice, that ONLY the first <code>echo &quot;Syncing my folder&quot; &gt; &#x2F;home&#x2F;pi&#x2F;SB_sync.log</code>, have ONE &gt;. All the others should have two <code>&gt;&gt;</code>. As you can see in my script above. Also notice, that the numbers in the rsync command itself, goes higher and higher, depending on how many rsyncs you have.<br>
<br>
Then press and hold <code>CTRL</code> and then press <code>x</code> to save. Press <code>y</code> to confirm.<br>
<br>
<h2>Step 6: Check the script</h2><br>
Okay, so now we want to see if the script actually works. The first time it is used, you will have to accept feralhosting&#x27;s fingerprint. Else cron won&#x27;t be able to run it. The smart thing with our cron logs, is that it does not only show progress, it also shows errors. This can come in very handy.<br>
<br>
So to accept feralhosting&#x27;s fingerprint, we will have to run the script manually at least one time.<br>
<br>
Type:<br>
<br>
<pre><code>$HOME&#x2F;scripts&#x2F;SB_sync</code></pre><br>
Then accept feralhosting&#x27;s fingerprint. When the sync then starts, just cancel it again. Hold <code>CTRL</code> and then press <code>c</code> to do that. <br>
<br>
<h2>Step 7: flock</h2><br>
As said earlier, we only want this script run, if it aint already running. To do that, we need to modify our cron job, in the cron tab. <br>
<br>
<strong>1: Making the cron entry</strong><br>
<br>
Type:<br>
<br>
<pre><code>sudo crontab -e</code></pre><br>
Go to the bottom of the document, (one empty line between the above text, to our entry) and type:<br>
<br>
<pre><code> *&#x2F;5 * * * * &#x2F;usr&#x2F;bin&#x2F;flock -xn &#x2F;tmp&#x2F;flocktmp.lock -c &quot;&#x2F;home&#x2F;pi&#x2F;scripts&#x2F;SB_sync&quot;</code></pre><br>
This will run the script, every five min.<br>
Then <code>ctrl+x</code> then <code>y</code>, to save and exit. <br>
And that is basically it. Thank you for following this FAQ. <br>
Credit to konichiwa, and ozymandias, for helping out with the technical stuff.<br>
<br>
-by Zeroz<br>
<br>
<h2>Helpful pictures</h2><br>
<strong>How the overall log file looks</strong><br>
<br>
<img src="https://raw.github.com/feralhosting/feralfilehosting/master/Feral%20Wiki/Linux%20Command-Line%20-%20Advanced/Raspberry%20Pi%20-%20Sync%20with%20rysnc/1.1.png"><br>
<br>
<img src="https://raw.github.com/feralhosting/feralfilehosting/master/Feral%20Wiki/Linux%20Command-Line%20-%20Advanced/Raspberry%20Pi%20-%20Sync%20with%20rysnc/2.2.png"><br>
<br>
The file will grow longer, the longer the script goes, and eventually say <code>Syncing Complete</code><br>
<br>
<strong>How the detailed log files look</strong><br>
<br>
<img src="https://raw.github.com/feralhosting/feralfilehosting/master/Feral%20Wiki/Linux%20Command-Line%20-%20Advanced/Raspberry%20Pi%20-%20Sync%20with%20rysnc/3.3.png"><br>
<br>
<img src="https://raw.github.com/feralhosting/feralfilehosting/master/Feral%20Wiki/Linux%20Command-Line%20-%20Advanced/Raspberry%20Pi%20-%20Sync%20with%20rysnc/4.4.png"><br>
<br>
<h2>Troubleshooting</h2><br>
<strong>Are you having problems with permissions?</strong><br>
<br>
This command, will give it <code>777</code> rights (Open to EVERYONE)<br>
<br>
<pre><code>sudo chmod -R 777 *</code></pre><br>
<strong>Still having problems with permissions?</strong><br>
<br>
Type this:<br>
<br>
<pre><code>sudo chown pi:pi -R *</code></pre><br>
<strong>The log showing up weird using SSH?</strong><br>
<br>
You can also use nano in terminal. The catch is, that nano will just show one long gigantic line. You can go to the end of that line, by pressing <code>ctrl</code> plus <code>e</code> and to the start of the line, by pressing <code>ctrl</code> plus <code>a</code>. But if you want to have a MUCH better overview, you&#x27;re want to use tightvnc.<br>
<br>
That&#x27;s because you need the GUI, to view the log properly. Follow this guide, to install <code>tightvnc</code>, so you can view your raspberry pi&#x27;s desktop remotely - <a href="http://programmaticponderings.wordpress.com/2012/12/26/installing-tightvnc-on-the-raspberry-pi/">installing tightvnc on the raspberry pi</a> <br>
<br>
<strong>Another way of monitoring if the pi is downloading.</strong><br>
<br>
We can see the general internet from the pi, using <code>bwm-ng</code>.<br>
<br>
To get this, type:<br>
<br>
<pre><code>sudo apt-get install bwm-ng</code></pre><br>
When you now type <code>bwm-ng</code> in pi&#x27;s terminal, you can see the general speed the whole pi is using. But do you really want to type that long sentence each time to check the speed? NO<br>
<br>
We want to create an alias. So we only need to type <code>speed</code>, to see the speed.<br>
<br>
Type this in the terminal:<br>
<br>
<pre><code>sudo nano ~&#x2F;.bash_aliases</code></pre><br>
Now type this in:<br>
<br>
<pre><code>alias speed=&#x27;bwm-ng&#x27;</code></pre><br>
Then press and hold <code>CTRL</code> and then press <code>x</code> to save. Press <code>y</code> to confirm.<br>
<br>
For the alias to take effect, reboot the pi using:<br>
<br>
<pre><code>sudo reboot</code></pre><br>
<strong>You wonder if you can see the temperature of the pi, still using a alias?</strong><br>
<br>
Ofc. This also comes in handy when you overclock, to monitor your pi won&#x27;t get hotter than 70deg.<br>
In terminal type:<br>
<br>
<pre><code>nano ~&#x2F;.bash_aliases</code></pre><br>
Now add this to the document:<br>
<br>
<pre><code> alias temp=&#x27;&#x2F;opt&#x2F;vc&#x2F;bin&#x2F;vcgencmd measure_temp&#x27;</code></pre><br>
As you proberly can see, you would have to type: <code>&#x2F;opt&#x2F;vc&#x2F;bin&#x2F;vcgencmd measure_temp</code>, to see the temp, but now you only need to write <code>temp</code>.<br>
<br>
Then press and hold <code>CTRL</code> and then press <code>x</code> to save. Press <code>y</code> to confirm.<br>
<br>
For the alias to take effect, reboot the pi using:<br>
<br>
<pre><code>sudo reboot</code></pre><br>
<br>
