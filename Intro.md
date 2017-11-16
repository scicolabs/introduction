# Introduction to Research Computing on AWS
## Introduction

In this lab, you will be introduced to the basic tools required to get started with Research Computing on AWS. A key ingredient for any research work is data. We’ll show you how to move data into and out of the AWS cloud efficiently and securely using the AWS CLI tool and Amazon S3.

When it comes to processing and analyzing that data, AWS offers a massive array of tools and platforms you might be interested in; from small to very large computers, powerful virtual scientific workstations with GPUs for data visualisation, big data tools like Hadoop and Spark, relational databases like Postgres (with PostGIS for geospatial work), and even your own personal HPC clusters in the cloud.

We’ll start at the beginning though. After moving data into S3, we’ll show you how to start your own powerful Linux workstation using a AWS partner solution called Alces Flight. To start with, this will give you a very capable Linux workstation in the cloud, with lots of software at your fingertips ready to install. We’ll install some scientific software and interact with the data we’ve moved into S3. Once we’ve done that though, we’ll show you how easy it is to turn this simple workstation into your own personal HPC cluster. The focus of this lab won’t be on HPC, but we have other courses we run that teach you how to do scalable high throughput or high performance work on AWS using tools like Alces Flight and AWS Batch or how to do big data analytics on AWS using Apache Hadoop and Spark with Amazon EMR etc.

The great thing about AWS is you can start simply and with a small amount of resources, but quickly scale that up to some of the largest infrastructure in the world if you need it. You get to decide what you need, and you have complete control over what scale of resources you use. We think you’ll be pleasantly surprised at how easy it is to get started doing meaningful research work on the cloud!

## Technical Knowledge Prerequisites

To successfully complete this lab, you should be familiar with the following:

- Introductory familiarity with SSH and Linux
- Command line familiarity – copy files/data
- Linux desktop (e.g. Gnome) experience not necessary, but helpful

## Topics Covered

This lab will take you through:

- Creating Jupyter Notebook environments on AWS
- Using very simple notebook examples to step through and interactively run code in your Jupyter Notebook environment on AWS
- Explore [Apache MXNet](http://mxnet.io/) to develop, train and evaluate deep learning models using GPUs in AWS

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

The EC2 console will now download the private key for your newly created key pair. Before we can use the private key we’ll need to update the permissions on it. To do this type:

    chmod 0400 lab61.pem

Don’t lose this and store it in a safe place! It effectively authenticates you when using AWS programmatically. You don’t want someone else impersonating you or using your credentials. We’ll be using this private key later in the lab.

# Module 1 - Moving research data into and out of Amazon S3

Very often you'll want to move data efficiently into the cloud so you can work on it.

There are a number of ways of doing this. One common way of moving data around quickly and securely is to use the [AWS CLI](https://aws.amazon.com/cli/). There are other graphical user interface tools that make it easy to move data into and out of AWS too, like [Cyberduck](https://cyberduck.io/). This is what we'll be using today.

## Installing Cyberduck

Go to the [Cyberduck website](https://cyberduck.io/), download the client for your operating system (Windows or OS X), and install it. Once installed, run the program. You should see something similar to this:

![Cyberduck](images/cyberduck.png)

What we need to do is configure our AWS credentials to securely authenticate and connect to the S3 service. This will let us view buckets, and upload data. We've already created an S3 bucket for you called **kzn-lab** and we'll be using that today as the target we'll upload data to and from.

To do this, we'll need to create a new connection to S3. In your Cyberduck application, click **Open Connection**. You should see something like the following:

![Cyberduck](images/cyberduck-open-connection.png)

Here we'll need our **AWS Access Key ID** and **AWS Secret Access Key ID** for the lab user we're using. You'll get this from the lab environment details page for the user you logged into the AWS lab account with.

Take your **Access Key ID** and your **Secret Access Key ID** and copy/paste them into Cyberduck. Once done, click the **Connect** button.

If you were successful, you should see one or more S3 buckets. One of which should be called **kzn-lab**:

![Cyberduck](images/cyberduck-buckets.png)

We'll use the **kzn-lab** bucket to move data into and out of the cloud.

## Uploading data

To upload data, double click on the **kzn-lab** bucket and make sure you've navigated to it. You should see the contents of the bucket now (either empty or with some data in it from other users).

From Cyberduck again, click the **Actions** dropdown. You should see the following actions:

![Cyberduck](images/cyberduck-actions.png)

Click **upload** and browse to a local file on your laptop you'd like to upload. Bear in mind, this is shared bucket. Everyone in the lab account has access to it. Within your own AWS account you will be the only person who can see and share data (by default), unless you choose to open up access to other users. How to do this securely is beyond the scope of this lab, but if you'd like to learn more about securely sharing data in Amazon S3 have a look at [Managing Access Permissions to Your Amazon S3 Resources](http://docs.aws.amazon.com/AmazonS3/latest/dev/s3-access-control.html).

Once you've started uploading data, Cyberduck will give you a nice indication of the progress of your upload:

![Cyberduck](images/cyberduck-uploading.png)

Once your upload is complete, you should see:

![Cyberduck](images/cyberduck-upload-complete.png)

## A few words on Amazon S3

It's worth making a few comments about Amazon S3. S3 is designed to provide 99.999999999% durability and 99.99% availability. In the Sydney AWS region, it costs roughly 2.5c per GB per month for data stored there. It

There are also different storage tiers which are even more cost effective. To learn more, visit [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/).

Some of the largest research datasets in the world are stored in S3 and accessed incredibly frequently and at massive scale by researchers all around the globe. For example, all of our [AWS Open Data](https://aws.amazon.com/opendata) datasets are stored on S3. Some of the more interesting datasets you can use for free are available at [Earth on AWS](https://aws.amazon.com/earth/). In fact, one of the datasets we'll be using today is the [NEXRAD on AWS](https://aws.amazon.com/public-datasets/nexrad/) dataset from the US National Oceanic and Atmospheric Administration (NOAA).

# Module 2 – Creating your first compute environment

In this module we'll create our first compute environment on Amazon EC2. We'll use a tool called [Alces Flight](https://alces-flight.com) to get started quickly.




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


