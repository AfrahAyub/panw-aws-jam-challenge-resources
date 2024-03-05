The "Hop & Code" owners are convinced that our competitor “Sneaky Suds” is exfiltrating our secret recipes.

Your Application development team found some very strange behaviour on the Beer Database Server and asked you to have a deeper look into it to figure out what's going on.  After some investigations, you realized that no outbound traffic gets analyzed by the Palo Alto Networks Firewall. That's something that we have to fix.

You started the journey by conducting a comprehensive audit of the existing AWS infrastructure. With a discerning eye, you created a detailed diagram of the AWS environment. You mapped out the route tables of every VPC and the Transit Gateway.

<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/detailed-start.png" alt="Full Starting Diagram with Route Tables" width="1500" /></p>
<br />


## Task

**Redirect all outbound traffic from the Beer Store Data Database Server to the Palo Alto Networks Firewall**

1. First, login to the Firewall. (**Helpful Info Section**)

2. Check the Firewall Monitor traffic logs to verify if you can see any traffic from the Beer Store Data Database Server. ((**Helpful Info Section**)

3. Update AWS routing to redirect the Beer Store Data Database Server outbound traffic for inspection by VM-Series through the Transit Gateway. <br />
<br />

## Task Validation

- Once you made the appropriate changes in the AWS routing you can log into the **Beer Store Data Database Server** via the SSM service and test with the **curl** command if the EC2 instance has internet access.
  - example curl command **sudo curl www.google.de** 


- If the curl command isn't working in the **Beer Store Data Database Server**, check the Palo Alto Networks Firewall Monitor Logs to see which Application is now blocked from the Firewall. 

- You should see the following example log in the firewall monitoring. By adding the following filter **( zone.src eq internal ) and ( zone.dst eq external )** into the Monitor Logs filter bar.<br />
   Some fields in the example log were removed.
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task1-deny2.png" alt="VPC Logs" width="1500" /></p>
<br />

- Input the Name of the blocked Application in the answer field to complete the task. <br />
<br />

## Helpful Info
**To Login into the VM Series Firewall Web UI**
- Identify the Elastic IP (Security VM-Series Management) of the EC2 Instance named "Security VM-Series"
- Open a browser window and navigate to https://("Security VM-Series-EIP")
- Login with the following credentials:
  - Username: admin
  - Password: Pal0Alt0@123

**How to see the Traffic Logs inside the Firewall**
- Login into the firewall
- Inside the firewall navigate to Monitor -> Traffic
- See the following picture as an example <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/example.png" alt="Monitor Logs" width="1500" /></p>
- In the Monitor Traffic window change the refresh timer from **Manual** to **10 seconds** by clicking on the dropdown field on the top right as the picture below shows
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task1-10.png" alt="Monitor Logs" width="1500" /></p>


**Login into the Beer Store Data Database Server**
- Use the Session Manager to log into the Server
- The name of the VM is "Beer Store Data Database"

**How to find the server's private IP?**
- On the AWS Console go to EC2
- On the EC2 Dashboard click on Instances
- The following EC2 instances are used by the lab:
  - Beer Store Data Database
  - Beer Store Frontend Webserver
  - Security VM-Series (Palo Alto Networks Firewall)<br />
<br />

## Inventory
- Palo Alto Networks NGFW VM-Series
- Amazon EC2
- Amazon VPC
- AWS Systems Manager (SSM)
- AWS Lambda
- AWS AWS Tranist Gateway
- AWS Gateway Load Balancer <br />
<br />

## Services You Should Use
- Palo Alto Networks NGFW VM-Series
- Amazon EC2
- Amazon VPC (Route tables) <br />

## clues
Clue 1:Did you check the VPC Route Table?
You should check if the Route Tables of the Beer Store Data VPC is pointing to the correct Ressource

Clue 2:Traffic still not showing in the Firewall?
If you still can't see the traffic in the Firewall monitoring. Please do the following:

1. Login into the AWS console
2. Go to VPC
3. Select in Filter by VPC field the "Beer Store Data VPC"
4. As next go to Route Tables and select the Beer Store Data Private route table
5. In the route table click on Routes (see below)
<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task1-routes.png" /></p>
<br />
6. Click Edit routes and do the following changes:

  1. Remove the route 10.0.0.0/8 -> Target TGW
  2.  Change the route 0.0.0.0/0 -> TGW
  3. click Save

7. Once you made the changes your routle should looks like the example below
<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task1-clue2-new.png" /></p>
<br />
Answer:- Look for "web-browsing"
