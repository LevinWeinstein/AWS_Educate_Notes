# Software Development

## Software Development: Introduction

Software development is a process by which standalone or individual software is created using a specific programing language. It involves writing a series of interrelated programming code, which provides the functionality of the developed software. Software development may also be called application development and software design.

Topicsrelated to Software Development include:

* Architecture
* DevOps
* Programming Languages
* Scripting Languages
* Mobile Applications
* Web Development

## Software Development: DevOps

DevOps is the comination of cultural philosophies, practices, an tools that increases an organization's ability to deliver applications and servies at high velocity: evolving and improving products at a faster pace than organizations using traditional software development and infrastructure management processes. This speed enables organizations to better serve their customers and compete more effectively in the market.

#### Continuous Integration

Continuous integration is a DevOps software development practice where developers regularly merge their code changes to a central repository, after which automated builds and tests are run. Continuous integration most often refers to the build or integration stage of the software release prcess and entails both an automation component (.g. a CI or build service) and a cultural component (e.g. learning to integrate frequently). The key goals of continuous integration are to find and address bugs quicker, improve software quality, and reduce the time it takes to validate and release new software updates.

#### Continuous Delivery

Continuous delivery is a DevOps software development practice where code changes are automatically built, tested, and prepared for a release to production. It expands upon continuous integration by deploying all code changes to a testing environment and/or a production environment after the build stage. When continuous delivery is implemented properly, developers will always have a deployment-ready build artifact that has passed through a standardized test process.

Continuous delivery lets developers automate testing beyond just unit tests so they can verify pplication updates across multiple dimensions before depoying to customers. These tests may include UI testing, load testing, integration testing, API reliability testing, etc. This helps developers more thoroughly validage updates and pre-emptively disover issues. With the cloud, it is easy and cost-effective to automate the creation and replication of multiple environments for testing, which was previously difficult to do on-premises.

[Building a CICD Pipeline for Container Deployment to Amazon EC2](https://youtu.be/9lJwlOh0B2s)

## Software Development: DevOps Blue/Green

Do you know what blue/green development entails? What are other strategies for building and deploying software?

* A **blue/green deployment** is a change management strategy for releasing software code. Blue/green deployments, which may also be referred to as **A/B deployments** require two identical hardware environments that are configured exactly he same way. While one environment is active and serving end users, the other environment remains idle.

* Blue/green deployments are often used for consumer-facing applications and applications with critical uptime requirements. New code is released to the inactive environment, where it is thoroughly tested. Once the code has been vetted, the team makes the idle environment active, typically by adjusting a router configuration to redirect application program traffic. The process reverses when the next software iteration is ready for release.

* If problems are discovered after the switch, traffic can be directed back to the idle configuration that still runs the original version. Once the new code has proven itself in production, the team may choose to update code in the idle configuration environment to provide an added measure of capability for disaster recovery.

## Software Development: Infrastructure as Code
As your teams and infrastructure grow, it becomes more difficult to track IT resource changes as well as identify who made hanges and when. It also becomes harder to enforce standards for your infrastructure resources, resulting in configuration drift and potential security issues. On AWS, you can easily standardize infrastructure configurations for commonly used IT services while also enabling self-service provisioning for your company. Once these resources are provisioned, you can then track how these resources are connected and monitor configuration changes and drift. In this session we will discuss how you can achieve a sophisticated level of standardization, configuration compllicnace, and monitoring using a combination of AWS Service Catlog, AWS COnfig, and AWS CloudTrail.

1. Provisioning - AWS CloudFormation
	* AWS CloudFormation is a service that provides a common language for you to describe and provision all the infrastructure resources in your cloud envionment. CloudFormation allows you to use a simple test file to model and provision, in an automated and secure manner, all the resources needed for your applications accross all regions and accounts. Once everything is modelled, this text file serves as the single source of truth of your cloud environment. You can also create a collection of approved CloudFormation files in AWS Service Catalog to allow your organization to only deploy approved and compliant resources.
2. Operations Management - AWS Systems Manager | AWS CloutTrail | AWS Config
	* AWS provides a set of services for systems and operations management that allows you to control your infrastructure resources with proper governance and copmliance. You can use AWS Systems Manager to quickly view and monitor all your resources and automate comon operational tasks, such as patching or state management. Systems Manager provides a unified user interface, enabling you to easily manage your cloud operations activities in one place. You can also use AWS CloudTrail for logging user activities within your organization and AWS Config for inventorying all configurations across your resources.
3. Monitoring and Logging - Amazon CloudWatch
	* Amazon CloudWatch (Links to an external site.) is a monitoring service for AWS cloud resources and the applications you run on AWS. You can use CloudWatch to collect and track metrics, collect and monitor log files, set alarms, and automatically react to changes in your AWS resources. CloudWatch can monitor AWS resources such as Amazon EC2 instances, DynamoDB tables, and RDS DB instances, as well as any custom metrics or log files generated by your applications. CloudWatch also provides a stream of events describing changes to your AWS resources that you can use to react to changes in your applications.
4. Managed Services for Configuration Management - AWS OpsWorks
	* AWS OpsWorks  (Links to an external site.)is a fully-managed configuration management service that hosts and scales Chef Automate and Puppet Enterprise servers. OpsWorks eliminates the need to install and operate your own configuration management systems or worry about scaling its infrastructure. It also works seamlessly with your existing Chef and Puppet tools. OpsWorks will automatically patch, update, and backup your Chef and Puppet servers as well as maintain the availability of them. OpsWorks is great choice if you are an existing user of Chef or Puppet.
