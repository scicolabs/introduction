# Introduction to Research Computing on AWS
## Introduction

In this lab, you will be introduced to a number of basic tools required to get started with Research Computing on AWS. A key ingredient for any research work is data. We’ll show you how to move data into and out of the AWS cloud efficiently and securely using both an example desktop application, as well as the AWS Python SDK and the AWS CLI tool and Amazon S3.

When it comes to processing and analyzing that data, AWS offers a massive array of tools and platforms you might be interested in; from small to very large computers, powerful virtual scientific workstations with GPUs for data visualisation, big data tools like Hadoop and Spark, relational databases like Postgres (with PostGIS for geospatial work), and even your own personal HPC clusters in the cloud.

We’ll start at the beginning though. After moving data into S3, we’ll show you how to start your own powerful cloud compute environment using a AWS partner solution called Alces Flight. To start with, this will give you a very capable Linux server environment in the cloud, with lots of software at your fingertips ready to install. We’ll install Jupyter (also known as IPython), and some scientific computing libraries and work with data in one of our Open Data collections, NEXRAD from NOAA. Once we’ve done that though, we'll also show you how easy it is to get a remote desktop session to your compute environment in case you want to install desktop tools.

Alces Flight is much more powerful than this though. I can be extended to become your very own personal HPC cluster. Showing you how to do this is beyond the scope of this introductory course, but we have other courses that take you through how to use Alces Flight to do large scale high performance or high throughput computing.

The great thing about AWS is you can start simply and with a small amount of resources, but quickly scale that up to some of the largest infrastructure in the world if you need it. You get to decide what you need, and you have complete control over what scale of resources you use. We think you’ll be pleasantly surprised at how easy it is to get started doing meaningful research work on the cloud!

## Technical Knowledge Prerequisites

To successfully complete this lab, you should be familiar with the following:

- Familiarity with an SSH client for your desktop operating system (e.g. PuTTY or a native SSH client)
- Basic familiarity with SSH and Linux
- Linux command line familiarity – copy files/data
- Some programming knowledge (Python preferred)
- Linux desktop (e.g. Gnome) experience not necessary, but helpful

## Topics Covered

This lab will take you through:

- Using client side tools to move data into an out of Amazon S3
- Creating a compute environment on AWS using Alces Flight
- Connecting to our remote compute environment using SSH
- Starting and using a Jupyter Notebook from our Alces Flight environment
	- Using this Jupyter environment to interact with and process some sample NEXRAD data from NOAA.
- Starting a remote VNC session on our compute environment and connecting remotely to a Linux desktop environment

## Sign in to the AWS Management Console

Using your AWS account to sign in to the AWS Management Console
Welcome to this self-paced lab! The first step is for you to sign in to Amazon Web Services.

1. In this lab we are going to be using an existing AWS account. Prior to the lab you will have been given an IAM user with a username and password, and a URL to the AWS IAM console login screen. Browse to that URL and use the username and password credentials to login into the AWS console.  
  
  ![AWS Console Sign-in](images/aws-iam-signin.png)

2. **AWS Region** – all the work you do today will be in a single AWS region. Please check with the lab instructor which AWS region you should use.

## Creating an Amazon EC2 Key Pair

1. Browse to the EC2 console
2. In the left hand navigation menu, under the ‘Network & Security’ section, click **Key Pairs**
3. Click the **Create Key Pair** button
4. Give your Key Pair a unique name, e.g. **lab61**
5. Click the **Create** button

The EC2 console will now download the private key for your newly created key pair. If you're using Linux or OS X, we'll need to update the permissions on this file. To do this, type:

    chmod 0400 lab61.pem

If you're using Windows, you don't need to do this step.

Don’t lose this and store it in a safe place! It effectively authenticates you when using AWS programmatically. You don’t want someone else impersonating you or using your credentials. We’ll be using this private key later in the lab.

# Module 1 - Moving research data into and out of Amazon S3

Very often you'll want to move data efficiently into the cloud so you can work on it.

