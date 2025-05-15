# KEST3NL05EU-lokaverk
# 1. Install and configure the server1, client1 and client2 with hostnames and domain as ddp.is
### Server 1 (Ubuntu)
![image](https://github.com/user-attachments/assets/eb617061-9367-45d9-8463-930e35f2f234)

/etc/hosts of Server 1:
![Skjámynd 2025-05-15 113143](https://github.com/user-attachments/assets/401da22e-5096-4bda-a368-24dbe72669d2)



### Client 1 (Ubuntu)
![image](https://github.com/user-attachments/assets/202e27a3-4424-4745-b38c-3b94f5805aaa)

/etc/hosts of Client 1:

![image](https://github.com/user-attachments/assets/bead782c-e740-4a4d-b9b2-cee0466ca442)




### Client 2 (CentOS)
![image](https://github.com/user-attachments/assets/0e554994-859f-4d79-a5bd-678238347333)

/etc/hosts of Client 2:

![image](https://github.com/user-attachments/assets/646781c2-2d60-4355-87ac-5d991a978d54)


# 2. Configure server1 with static IP Address, from the IP Address block 192.168.100.0/24. The server must be configured with the 10th usable IP Address.

### "ip a" of Server 1 where you can see ens37 being 192.168.100.10 which is the static IP 
![Skjámynd 2025-05-15 113735](https://github.com/user-attachments/assets/f793abb9-bd42-4af0-be97-12e2962b5fa1)


```sudo nano /etc/netplan/01-netcfg.yaml```
![image](https://github.com/user-attachments/assets/2e4d728e-6f10-4aa9-ad8d-e5df923547e7)


# 3. Install and configure DHCP on server1, so both clients get IP Addresses, Gateway, DNS IP address and domain name automatically via HDCP.
### server1 (Ubuntu)
![image](https://github.com/user-attachments/assets/0581d89f-a477-46a3-b8e0-c6aad6308a73)

### DHCP server config file:
![image](https://github.com/user-attachments/assets/be22afdb-f88b-4e99-b3e7-e8c0f585a93d)

```sudo nano /etc/default/isc-dhcp-server```
### : This is for saying the dhcp server will be on the interface "ens37"
![image](https://github.com/user-attachments/assets/39f3b7d5-56b4-4b8c-9fa0-1ae74d5af352)




### client1 (Ubuntu)
![image](https://github.com/user-attachments/assets/2db125d2-bb02-4d3f-9acc-f3bdad8b4807)

![image](https://github.com/user-attachments/assets/465806fd-2aaa-4d90-a71e-842899d99e36)


### client2 (CentOS)
![image](https://github.com/user-attachments/assets/faeae2bb-ba71-4964-9f66-de255adf6337)

![image](https://github.com/user-attachments/assets/8015cad1-0ccd-4603-8d2c-1a2d50d019ab)


# 4. Install and configure DNS server on server1, so Hostnames are resolved to IP Addresses

### This is the sudo nano of /etc/bind/named.conf.options after I have installed bind9 (DNS)
![image](https://github.com/user-attachments/assets/955554c7-d38c-4a64-b14c-cf3d6f471463)

### this is the zone definition of the DNS server under 

```sudo nano /etc/bind/named.conf.local```

![image](https://github.com/user-attachments/assets/29e1b03d-5b1e-4acf-aee8-d1ebbf59f7ca)


### I created the zone directory and file with 
 ```
sudo mkdir -p /etc/bind/zones
sudo nano /etc/bind/zones/db.ddp.is
```

![image](https://github.com/user-attachments/assets/240b3303-3cd5-4b92-a068-de1fff722220)


And with that Clients can ping server1 and eachother and server 1 can ping both clients







![image](https://github.com/user-attachments/assets/b839f944-259f-4222-82d2-6fec58c79993)

# 5. Create the users accounts using a script, see the Users file.

I first moved over the .CSV file to the VMware Linux Server 1 by doing:

```scp /Users/thord/Downloads/Linux_Users.CSV server1@192.168.100.10:/home/server1```

on my own Laptop which would safely copy the file to the directory /home/server1/Linux_Users.CSV

After that I see that when I cat the file I get a bunch of errors due to the File being in UTC-16
I change it to UTC-8 with:

```iconv -f utf-16 -t utf-8 Linux_Users.CSV -o users_clean.csv```

I create a Script with 

```nano ~/create_users.sh```

and use this script code:
```

INPUT="$HOME/users_clean.csv"
IFS=','

while read name firstname lastname username email dept empid; do
    # Skip header line
    if [[ "$username" == "Username" ]]; then
        continue
    fi

    echo "Creating user: $username ($name)"

    # Create user with home directory and full name
    sudo useradd -m -c "$name" "$username"

    # Set default password to "password123"
    echo "$username:password123" | sudo chpasswd

    # Force password change on first login
    sudo chage -d 0 "$username"
done < "$INPUT"
```


After this is done I can confirm that they did infact get created with 

```
ls /home
```

All of their passwords are "password123"

![Skjámynd 2025-05-15 125710](https://github.com/user-attachments/assets/586a6415-9e37-4f17-b08a-d8797561b2f4)


# 6. Install and configure MySQL on server1 and create Human Resource database. The database stores information about employees, employees are identified by their Kennitala, Firstname, Lastname, Email, phone Number, hire date and salary. Employees work in Departments where each department has one manager, departments are identified by department ID, department name. Each department is in a different location. Locations are identified by their location ID, city, Address, and zip code. One or more employees work in different jobs, and the jobs are identified by Job ID, job title, Min salary and max salary.

Installed MySQL with:

```
sudo apt install mysql-server -y
```

then logged into MySQL with

```
sudo mysql -u root -p
```

I created a database named "human_resources

then created a table called "locations" with

```
CREATE TABLE locations (
    location_id INT PRIMARY KEY AUTO_INCREMENT,
    city VARCHAR(100),
    address VARCHAR(255),
    zip_code VARCHAR(20)
);
```

Then created another table called "departments" with

```
CREATE TABLE locations (
    location_id INT PRIMARY KEY AUTO_INCREMENT,
    city VARCHAR(100),
    address VARCHAR(255),
    zip_code VARCHAR(20)
);
```

Then created a table called "jobs" with 

```
CREATE TABLE jobs (
    job_id INT PRIMARY KEY AUTO_INCREMENT,
    job_title VARCHAR(100),
    min_salary DECIMAL(10,2),
    max_salary DECIMAL(10,2)
);
```

Then created a table called "employees" with

```
CREATE TABLE employees (
    kennitala VARCHAR(20) PRIMARY KEY,
    firstname VARCHAR(100),
    lastname VARCHAR(100),
    email VARCHAR(100) UNIQUE,
    phone_number VARCHAR(20),
    hire_date DATE,
    salary DECIMAL(10,2),
    department_id INT,
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);
```

I then created a Manager with

```
INSERT INTO employees (kennitala, firstname, lastname, email, phone_number, hire_date, salary, department_id)
VALUES ('1234567890', 'Andri Mar', 'Fridriksson', 'amf@ddp.is', '555-1234', '2023-01-01', 7000.00, NULL);

INSERT INTO locations (city, address, zip_code)
VALUES ('Reykjavík', 'Hateigsvegur', '101');

INSERT INTO departments (department_name, manager_kennitala, location_id)
VALUES ('IT', '1234567890', 1);

UPDATE employees SET department_id = 1 WHERE kennitala = '1234567890';
```

# 7. Due to data loss the company policy requires backups weekly, assystem engineer you are required to schedule backups of home directories to run weekly at midnight each Friday.

I created a backup directory on server 1 with 

```
sudo mkdir -p /backups
sudo chown root:root /backups
sudo chmod 700 /backups
```

I created a script to backup all files in Home Directory

```
# Set date format and filename
DATE=$(date +%Y-%m-%d)
BACKUP_FILE="/backups/home_backup_$DATE.tar.gz"

# Create the backup
tar -czf "$BACKUP_FILE" /home
```

I schedule it with "cron" with 

```
sudo crontab -e
```

and put this code into it 

```
0 0 * * 5 /usr/local/bin/weekly_home_backup.sh
```

0 0 * * 5 is for midnight on Fridays

and now it backs up files every midnight of Friday

# 8. Install and configure NTP on server1 and clients, server1 must be master server and client must synchronize their time with the server.

I installed NTPSEC on server 1. and went and configured the config file with 

```
sudo nano /etc/ntpsec/ntp.conf
```
and added this to the config file.

```
driftfile /var/lib/ntpsec/ntp.drift

server 127.127.1.0
fudge 127.127.1.0 stratum 10

restrict default kod nomodify nopeer noquery limited
restrict 127.0.0.1
restrict ::1
restrict 192.168.100.0 mask 255.255.255.0 nomodify notrap
```

![image](https://github.com/user-attachments/assets/4b8b551a-9d4e-44c4-accb-e8cf2de3a932)

Then I download crony on both clients

```
sudo apt install crony -y # Ubuntu Client1
sudo yum install crony -y # CentOS Client2
```

After that happens I go into the chrony config file of both clients and add this little code on both of them

```
server 192.168.100.10 iburst

driftfile /var/lib/chrony/chrony.drift
makestep 1.0 3
rtcsync
```

Client2 (CentOS)
```
sudo nano /etc/chrony.conf # For CentOS Client2
```

![image](https://github.com/user-attachments/assets/a29e9956-6b6e-429e-9997-cf92e384ad7d)

![image](https://github.com/user-attachments/assets/98f13bdf-1b61-4b5d-af4f-a921ab5e894e)



Client1 (Ubuntu)
```
sudo nano /etc/chrony/chrony.conf # For Ubuntu Client1
```

![image](https://github.com/user-attachments/assets/acebd34c-6a1c-45e6-8853-2f7c3e3b1836)

![image](https://github.com/user-attachments/assets/679f777f-c622-40a2-9947-014c506abc6a)



# 9. Install and configure syslog server on server1, server1 should get logs from both the clients for proactive management and monitoring.
# 10. Install and configure Postfix on server1, so users can send and receive emails using Round Cube open-source software.

# 11. Install and configure shared printers for each group, only users that belong to the group should print only, accept IT and Management groups should print and manage the printers.

# 12. For security reasons, install and configure SSH on the server and clients, SSH login should use RSA

# 13. All unused ports should be closed, use NMAP for testing.

keys instead of password authentication.
15. All unused ports should be closed, use NMAP for testing.
