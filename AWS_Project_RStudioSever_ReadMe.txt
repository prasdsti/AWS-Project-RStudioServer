#awsproject2
For more details about how to use R with AWS goto: https://aws.amazon.com/fr/blogs/big-data/running-r-on-aws/ 

------ Creating Key Pairs -----
1/ Create your key pairs if you don't have any:
	a/ Go EC2
	b/ On left pan: "Key Pairs"
	c/ Create Key Pair
	d/ Enter the name you want then create


------ Elastic IPs -----
2/ Create 2 Elastic IPs, one for the NAT Gateway and one for the WEBSERVER
	a/ Go to "Service"
	b/ In left pan select "Elastic IPs"
	c/ Select "Allocate new address" (in new opened window, just choose "Allocate" then "close")


------ Create your VPC ---------	
3/ In the AWS Management Console, on the Services menu, click VPC.
4/ Click Start VPC Wizard.
5/ In the navigation pane, click VPC with Public and Private Subnets.
6/ Click Select.
7/ Configure the following settings (and ignore any settings that aren't listed):
	a/ IPV4CIDR block: Type: 10. 0. 0. 0/16
	b/ VPC name: "VPC_RStudioServer"
	c/ Public subnet's IPv4 CIDR: Type 10. 0. 1. 0/24 
	d/ Availability Zone: Click the first Availability Zone.
	e/ Public subnet name: type "VPC_RStudioServer public Subnet"
	f/ Specify the details of your NAT gateway: "Choose one of the Elastic IP previously defined"
8/	Click Create VPC.
9/	In the success message, click OK.


------- Create Security Group for public subnet -----
10/ In the navigation pane, click Security Groups.
11/ Click Create Security Group.
12/ In the Create Security Group dialog box, configure the following settings (and ignore
	any settings that aren't listed)
	a/ Name tag: "VPC_RStudioServer public security group"
	b/ Group name: "VPC_RStudioServer public security group"
	c/ Description: "VPC_RStudioServer public security group"
	d/ VPC: Click "VPC_RStudioServer" 
13/ Click Yes, Create.
14/ Select "VPC_RStudioServer public security group".
15/ Click the Inbound Rules tab.
16/ Click Edit.
17/ Click Add.
18/ For Type, click HTTP (80)
19/ Click in the Source "Anywhere"
20/ Click Add again.
21/ For Type, click HTTPS (443)
22/ Click in the Source "Anywhere"
23/ Click Add again.
24/ For Type, click SSH (22)
25/ Click in the Source "Anywhere"
26/ Click Add again.
27/ For Type, click CUSTOM (8787) => Port 8787 for RStudio-Server
28/ Click in the Source "Anywhere"
29/ Click Add again.
30/ For Type, click CUSTOM (3838) => Port 3838 for Shiny-Server
31/ Click in the Source "Anywhere"
32/ Click Save.


------- Create WEBSERVER Instance ------
33/ On the Services menu, click EC2.
34/ Click Launch Instance.
35/ In the row for Amazon Linux AM
36/ On the Step 2: Choose an Instance Type page, select the correct type of instance depending of how you would like to use R (Daily basis? Huge amount of data to analyse? etc ...) 
37/ On the Step 3: Configure Instance Details page, configure the following settings (and ignore any settings that aren't listed)
	a/ Network: "VPC_RStudioServer"
	b/ Subnet: "VPC_RStudioServer public Subnet"
	c/ Expand "Advanced Details" and add in "User Data" the following lines to install RStudio-Server (change "username" and "password" in the following lines):
	
	
		#!/bin/bash
		#install R
		sudo yum update -y
		sudo yum install -y R

		#install RStudio-Server 1.0.153 (2017-07-20)
		sudo wget https://download2.rstudio.org/rstudio-server-rhel-1.0.153-x86_64.rpm
		sudo yum install -y --nogpgcheck rstudio-server-rhel-1.0.153-x86_64.rpm
		sudo rm rstudio-server-rhel-1.0.153-x86_64.rpm

		#install shiny and shiny-server (2017-08-25)
		sudo R -e "install.packages('shiny', repos='http://cran.rstudio.com/')"
		sudo wget https://download3.rstudio.org/centos5.9/x86_64/shiny-server-1.5.4.869-rh5-x86_64.rpm
		sudo yum install -y --nogpgcheck shiny-server-1.5.4.869-rh5-x86_64.rpm
		sudo rm shiny-server-1.5.4.869-rh5-x86_64.rpm

		#add user(s)
		sudo useradd username
		sudo echo username:password | sudo chpasswd
		
		
		
38/ Click Next: Add Storage (select enough space for data you would like to process).
39/ Click Next: Add Tags.
40/ Click Add Tag, and configure the following settings (and ignore any settings that aren't listed):
	a/ Key: "Name"
	b/ Value: "VPC_RStudioServer_AMI"
41/ Click Next: Configure Security Group.
42/ On the Step 6: Configure Security Group page, click Select an existing security group, and then select the security group you created previously "VPC_RStudioServer public security group"

43/ Review and launch instance
44/ Go to instance list and wait until status pass 2/2
45/ Once instance is launched look in bottom of the page to copy the DNS (or the public IP) and pass in new brower, and add the port 8787 at the end. Your address in the browser has to be something like that: 
	http://ec2-34-245-238-119.eu-west-1.compute.amazonaws.com:8787
46/ Then enter your username and password you have defined during installation, and you are logged in to your RStudio Server!