There are a number of ways of doing this. One common way of moving data around quickly and securely is to use the [AWS CLI](https://aws.amazon.com/cli/). There are other graphical user interface tools that make it easy to move data into and out of AWS too, like [Cyberduck](https://cyberduck.io/). This is what we'll be using today.

## Installing Cyberduck

Go to the [Cyberduck website](https://cyberduck.io/), download the client for your operating system (Windows or OS X), and install it. Once installed, run the program. You should see something similar to this:

![Cyberduck](images/cyberduck.png)

What we need to do is configure our AWS credentials to securely authenticate and connect to the S3 service. This will let us view buckets, and upload data. We've already created an S3 bucket for you called **kzn-lab** and we'll be using that today as the target we'll upload data to and from.

To do this, we'll need to create a new connection to S3. In your Cyberduck application, click **Open Connection**. You should see something like the following:

![Cyberduck - Open connection](images/cyberduck-open-connection.png)

Here we'll need our **AWS Access Key ID** and **AWS Secret Access Key ID** for the lab user we're using. You'll get this from the lab environment details page for the user you logged into the AWS lab account with.

Take your **Access Key ID** and your **Secret Access Key ID** and copy/paste them into Cyberduck. Once done, click the **Connect** button.

If you were successful, you should see one or more S3 buckets. One of which should be called **kzn-lab**:

![Cyberduck - Browsing S3 buckets](images/cyberduck-buckets.png)

We'll use the **kzn-lab** bucket to move data into and out of the cloud.

## Uploading data

To upload data, double click on the **kzn-lab** bucket and make sure you've navigated to it. You should see the contents of the bucket now (either empty or with some data in it from other users).

From Cyberduck again, click the **Actions** dropdown. You should see the following actions:

![Cyberduck - Actions menu](images/cyberduck-actions.png)

Click **upload** and browse to a local file on your laptop you'd like to upload. Bear in mind, this is shared bucket. Everyone in the lab account has access to it. Within your own AWS account you will be the only person who can see and share data (by default), unless you choose to open up access to other users. How to do this securely is beyond the scope of this lab, but if you'd like to learn more about securely sharing data in Amazon S3 have a look at [Managing Access Permissions to Your Amazon S3 Resources](http://docs.aws.amazon.com/AmazonS3/latest/dev/s3-access-control.html).

Once you've started uploading data, Cyberduck will give you a nice indication of the progress of your upload:

![Cyberduck - Uploading](images/cyberduck-uploading.png)

Once your upload is complete, you should see:

![Cyberduck - Upload complete](images/cyberduck-upload-complete.png)

## A few words on Amazon S3

It's worth making a few comments about Amazon S3. S3 is designed to provide 99.999999999% durability and 99.99% availability. In the Sydney AWS region, it costs roughly 2.5c per GB per month for data stored there. It

There are also different storage tiers which are even more cost effective. To learn more, visit [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/).

Some of the largest research datasets in the world are stored in S3 and accessed incredibly frequently and at massive scale by researchers all around the globe. For example, all of our [AWS Open Data](https://aws.amazon.com/opendata) datasets are stored on S3. Some of the more interesting datasets you can use for free are available at [Earth on AWS](https://aws.amazon.com/earth/). In fact, one of the datasets we'll be using today is the [NEXRAD on AWS](https://aws.amazon.com/public-datasets/nexrad/) dataset from the US National Oceanic and Atmospheric Administration (NOAA).

# Module 2 – Creating your first compute environment

In this module we'll create our first compute environment on Amazon EC2. We'll use a tool called [Alces Flight](https://alces-flight.com) to get started quickly. Alces Flight is available in the [AWS Marketplace](https://aws.amazon.com/marketplace).

Today, we'll be using the [Alces Flight Solo (Community Edition)](https://aws.amazon.com/marketplace/pp/B01GC9E3OG?qid=1510807355800).

1. Browse to the [Alces Flight Solo (Community Edition)](https://aws.amazon.com/marketplace/pp/B01GC9E3OG?qid=1510807355800) in the AWS Marketplace.
2. On the right-hand side of the marketplace listing you should see  a **Region** drop-down. Today we're going to be using the **Asia Pacific (Sydney)** region. Please don't try and use the Tokyo or Mumbai or Frankfurt regions. You can try those out later :)  
  
	![Alces Flight Solo from the AWS Marketplace](images/aws-marketplace-region.png)

3. We'll also make sure we're using the **Single AMI** delivery method. This will give us a usable Alces Flight environment with a single node. This is all we need to get started with our work in the cloud.
4. Once you have these options selected, click the **Continue** button.
4. On the 1-Click Launch screen we need to carefully select the **security group** we'll use to access our compute environment. Make sure you select **kzn-lab** from the dropdown, as below:  
  
	![Alces Flight Solo - selecting keypair and security group](images/aws-marketplace-sg-keypair.png)
  
5. We also need to make sure that **our keypair** is selected. **This is important**. If you select the default or someone else's keypair, **you won't be able to connect to your compute environment**.  

	Make sure you select the keypair you created earlier in the lab.

7. Now, scroll back to the top of the screen click the **Launch with one click** button on the right hand side. Your new Alces Flight compute environment is now launching!

Now we want to look at the status of our new compute environment launching from the EC2 console. To do this:

1. Browse back to the EC2 Console, and click on **Instances** again in the left hand navigation. You may see multiple compute environments launching. This is because we are effectively launching many environments in parallel - one for each person doing the lab! The cloud is good at doing things in parallel :)

2. Nevertheless, we want to find **our** compute environment. To do this, we'll filter the instances and use the **Key Name** attribute. To do this, click on the top search box above the list of instanced in the EC2 console, and type **Key Name**. You should see a list of key names autocomplete. See below:

	![EC2 Console - filter by keyname](images/aws-ec2-filter-keyname.png)

	Once you select your keypair name from the list, the filter will only show **your instance**.
	
3. We're going to do one more bit of housekeeping before we finish with the EC2 console. We're going to **tag** our instance so we can find it easily in the future. If you mouseover your instance, you'll see a small pencil icon appear  over the **Name** field. Click on the pencil icon

4. A field will appear. Type something that you'll remember so you can find your instance easily in the future. You can always use filters like we did above, but sometimes it's easier to just see your instance at a glance. For example, I've called my instance:

	![EC2 Console - tagging your instance](images/aws-ec2-tagged.png)
		
5. Now if you browse back to the EC2 console and list instances again in the future, you'll see your named instance in the list.

## Connecting to your Flight compute environment using SSH

To connect to our compute environment we first need to know what the public DNS name for the instance is.

To do this, browse to the EC2 console, and select your compute environment. In the instance details pane at the bottom of the screen you should see the Public DNS name for the instance. The following screenshot shows this:

![Public DNS for your instance](images/aws-public-dns-instance.png)

Copy this DNS name into your clipboard. We'll refer to it later when we're connecting to our instance via SSH.

***OS X / Linux***

If you're using **OS X** or **Linux**, you can simply use the built in SSH client. To do this:

1. Open a terminal window
2. Make sure the permissions on your private key are correct (you should have done this when you created your key):  
  
	`chmod 400 <<your_private_key.pem>>` 

	and then type the following:  
  
	`ssh -i your_private_key.pem>> -L 8888:localhost:8888 alces@ec2_publicdns_name
`

	Where,

	**ec2_publicdns_name** is the DNS name for your EC2 instance
	**your_private_key.pem** is the path to your private key you downloaded earlier

***Windows***

If you're using **Windows**, you can use the PuTTY program to connect to your EC2 instance over SSH and setup the SSH tunnel. To do this, follow [Setting up an SSH tunnel with PuTTY](http://realprogrammers.com/how_to/set_up_an_ssh_tunnel_with_putty.html).

What we are doing here is creating a secure tunnel between your local computer and the server. This means all traffic will be encrypted and we won't be exposing any unsecured public ports on the internet. This makes our connection to the Jupyter instance considerably more secure.

After you've figured this out you should have a remote SSH session to your very first compute environment using Alces Flight, and should see something like the screenshot below:

![Alces Flight ASCII art](images/alces-flight-login.png)

## Configuring our compute environment

The first thing you should note is that our flight compute environment isn't fully configured yet. This is obvious from the message when we first log in:

	Configuration of this node has not yet been completed and it is not yet operational.

To do finish configuration for our node, we'll type a single command at the command line prompt:

`alces configure node`

The 'alces configure' command will prompt us for some information. Just hit enter and accept all defaults.

Our Flight environment will now go and configure itself, and prompt us when it's finished - it'll take about 60 seconds. While you wait, you might be wondering why we need to do this. Our Flight environment as it stands right now is a simple single instance. Today we'll just be using it as a single compute environment in the cloud. However, Flight is considerably more powerful than this. We can come back at a later date and configure our Flight environment to be a self-contained HPC cluster if we so wish. We might start with a single instance, use it for prototyping and testing, and then when we want access to more compute or memory, or we want to run real-world, large HPC type applications, we can use the same compute environment and "scale" it up to a fully featured HPC cluster with as many nodes, cores, memory as we desire. This is a really powerful feature of the cloud - you can start small, and scale up only when you need it.

Once our Flight compute environment has finished configuring itself, you'll see your command line prompt update with a message saying so. Something like:

![Alces Flight - node configuration](images/alces-flight-configuration.png)

**Once you see this, log out of your SSH session, and reconnect.** This is the easiest way for our current session to be updated.

## Exploring Alces Gridware

Another neat thing we have at our fingertips is a huge range of software.

At the command prompt, type the following:

`alces gridware list`

You should see a large number of different software packages we can install. We're going to install Anaconda (which also installed Jupyter for us).

To do this, again at the command prompt, type:

`alces gridware list anaconda*`

You'll see the Anaconda package available. To install it, type:

`alces gridware install main/apps/anaconda3`

Gridware will go off and download the package for us and any dependencies and then install it. N.B. it could take up to 1 minute for this to complete. Once installed, we'll need to **enable** the new package using the **module** subsystem.

To do this, type:

`module avail`

You should see all the modules available for you to enable on your Flight compute instance. We'll just enable the Anaconda3 module.

To do this, type:

`module load apps/anaconda3`

### Python modules, weather radar data and AWS SDKs

We'll also install some python modules we'll be using later in this lab. To do this, type:

`conda install -c anaconda netcdf4 numpy boto3`

Select that you wish to proceed if prompted and wait for the system to download and install these modules. This could take some time, perhaps as long as 5-6 minutes. Perhaps grab a cup of coffee or help the person next to you if you're ahead of them :)

What these Python modules will let us do is connect to Amazon S3, download weather data from the NEXRAD archive and then parse and manipulate this data in [NetCDF](https://en.wikipedia.org/wiki/NetCDF) format. NetCDF is very commonly used in the scientific computing world for creating and sharing array-oriented data.

We also need the geopy and awscli Python modules:

`pip install geopy awscli`

And finally, we also need to install py-ART:

	cd ~
	git clone https://github.com/ARM-DOE/pyart.git
	cd pyart
	python setup.py install --user
	cd ~
	mkdir data
	export HDF5_DISABLE_VERSION_CHECK=2
	

This will take about 1 minute to install. [Py-ART](http://arm-doe.github.io/pyart/) or the Python ARM Radar Toolkit, is a really neat module that gives you the ability to use a whole range of weather radar primitives and algorithms. We won't be covering it in detail, but we will be using it soon to visualize some of the NEXRAD weather data.

### Setting up the AWS SDK

Before we're ready to do some data munging and visualisation we need to do one more thing, setup credentials for the AWS SDK.

1. To do this, from your Terminal window, run:

	`aws configure`

2. The first thing you'll be prompted for is your **AWS Access Key ID**. Go back to the lab environment page, and for your username get the AWS Access Key from the 3rd column in the table. It will probably start with something like 'AKIA...'

3. The second credential you'll need to enter is your **AWS Secret Access Key**. This is in the fourth column in the lab environment details page for your user name. Copy and paste that into the Terminal window.

4. Where you are prompted for **Default region name** and **Default output format** just leave both blank and **hit enter** twice.

Now, what this has done is configure our compute environment to know how to authenticate as our lab user and use the AWS APIs, either via the AWS CLI or using one of the AWS SDKs. If you were watching really closely earlier, we installed a Python module called **boto3** which is the Python 3 AWS SDK. We'll be using that in a minute.

Finally, we can now we can run a Jupyter notebook with all the dependencies we need installed and configured!

To do this, type the following in your Terminal window:

`jupyter notebook --no-browser`

## Using Jupyter on your Flight environment

If you've got this far, you should now have the Jupyter notebook server running on your Flight compute environment. Because we connected to our EC2 instance by setting up an SSH tunnel, we can connect to our Jupyter notebook from our local laptop just like it was running locally. It's not though, it's running in the cloud.

To test this, open a web browser on your laptop or local machine, and browser to:

`http://localhost:8888/`

You should see a Jupyter notebook environment load, something like:

![Your Jupyter notebook environment](images/alces-flight-jupyter-notebook.png)

1. Open a new Terminal in your Jupyter environment and do the following:

	`git clone https://github.com/scicolabs/Weather-Data-Eng-Pipeline.git`
	
	This contains some great code example written by Martin Bertin. The original Github repository can be found at [https://github.com/MarvinBertin/Weather-Data-Eng-Pipeline](https://github.com/MarvinBertin/Weather-Data-Eng-Pipeline).

2. Once you've cloned this repository into your Jupyter notebook environment you should be able to run the sample notebooks.  
  
	The notebook we'll be focusing on is called **PART2-Weather_Data_Batch_Mapping_Visualization.ipynb**.
	
	To run this, browse into the **Weather-Data-Eng-Pipeline** folder and then click on the **PART2-Weather_Data_Batch_Mapping_Visualization.ipynb** notebook.
	
	![Navigating to Weather notebooks](images/jupyter-notebook-navigating.png)
	
3. You should see the notebook load in a new browser tab/window. Step through the code in this notebook.

	The first plot we do of the NEXRAD data is Hurricane Katrina:

	![Hurricane Katrina](images/jupyter-notebook-hurricane.png)

4. This notebook is using the NEXRAD archive stored on Amazon S3 as part of the AWS Open Data program.

![Notebook plots](images/jupyter-notebook-plots.png)

## Starting a Linux desktop session and connecting to it using VNC

Another feature that we get with our Flight compute environment is a full Linux desktop environment. Sometimes you'll want to install local desktop tools on your compute environment in the cloud, so it's useful to be able to remotely connect to a desktop session on your compute environment to interactively run those.

Alces Flight gives us some neat functionality that makes it really easy to do this. In this section we'll explore this.

1. From your SSH session from your Alces Flight compute environment, type the following:

	`alces session avail`
	
2. You should see a list of available VNC sessions you can start. We're going to start the Gnome session. To do this type:

	`alces session start gnome`
	
3. Your Flight compute environment will have just configured itself for VNC and will have setup a Gnome desktop session that you can now connect to. To try this you will need a VNC client on your laptop.

	**For OS X:**

	On OS X you can just use the Finder.app to connect to your desktop.
	
		1. Go to Finder and click on the **Go** menu
		2. Select **Connect to server**
		3. In the **Server Address** field, enter the VNC session string that your Flight environment gave you, e.g. vnc://alces:ac2SxctI@54.252.193.251:5901
		
	If you were successful, you should see an Alces Flight logo on Gnome desktop session like the one below:

![Linux Desktop on your Flight compute environment](images/linux-desktop-flight.png)

## Mapping NEXRAD Radar data with CartoDB

The following section was taken from an excellent tutorial by [Stuart Lynn](https://carto.com/blog/author/stuart-lynn/) published on the [Carto.com](https://carto.com/) blog, called [Mapping NOAA NEXRAD radar data with CartoDB](https://carto.com/blog/mapping-nexrad-radar-data/).

1. Open a Terminal from your Linux desktop.

2. You can copy files from the NEXRAD archive by using the AWS CLI on your flight compute environment:

	For example:

		aws s3 cp s3://noaa-nexrad-level2/2014/07/03/KMHX/KMHX20140703_182118_V06.gz ./

3. If you want to, have a go at installing NOAA's Climate Toolkit. To do this, [download the Linux version](https://www.ncdc.noaa.gov/wct/app/dist/wct-4.0.6.zip), and follow the installation instructions.

Once installed, try the tutorial above. It does something very similar to what we just did in a Jupyter notebook environment, but with Linux desktop tools instead.

# Summary

Congratulations! In this lab you created and used your first research compute environment on AWS using Alces Flight.

We used a single instance to do something interesting, but the great thing about Alces Flight (and similar tools on AWS) is that you can start small and then scale to your needs. Alces Flight actually gives you a fully-featured HPC cluster environment on AWS if that's what you need.

Your AWS account team would also love to hear about your use case and what you’re looking to do with AWS.

**Have fun, and remember, don’t forget to the turn your AWS resources off when you leave the room!**

# Additional Resources

AWS Research and Technical Computing

	https://aws.amazon.com/rtc

AWS Open Data

	https://aws.amazon.com/opendata

Alces Flight

	https://alces-flight.com/

For feedback, suggestions, or corrections, please email Adrian White at **whiteadr@amazon.com**


