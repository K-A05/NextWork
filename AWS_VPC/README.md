<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Virtual Private Cloud

**Project Link:** [View Project](https://nextwork.ai/projects/7f907ac7-2473-5c15-a8be-edb36581cdba)

**Author:** Karan  
**Email:** karan.anand511@gmail.com

---

![Image](https://nextwork.ai/nostalgic_orange_serene_oriental_melon/uploads/7f907ac7-2473-5c15-a8be-edb36581cdba_2facf927)

## Introducing Today's Project!

In this project, we'll go step by step to make an Amazon Virtual Private Cloud, create a public subnet and then connect that subnet to the internet.

After completing those steps we should be able to access a virtual private cloud over the internet that will allow us to be able to make use of the AWS cloud by having a private partion on it that we can use to launch and maintain resources.

## Virtual Private Clouds (VPCs)

### What I did in this step

First, we access the AWS Virtual Private Cloud (VPC) console in order to be able to create our subnet that will become our private cloud.



### How VPCs work

VPC are section of the cloud we can create to host our own data and application logic privately.

### Why there is a default VPC in AWS accounts

The default VPC that comes with every new amazon account allows for launching certain services such as Lambda or S3 without the need for setting up a VPC from scratch, which would entail learning how to do so.

![Image](https://nextwork.ai/nostalgic_orange_serene_oriental_melon/uploads/7f907ac7-2473-5c15-a8be-edb36581cdba_2facf927)

### Defining IPv4 CIDR blocks

CIDR is a technique of section out portions of a network of IP addresses, with each section being it's own block.

The notation 10.0.0.0/16 simply mean up to 16 bits of the addresable space is variable, with the other 16 being static, i.e. 10.0.x.x, with the last two being able to take on any value between 0-255.

IPv4 addresses are made up 4 octets of 8 bits, or 4 bytes in total. CIDR can be used to section anywhere from 0-32 of the total bits that can make up an address, but using multiples of 8 gives us clean splits of addresses between different values for each octet in the address.

## Subnets

### What I did in this step

After creating the VPC, we're now gonna divide the network into subnets. This will allow us to group resources along thier access needs and resources.

Subnets in the same VPC cannot have overlapping  IPv4 CIDR blocks.

Hence each VPC needs a unique range of IP addresses.



### Creating and configuring subnets

Subnets are different sections within the network, these sections are further categorised along the Availability Zone that is local to the area we're in. AZs are just clusters of servers that are dotted around different regions. 

### Public vs private subnets

The word "public" in our subnet denotes that the subnet is connected to the internet. a private subnet on the other hand has no direct access to the internet.  

The new subnet is not public yet because it has no direct connection to the internet which is what we will setup in the next step.

![Image](https://nextwork.ai/nostalgic_orange_serene_oriental_melon/uploads/7f907ac7-2473-5c15-a8be-edb36581cdba_157c4219)

### Auto-assigning public IPv4 addresses

In order to connect our subnet to the broader internet we need to provision it with a public IP address, by enabling auto-assigining for IP addresses,  an EC2 instance launched within the subnet will automatically get an IPv4 address.

## Internet gateways

### What I did in this step

Now that we have our VPC and our subnet, we're ready to connect our subnet to the internet to have a channel to access the internet over the VPC.

### Setting up internet gateways

Internet gateways allow us to connect our subnets to the internet.

Doing this will let the VPC access the public internet.

![Image](https://nextwork.ai/nostalgic_orange_serene_oriental_melon/uploads/7f907ac7-2473-5c15-a8be-edb36581cdba_4ae90410)

## Using the AWS CLI

### What I'm doing in this extension

Now, after having setup the VPC and the subnets and connecting it to the internet, we're gonna be making use of the AWS cloudshell in order to do the same things over the command line.

### Exploring CloudShell and CLI

The CloudShell is the shell that is present in the management console and allows for the execution of code in a cloud environment, coming pre-installed with the AWS-CLI software; this software allows us to create, update and delete AWS services with the use of commands.

The CLI is especialy valuable for the creation and management/automation of cloud based services.

### Debugging my setup

to setup a VPC/subnet we need to make use of the following command:
`aws ec2 create-subnet --vpc-id VPC-ID --cidr-block 10.0.0.0/28`

We need to make sure the add the CIDR block and have the block in the range of addresses that the VPC is made on, since the VPC was made on 10.0.0.0/24, the range of the subnet has to be within 25 >= n <= 32.

Without specifying this we run into an error.

![Image](https://nextwork.ai/nostalgic_orange_serene_oriental_melon/uploads/7f907ac7-2473-5c15-a8be-edb36581cdba_9b2465411)

### Comparing CloudShell vs AWS Console

Compared to setting it up in the console, the CLI is a much more seamless process with a lot less time spent on scanning options in the gui, although the final presentation of the changes can be out of sync with whats internally set up, in my case the gateway was successfully attached, as per the output in the terminal but wouldn't show up in the resource maps for my subnets.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/7f907ac7-2473-5c15-a8be-edb36581cdba)*
