## Red-team vs Blue-team

This is a report of red-team and blue-team whereI play a role as pestester and SOC analyst.

As the Red Team, I will attack a vulnerable VM within my environment, ultimately gaining root access to the machine. As Blue Team, I will use Kibana to review logs taken during Day 1 engagement. I will use the logs to extract hard data and visualizations for their report.

### Network Diagram 

<img width="545" alt="Network-diagram" src="https://user-images.githubusercontent.com/80186549/116966358-4d5e7b00-acf3-11eb-90bc-357e24987acd.PNG">

| Name            | IP Address | 
|----------       |---------------------|
| Azura Hypervisor| 192.168.1.1  | 
| Linux           | 192.168.1.8  | 
| ELK server      | 192.168.1.00 | 
| Capstone ( Apache server)| 192.168.1.105 |  

### Step 1

- For these step we will play a role as Red-team.

- Using Nmap scan for webserver's IP Address as we already known we are on the same  subnet.

<img width="858" alt="Port 80 open" src="https://user-images.githubusercontent.com/80186549/116966425-8139a080-acf3-11eb-9370-56893c3883b9.PNG">

- The results shows that port 80 TCP was open, port 80 is a vulnerable port because it suppports web traffics that web browsers receive, SQL injection, cross-site request and cross-stie scripting.

- Once I got a website IP a did a bit of reconnaissance by go through a company folders.

<img width="951" alt="sensitive data1" src="https://user-images.githubusercontent.com/80186549/116966447-8eef2600-acf3-11eb-9ce1-2fac12947dd4.PNG">
<img width="957" alt="sensitive data2" src="https://user-images.githubusercontent.com/80186549/116966463-97476100-acf3-11eb-8116-6801295c7538.PNG">
<img width="973" alt="sensitive data3" src="https://user-images.githubusercontent.com/80186549/116966518-b6de8980-acf3-11eb-99f2-b2bc0f185e9b.PNG">

- I found so many useful information to determine my target which is Ashton because he is the one that can access to the secret folder of comapny where I might find something more useful.

- In order to get access I need to brute force Ashton user by using Hydra. 

<img width="587" alt="hydra command" src="https://user-images.githubusercontent.com/80186549/116966543-c2ca4b80-acf3-11eb-9d68-6502723a5e9e.PNG">
<img width="601" alt="Ashton passwed cracked" src="https://user-images.githubusercontent.com/80186549/116966560-ccec4a00-acf3-11eb-9790-9942b0c262f3.PNG">

- Now once I got Ashton's user password I log in as Ashton to the secret folder.

<img width="877" alt="secret_folder accessed" src="https://user-images.githubusercontent.com/80186549/116966570-d970a280-acf3-11eb-9687-ab4eb5531db4.PNG">

- I found a hash value of Ryan which is CEO of the company where I will use his credentials to upload a payload to the website later.

- For this bit I used msfvenom to create a payload called TCP reverse shell.

<img width="790" alt="how to create payload" src="https://user-images.githubusercontent.com/80186549/116966591-e4c3ce00-acf3-11eb-9793-50d6502ed6ed.PNG">

- Once the payload created I uploaded it to website and exploit it using msfconsole.

<img width="736" alt="upload payload" src="https://user-images.githubusercontent.com/80186549/116966606-eee5cc80-acf3-11eb-90ac-342a1704e172.PNG">
<img width="773" alt="exploiting" src="https://user-images.githubusercontent.com/80186549/116966624-f73e0780-acf3-11eb-87da-c67e2e0c6751.PNG">
<img width="755" alt="exploiting2" src="https://user-images.githubusercontent.com/80186549/116966648-002ed900-acf4-11eb-8cd4-eacb81396b1f.PNG">

- Using cat to view the flag folder then finish the step.

<img width="894" alt="Capture the flag" src="https://user-images.githubusercontent.com/80186549/116966677-0de45e80-acf4-11eb-938b-374cb55ddc35.PNG">

- For the 1 step is now completed.

### Step 2

- For these step we will working as Blue-team looking through logs to determine all of those attack that gone throught the web server.

- Using Kibana to determine the logs and packetbeat to analys it we will see later what is next.

- To determine port scan.
- Search for `source.ip:192.168.1.8 and destination.ip:192.168.1.105`.

<img width="957" alt="blue-team port_scan" src="https://user-images.githubusercontent.com/80186549/116966754-3e2bfd00-acf4-11eb-9a35-6475ea4b6256.PNG">

- To determine brute force attack.
- Search for ` source.ip:192.168.1.8 and http.response.status_code:401 and user_agent.original:"Mozilla/4.0 (Hydra)"`

<img width="946" alt="blue-team brute_force" src="https://user-images.githubusercontent.com/80186549/116966777-4a17bf00-acf4-11eb-87e9-493419288ae0.PNG">

- Add those value to the dashboard.

![blue-team brute_force1](https://user-images.githubusercontent.com/80186549/116966810-5439bd80-acf4-11eb-8678-4204771e355f.PNG)

- To determine how many times they got access to secret folder.
- Search for ` destination.ip:192.168.1.105 and url.path:"/company_folders/secret_folder"`

<img width="906" alt="blue-team secret_folder" src="https://user-images.githubusercontent.com/80186549/116966878-73384f80-acf4-11eb-8238-eab10e9ae6c7.PNG">

- Add those value to the dashboard.

<img width="483" alt="blue-team secret_folder1" src="https://user-images.githubusercontent.com/80186549/116966892-7cc1b780-acf4-11eb-9371-39d46c31176e.PNG">

 - To determine how many times they got access to webdav.

- Search for `source.ip:192.168.1.8 and destination.ip:192.168.1.105 and url.path:"/webdav"`

<img width="951" alt="blue-team webdav" src="https://user-images.githubusercontent.com/80186549/116966912-877c4c80-acf4-11eb-8f6d-14b3dd169a32.PNG">

- For further more please refer to my [pdf](https://github.com/Chhunly-TAING/Red-Team-vs-Blue-Team/files/6419305/_Project.2.Template.Chhunly.TAING.pdf).
