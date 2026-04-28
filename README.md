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



