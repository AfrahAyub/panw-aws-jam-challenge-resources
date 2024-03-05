## Background

Well done on stopping the lateral propagation between the Beer Frontend Store and our Beer Store Data Database! Time to unmask how the Sneaky Suds exploited our Storefront.
We have to figure out how they could get into our Beer Frontend Server. For that, we have to make some more changes to our AWS Infrastructure. Below are the updated AWS Network Diagram after making the previous changes

<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/after-task-2.png" alt="Network Diagram2" width="1500" /></p>
<br />
<br />


## Task
1. You realized that you have no Inbound inspection on the Beer Store by looking into the Firewall monitor logs and adding the following filter  **(( zone.src eq frontend ) and ( zone.dst eq frontend )) or (( zone.src eq external ) and ( zone.dst eq internal ))**. 
2. You should now redirect the traffic from the Beer Frontend VPC to the Firewall. For help see Clue 1
3. Now after you redirect inbound traffic through the Firewall, you should connect to the **Beer Store Frontend Webserver** (HTTP) over the Public IP. You should be able to see the following Webpage. This is the entrypoint to the Log4j Attack. The attack is being automatically generated, you do not need to authenticate to the beer store frontend before proceeding to the next step
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/beerstore.png" alt="Beer Store" width="500" /></p>
   In the Firewall Monitor Logs, you should see the following log by entering the Filter ( addr.src in YOUR-PIP ). Replace "YOUR-PIP" with your local Public IP (Logs can take up to 1 min to be shown in the Monitor) <br />
   In case you still don't see any traffic logs, check the Internet Edge route table or check Clue 2
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task3-logs.png" alt="Logs" width="1000" /></p>
4. Next we will check the Firewall Monitor Threat logs, to see if any unexpected behaviour is happening. In the Threat Logs, you can see some **Log4j** Attacks. But for some reason, the Firewall isn't blocking them, just alerting. See the picture below as an example.
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/alert-log4j-new.png" alt="alert" width="1000" /></p>
5. Now you have to change/update the Threat Profile on the Security Policy to block/reset the vulnerable traffic. To perform the change on the Security Policy follow the instructions below:
   1. On the Firewall tab on the browser, navigate to the Policies tab, and select Secuirty on the left pane
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol1.png" width="1000" /></p>
   2. Now you can see all the Security rules. Click on the Rule **Allow inbound** frontend rule, and a new window will open
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol2.png" width="1000" /></p>
   3. On the new window click on **Actions** tab
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol3.png" width="500" /></p>
   4. On the **Profile Setting** section you can see that under **Vulnerability Protection**,the **alert** profile is selected. That profile will only alert and not block or reset any communication.
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol4.png" width="500" /></p>
   5. Change the **Vulnerability Protection** from **alert** to **strict**.
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol5.png" width="500" /></p>
   6. Click **OK**, and the window will close automatically.
   7. Next, we have to commit the changes you made to the firewall. Click on the **Commit** button in the top right corner.
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol6.png" width="500" /></p>
   8. A new window will open. Here you will have to click on **Commit** button
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol7.png" width="500" /></p>
   9. Wait for Status Complete and Result Successful and close the Window
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol8.png" width="500" /></p>
6. After changing the **Vulnerability Protection** Profile from **alert** to **strict** you should see the following logs under the **Threat section** of the logs
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/blocked-log4j-new.png" width="1000" /></p>
<br />
<br /> 

## Task Validation
- After you make the appropriate changes in AWS routing and on the Palo Alto Networks Firewall  it should have successfully blocked the attack to the **Beer Store Frontend Webserver** and you should be able to see a **reset-both** log entry in the Palo Alto Networks Monitor Logs -> Threat.
- If yes, type the Threat Name/ID of the Log4j Attack in the answer field (The answer is case sensitive).
<br />
<br />

### Helpful info
How to see the Threat Logs inside the Firewall
- Log into the firewall
- Inside the Firewall Navigate to Monitor -> Threat
- See the following picture as an example <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/threat.png" alt="Monitor Threat Logs" width="1000" /></p>
<br />

## Inventory
- Palo Alto Networks NGFW VM-Series
- Amazon EC2
- Amazon VPC
- AWS Systems Manager (SSM)
- AWS Lambda
- AWS Tranist Gateway
- AWS Gateway Load Balancer <br />
<br />

## Services You Should Use
- Palo Alto Networks NGFW VM-Series
- Amazon EC2
- Amazon VPC (Route tables / Internet Gateway)
- AWS Gateway Load Balancer (endpoints) <br />

## Clues
Clue 1:Did you checked the AWS route table?
1. Login into the AWS console
2. Go to VPC
3. Select in Filter by VPC field the "Beer Store Frontend VPC"
4. As next go to Route Tables and select the Beer Store Frontend Public route table
5. In the route table click on Routes (see below)
<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task3-clue1-pup-rt.png" /></p>
<br />

6. Click Edit routes and do the following change:
 1. Change the route 0.0.0.0/0 -> Gateway Load Balancer Endpoint
 2. click Save

7. Once you made the changes your routle should looks like the example below
<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task3-clue1.png" /></p>
<br />

Clue 2:Did you checked the Internet Route Table?
1. Login into the AWS console
2. Go to VPC
3. Select in Filter by VPC field the "Beer Store Frontend VPC"
4. As next go to Route Tables and select the Beer Store Frontend IGW Edge route table
5. In the route table click on Routes (see below)
<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task3-clue2-igw-rt.png" /></p>
<br />

6. Click Edit routes and do the following change:
  - Add the route 10.1.2.0/24 -> Gateway Load Balancer Endpoint
<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task3-clue2-igw-rt2.png" /></p>
<br />

  - click Save

7. Once you made the changes your routle should looks like the example below
<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task3-clue2.png" /></p>
<br />

Answer: Apache Log4j Remote Code Execution Vulnerability
