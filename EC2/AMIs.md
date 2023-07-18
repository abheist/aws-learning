Amazon Machine Image
- AMI are a customization of an EC2 instance
	- You add your own software, configuration, operating system, monitoring...
	- Faster boot / configuration time because all your software is pre-packaged.
- AMI are built for specific region (and can be copied across regions)
- You can launch EC2 instances from:
	- A public AMI: AWS provided
	- Your own AMI: you make and maintain them yourself
	- An AWS marketplace AMI: an AMI someone else made (and potentially sells)
- AMI process (from an EC2 instance)
	- Start an EC2 instance and customize it
	- Stop the instance (for data integrity)
	- Build an AMI - this will also create EBS snapshots
	- Launch instances from other AMIs ![Screenshot 2023-06-06 at 8.40.13 PM](../images%201/Screenshot%202023-06-06%20at%208.40.13%20PM.png)
- AMI hands-on
	- Go to instance page
	- Create new instance
	- Fill in the details you need for the AMI, just to test this functionality, add the following in user data
```sh
 #!/bin.bash
 # use this for your user data (script from top to bottom)
 # install httpd (linux 2 version)
 yum update -y
 yum install -y httpd
 systemctl start httpd
 systemctl enable httpd
```
- Once filled, click on `Launch Instance` button, it'll create an Instance and you can go to view all instances page.
- Give some time to instance get it initiated and run the script we provided in `User data` section, it'll take a minute or two, once it's running.
- You can copy the public IP and paste it in the new tab, it'll open a default server Apache index page
- Once you see the page, you are good to go.
- To create an AMI, right click on the instance we recently created
- in context menu, go to `Image and templates` and click on `Create an Image`
- It'll open up a form
	- Instance ID
	- Image name
	- Image description
	- No reboot
	- Instance volumes, these are volume which was with instance, you can add more volumes by clicking on `Add volume` button
	- Add tags and fill in all the necessary information
	- Click on `Create Image`
	- AMI will be created
- You can see the created AMI by going to the AMI page from EC2 dashboard sidebar under Images
- On AMI pages, there will be a list of AMIs, you can select the AMI and find all the AMI details
	- AMI ID
	- image type
	- Platform type
	- Root device type
	- AMI name
	- Owner account ID
	- Architecture
	- Status, etc
- Launch instances from AMI
	- Go to AMI page, select the AMI, click on `Launch Instance from AMI` button at the top right corner
	- or you can go to instances page, click on `Launch Instances` button at the top-right corner, once the form opens up, you can select you AMI under My AMIs under AMI selection section.
	- Fill other necessary details, etc
	- Add following under `User data` just for this testing purpose
```sh
	#!/bin/bash
	echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
```
- once filled, click on `Launch Instance` button
- Once instance is launch, you can copy the public IP address and paste it in new tab
- You can see the hello world message
- You'll see that this process is faster since it just copied our pre-made AMI image.

