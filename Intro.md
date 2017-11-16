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

# Module 1 – Creating your first compute instance

In this module we'll create our first compute instance on Amazon EC2. We'll use a tool called Alces Flight to get started quickly.

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


