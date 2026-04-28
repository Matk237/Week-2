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
