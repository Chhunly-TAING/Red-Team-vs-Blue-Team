## Red-team vs Blue-team

This is a report of red-team and blue-team whereI play a role as pestester and SOC analyst.

As the Red Team, I will attack a vulnerable VM within my environment, ultimately gaining root access to the machine. As Blue Team, I will use Kibana to review logs taken during Day 1 engagement. I will use the logs to extract hard data and visualizations for their report.

### Network Diagram 

!(Images/Network-diagram.png)

| Name            | IP Address | 
|----------       |---------------------|
| Azura Hypervisor| 192.168.1.1  | 
| Linux           | 192.168.1.8  | 
| ELK server      | 192.168.1.00 | 
| Capstone ( Apache server)| 192.168.1.105 |  

### Step 1

- For these step we will play a role as Red-team.

- Using Nmap scan for webserver's IP Address as we already known we are on the same  subnet.

!(Images/Port 80 open.png)

- The results shows that port 80 TCP was open, port 80 is a vulnerable port because it suppports web traffics that web browsers receive, SQL injection, cross-site request and cross-stie scripting.

- Once I got a website IP a did a bit of reconnaissance by go through a company folders.

!(Images/sensitive data1.png)
!(Images/sensitive data2.png)
!(Images/sensitive data3.png)

- I found so many useful information to determine my target which is Ashton because he is the one that can access to the secret folder of comapny where I might find something more useful.

- In order to get access I need to brute force Ashton user by using Hydra. 

!(Images/hydra command.png)
!(Images/Ashton passwed cracked.png)

- Now once I got Ashton's user password I log in as Ashton to the secret folder.

!(Images/secret_folder accessed.png)

- I found a hash value of Ryan which is CEO of the company where I will use his credentials to upload a payload to the website later.

- For this bit I used msfvenom to create a payload called TCP reverse shell.

!(Images/how to create payload.png)

- Once the payload created I uploaded it to website and exploit it using msfconsole.

!(Images/upload payload.png)
!(Images/exploiting.png)
!(Images/exploiting2.png)

- Using cat to view the flag folder then finish the step.

!(Images/Capture the flag.png)

- For the 1 step is now completed.

### Step 2

- For these step we will working as Blue-team looking through logs to determine all of those attack that gone throught the web server.

- Using Kibana to determine the logs and packetbeat to analys it we will see later what is next.

- To determine port scan.
- Search for `source.ip:192.168.1.8 and destination.ip:192.168.1.105`.

!(Images/blue-team port_scan.png)

- To determine brute force attack.
- Search for ` source.ip:192.168.1.8 and http.response.status_code:401 and user_agent.original:"Mozilla/4.0 (Hydra)"`

!(Images/blue-team brute_force.png)

- Add those value to the dashboard.

!(Images/blue-team brute_force1.png)

- To determine how many times they got access to secret folder.
- Search for ` destination.ip:192.168.1.105 and url.path:"/company_folders/secret_folder"`

!(Images/blue-team secret_folder.png)

- Add those value to the dashboard.

!(Images/blue-team secret_folder1.png)

 - To determine how many times they got access to webdav.

- Search for `source.ip:192.168.1.8 and destination.ip:192.168.1.105 and url.path:"/webdav"`

!(Images/blue-team webdav.png)

- For further more please refer to my pdf where I attached it with my work.


