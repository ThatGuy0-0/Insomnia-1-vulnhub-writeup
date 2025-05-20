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

<h2 id="overview">🕵️ Overview</h2>
<blockquote>
Insomnia is a beginner to intermediate level boot2root machine hosted on VulnHub. The goal is to obtain root access and capture the flags hidden in the filesystem.
</blockquote>

<hr>

<h2 id="enumeration">🔍 Enumeration</h2>

<blockquote>
Basic host discovery and port scanning was done using <code>nmap</code>.
</blockquote>

<pre>"images/Screenshot%20(160).png</pre>

<blockquote>
Port 80 is open. Navigating to the IP in browser leads to a custom webpage.
</blockquote>

<img src="screenshots/02-web-homepage.png" alt="Web Homepage" width="700"/>

<hr>

<h2 id="web-application-analysis">🌐 Web Application Analysis</h2>

<blockquote>
Exploring the site reveals JavaScript files hinting at hidden credentials or functionality.
</blockquote>

<img src="screenshots/03-js-analysis.png" alt="JavaScript Analysis" width="700"/>

<blockquote>
One of the JS files leaks credentials for login.
</blockquote>

<img src="screenshots/04-js-credentials.png" alt="Found Credentials in JS" width="700"/>

<hr>

<h2 id="initial-foothold">📥 Initial Foothold</h2>

<blockquote>
Using the credentials found in the JavaScript file, I log in via the web form.
</blockquote>

<img src="screenshots/05-login-success.png" alt="Login Success" width="700"/>

<blockquote>
Post-authentication reveals a command injection vulnerability in the hostname parameter.
</blockquote>

<img src="screenshots/06-command-injection.png" alt="Command Injection" width="700"/>

<blockquote>
I use this to gain a reverse shell on the machine.
</blockquote>

<img src="screenshots/07-reverse-shell.png" alt="Reverse Shell" width="700"/>

<hr>

<h2 id="privilege-escalation">⬆️ Privilege Escalation</h2>

<blockquote>
Enumerating the system shows a binary with SUID bit set: <code>/opt/insomnia/bin/rootutil</code>.
</blockquote>

<img src="screenshots/08-suid-rootutil.png" alt="SUID Binary Found" width="700"/>

<blockquote>
Running the binary reveals it's vulnerable to PATH manipulation. I hijack <code>ls</code> to escalate to root.
</blockquote>

<img src="screenshots/09-path-hijack.png" alt="PATH Hijacking" width="700"/>

<hr>

<h2 id="flags">🚩 Flags</h2>

<blockquote>
User flag found in <code>/home/insomnia/user.txt</code>:
</blockquote>

<img src="screenshots/10-user-flag.png" alt="User Flag" width="700"/>

<blockquote>
Root flag located in <code>/root/root.txt</code> after successful privilege escalation:
</blockquote>

<img src="screenshots/11-root-flag.png" alt="Root Flag" width="700"/>

<hr>

<p><strong>✅ Machine rooted successfully!</strong></p>
