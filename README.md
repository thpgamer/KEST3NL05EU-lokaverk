# KEST3NL05EU-lokaverk
# 1. Install and configure the server1, client1 and client2 with hostnames and domain as ddp.is
### Server 1 (Ubuntu
![image](https://github.com/user-attachments/assets/eb617061-9367-45d9-8463-930e35f2f234)


### Client 1 (Ubuntu)
![image](https://github.com/user-attachments/assets/202e27a3-4424-4745-b38c-3b94f5805aaa)


### Client 2 (CentOS)
![image](https://github.com/user-attachments/assets/0e554994-859f-4d79-a5bd-678238347333)

# 2. Configure server1 with static IP Address, from the IP Address block 192.168.100.0/24. The server must be configured with the 10th usable IP Address.

Image from Server 1 /etc/netplan/50-cloud-init.yaml File
![image](https://github.com/user-attachments/assets/3fb664f4-e77c-48aa-a2f6-463717d5475e)

After I added the block of code I did "sudo netplan apply" to apply the changes to the .yaml file.




# 3. Install and configure DHCP on server1, so both clients get IP Addresses, Gateway, DNS IP address
and domain name automatically via HDCP.

Image from /etc/dhcp/dhcpd.conf to put everything right.
![image](https://github.com/user-attachments/assets/f32db00d-8d8f-44f4-a8cf-cb012af2716f)

5. Install and configure DNS server on server1, so Hostnames are resolved to IP Addresses.
6. Create the users accounts using a script, see the Users file.
7. Install and configure MySQL on server1 and create Human Resource database. The database
stores information about employees, employees are identified by their Kennitala, Firstname,
Lastname, Email, phone Number, hire date and salary. Employees work in Departments where
each department has one manager, departments are identified by department ID, department
name. Each department is in a different location. Locations are identified by their location ID, city,
Address, and zip code. One or more employees work in different jobs, and the jobs are identified
by Job ID, job title, Min salary and max salary.
8. Due to data lossthe company policy requires backups weekly, assystem engineer you are required
to schedule backups of home directories to run weekly at midnight each Friday.
9. Install and configure NTP on server1 and clients, server1 must be master server and client
must synchronize their time with the server.
10. Install and configure syslog server on server1, server1 should get logs from both the clients for
proactive management and monitoring.
11. Install and configure Postfix on server1, so users can send and receive emails using Round Cube
open-source software.
12. Install and configure shared printers for each group, only users that belong to the group should
print only, accept IT and Management groups should print and manage the printers.
13. For security reasons, install and configure SSH on the server and clients, SSH login should use RSA
keys instead of password authentication.
14. All unused ports should be closed, use NMAP for testing.
