# Silver Platter



<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Enumeration

We'll start with a simple nmap scan to discover open ports.\


```
nmap -sS -Pn silverplatter.thm
```

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

We find 22, 80, and 8080, open. Let's check out the webpage.

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

It brings us to a simple website and we find some information about a username on an website or app called Silverpeas.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Unfortuneately we don't find anything on port 8080 but lets enumerate using Gobuster.

I found a couple of webpages on port 80 but they didn't lead me to anything. \
\
However on port 8080 if we visit /silverpeas using our information from earlier, we get a login page.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

After many unsuccessful attempts and some research I found a CVE for Silverpeas that can let us bypass the password.\


Using [CVE-2024-36042](https://gist.github.com/ChrisPritchard/4b6d5c70d9329ef116266a6c238dcb2d) and Burp suite to intercept the POST request and remove the password field, we can be redirected to the main page as a admin.



<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

