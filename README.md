<div align="left"> 
 
# Internee.pk Cloud Monitoring and Alerting

In this project, I deployed the Internee.pk website on an AWS EC2 instance and configured comprehensive monitoring and alerting using Amazon CloudWatch and AWS SNS. The key achievements include:

1. Hosted the Internee.pk web application on an Ubuntu EC2 instance.
2. Enabled detailed CloudWatch monitoring for the instance.
3. Configured critical metrics: CPUUtilization, NetworkIn, NetworkOut, StatusCheckFailed, StatusCheckFailed\_Instance, and StatusCheckFailed\_System.
4. Created CloudWatch alarms that send notifications via AWS SNS when CPU utilization exceeded 50%.

---

## Step 1: Hosted the Internee.pk Website on EC2

* **Launched an EC2 Instance**

  * Selected an Ubuntu Server AMI and a t2.micro instance.
  * Configured security groups to allow HTTP (port 80) and SSH (port 22).
  * Attached an IAM role (`EC2CloudWatchAgentRole`) with the `CloudWatchAgentServerPolicy` policy.

* **Deployed the Web Application**

  * Connected via SSH:

    ```bash
    ssh -i your-key.pem ubuntu@<13.203.230.193>
    ```
  * Installed and configured Apache:

    ```bash
    sudo apt update
    sudo apt install apache2 -y
    ```
  * Uploaded application files to `/var/www/html`.

---

## Step 2: Enabled CloudWatch Monitoring for EC2

* **Activated Detailed Monitoring**

  * In the EC2 Console, enabled 1-minute interval monitoring for the instance under the **Monitoring** tab.

* **Installed and Configured the CloudWatch Agent**

  * Downloaded and installed the agent on Ubuntu:

    ```bash
    wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
    sudo dpkg -i amazon-cloudwatch-agent.deb
    ```
  * Created the agent configuration via the CloudWatch Console wizard and stored it in SSM Parameter Store.
  * Deployed the configuration and started the agent:

    ```bash
    sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
      -a fetch-config -m ec2 -c ssm:/AmazonCloudWatch-linux-config -s
    ```

---

## Step 3: Configured Key Metrics

In the CloudWatch Console under **Metrics → EC2 → Per-Instance Metrics**, I selected:

* **CPUUtilization**: Percent of CPU in use.
* **NetworkIn**: Bytes received by the instance.
* **NetworkOut**: Bytes sent by the instance.
* **StatusCheckFailed**: Combined system and instance status checks.
* **StatusCheckFailed\_Instance**: Instance-level status checks (software/OS).
* **StatusCheckFailed\_System**: System-level status checks (hardware/network).

These metrics were added to a CloudWatch dashboard for real-time visualization.

---

## Step 4: Set Up Alarms and Notifications

* **Created an SNS Topic**

  * In the SNS Console, created `internee-alerts` and subscribed my email address.

* **Defined a CloudWatch Alarm**

  * Selected the **CPUUtilization** metric for the EC2 instance.
  * Set the threshold:

    * **Condition:** Average CPU utilization > 50%
    * **Period:** 5 minutes
  * Configured the alarm to publish to the `internee-alerts` SNS topic when in the `ALARM` state.

* **Verified Notifications**

  * Simulated high CPU load and confirmed receipt of the SNS email alert.

---  </div>



