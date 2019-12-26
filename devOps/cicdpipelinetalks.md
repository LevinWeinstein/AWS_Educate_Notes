# Building a CICD Pipeline for Container Deployment to Amazon ECS

[Video Link](https://www.youtube.com/watch?time_continue=77&v=9lJwlOh0B2s&feature=emb_title)

* What to expect from This Session
	* Review continuous integration, delivery, and deployment
	* Using Docker images, Amazon ECS, and Amazon ECR for CI/CD
	* Deployment strategies with Amazon ECS
	* Building Docker container images with AWS CodeBuild
	* Orchestrating deployment pipelines with AWS CodePipeline
	* Demo

## Continuous Integration, Delivery, and Deployment

Let's go back 20 years ago. 20 years ago, current people wouldn't be able to do what they're doing. The first step would be to write a large purchase order to a hardware place and build up a big ass data center.

With AWS and te advent of the cloud, that heavy lifting is no longer the concern of the startup.

This leads to increased agility. Incourages experimentation and innovation. If something fails just trash those EC2 instances and shit.

Talk about: How can we limit the amount of time it takes between completing a change to a piece of software and the resulting deployment to production.

* How can we quickly and reliably deliver good ideas to our customers?

Learnings:
* Frequency reduces difficulty
* Latency between check-in and production is waste
* Consistency improves confidence
* Automation over toil
* Empowered developers make happier teams
* Smaller batch sizes are easier to debug
* Faster delivery improves software development practices.


Source -> Build -> Test -> Production


* Source
	* Version Control
	* Branching
	* Code Review
* Build
	* Compilation
	* Unit Tests
	* Static Analysis
	* Packaging
* Test
	* Integration Tests
	* Load Tests
	* Security Tests
	* Acceptance Tests
* Production
	* Deployment
	* Monitoring
	* Measuring
	* Validation

Continuous Integrations covers Source and Build.
Continuous Delivery & Deployment covers those + Test and Production.

## Docker Images
* Packages Application Code and Runtime Dependencies
	* Reproducible
	* Immutable
	* Portable
* Built from a Dockerfile
	* which is a set of directives that build up that image in a reproducible fashion.

When we have our docker container image, this is now portable.
We can run it in our local dev environment, we can run it in our test environment, and ultimately, we can run it in production porably.

A docker image is composed of a set of layers, and these layers build upon each other.

Dockerfile							microservice:latest
FROM amazonlinux:2017.03            1c2acd7c
RUN yum install -y nginx            8ab2ba66
COPY ./app /bin/app 	            91bd52b7
CMD ["/bin/app"]					d2cccfda

So here we have a hypothetical Docker image called microservice tagged with the latest tag, and we have four layers that consitute this docker image. Each image represents work done within the Dockerfile directive so the dockerfile directive define what is in that layer.

So here we have a small little Dockerfile here, that is deriving our docker container image from amazonlinux, uh, specific tag of amazon linux, march 2017. We're installing dependencies, so here we're installing nginx. We're copying a file into our docker container image and we're defining how to start our docker container image. So when we run our docker image, we want our application to start. So this is a, now, docker container image that we can ship to production and run portably.

And we can have many versions of our docker container image.

microservice:1.0.0
microservice:1.1.0
microservice:1.1.1
microservice:1.2.0

So here we have semantically versioned four different versions from 1.0.0 to 1.2.0. And we can store these docker container images in the container registry. So on the AWS platform, we have the EC2 container registry, which is a docker registry that you can use to store images. There are other container registries as well.

And this allows you to reference that image and access it from different locations and different environments. So we can now have a staging environment perhaps that is pointing to or using our microservice 1.2.0 image while perhaps in prouction we have 1.1.1.

Dev|CI|UAT|Production

So as I said, we now have this image, and our docker container image can move between the different layers of our application.

#### Best Practices
* Pin external dependencies to specific versions for reproducibility
* Package only the runtime requirements for production
* Minimize changes in each layer to maximize cache-ability
* Maintain a .dockerignore file to exclude unneeded files from the image

### Building Docker Images

So, conceptually it's very simple: there's some application code or an application... the runtime environment for that application defined as a docker file. And then there's some sort of process that takes that docker file and actualizes it into a docker container image and creates that docker container image. And there are many different ways to automate the building of docker images. Many customers use jenkins, which is an open-source platform for automation and software delivery. We have aws-codebuild, which we're going to dive into a little bit more deeply, that is a managed-build service that allows you to build all kinds of different projects from JAVA and RUBY and uh, you know all the way to docker and allows you to extend it to custom build environments. And others will, you know, write their own custom scripts and run scripts on EC2 instances.

One thing I wanted to mention quickly is that we're going to talk a little bit about the best practices of maintaining a build environment, there are many ways to do that, a couple that come to mind are just:

* Keep in mind when you're building infrastructure like this to manage your build process.
* Ensure that you're paying as little as you can.

Things like: the EC2 spot instances where you can acquire, basically bid on unused compute capacity for savings of upwards of 90%, those are great for build processes.

And jenkins, we have a plugin to jenkins that automatically manages clusters of spot instances. You can use that to really shave off a lot of cost for your build system. 

#### Codebuild
Likewise, so AWS CodeBuild is a servie to build and test code with continuous scaling with pay-as-you go options (with per-minute pricing).

* Build and test projects across platforms and runtimes including Java, Ruby, Python, Android, Docker, etc.
* Never pay for idle time
* Fully extensible to other platforms through custom build environments

The cool thing about codebuild: it continuously scales. (TL:DR You can have more people building things if you need to and stuff)


Build Specification -- Phases

Phases | Description | Examples
install | Installation of packages into the Environment | Install testing frameworks e.g. RSpec, Mocha
pre_build | Commands to run before the build such as login steps or installation of dependencies | Log in to Amazon ECR, run Ruby bundler or npm
build | Sequence to run the build such as compilation and/or running tests | Run go build, sbt, Mocha, RSpec
post_build | Commands to run after a build on success or failure | Build a JAR via Maven or push a Docker image to Amazon 

Build Specification -- Docker
```
version: 0.2

phases:
 pre_build:
  commands:
   - $(aws ecr get-login)
 build:
  commands:
   - docker build -t "${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}"
  post_build:
   commands:
    - docker push "${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}"
```

Best Practices:
* Minimize your spend on build resources
	* AWS CodeBuild
	* EC2 Spot Instances
* Tag output artifacts to source control revisioins (e.g. git SHA, semantic version)
* Avoid using "latest" or "production" tag
* Optimize or build speed
* Collocate build process with its artifact repository. 

## Deploying Docker Containers

Running a single docker container in a cloud provider is a pretty straightforward exercise, right? You can script that maybe through, using maybe automation like user data, you can have an instance pull up and run an image as a deamon and insure it continues to run. So that's very straightforward to do. But the reality of how folks use docker is it's not just one container image and it's not just one container. It's many many containers or microservices that make up a whole. And orchestrating this becomes a challenge if you're not using some sort of service or tool to help you do that.

So we're going to talk a little bit about the amazon EC2 Container Service as a way to solve that (idk why, kubernetes seems like the play here but whatever) on EC2 instances. The EC2 container service is an orchestration system that allows you to run what we call services and services are managed groups of tasks on EC2 container instances. That integrate with things like elastic load balancing, to manage where and when your docker containers run. It has a lot of features out-of-the-box that just kind of work, so things like when you spin up a container instance, an ec2 instance, one of the best practices is to set the instance profile and scope down the perfmissions that that instance has to the least amount of privileges it needs. With the EC2 container service you can give task-individual permissions just like an instance, you can do autoscaling, so you can scale your service to have more containers or less depending on your load, lots of the kinds of features you would think about using on AWS with instances amazon EC2 container service allows you to use those features within the context of containerized workloads.

So the components here are we have our tasks, which are running on EC2 instances, and we have the amazon ECS service, which is managing those tasks through an agent that is installed on each EC2 instance, and we have some kind of deployment automation that is talking to the ECS service. So what we're going to talk about now is what that automation is and how that works. and in deploying to ECS there are lots of different strategies you can employ.

What we're going to walk through now is how can we get code from our source code repository onto ECS, what is the actual orchestration of steps that happens, you know, when does my new version go out, uh, how do i integrate testing into that, what are the different patterns?

So let's dive write in and talk abotu the first pattern.

Scenario
Service's task definition is updated to a new revision with parameters:

* Deployment
	* In Place - Doubling
	* In Place - Rolling
	* Canary
	* Blue/Green - DNS Swap
	* Blue/Green - Target Group Swap

Best Practices
* Use Elastic Load Balancing health checks to prevent botched deploys
* For higher confidence, integrated automated testing against a new environment or monitoring of a canary before cutover
* Ensure your applicaiton can function against the same backend schema for adjacent releases


## Building a Deployment Pipeline

Deployment Pipeline: The automated manifestation of the process for getting yoru software from version control and into the hands of your customers

Source - Build - Test (Different types of tests can be parallel) - Production

### AWS CodePipeline

Model deployment pipelines through a visual workflow interface which build, test, and deploy new revisions on code changes

* Integrates with AWS Services, open source and third party tools for building, testing, and deploying code

* Extend deployment pipelines with custom logic hrough AWS Lambda functions or custom actions

*  Allows operators to block transitions to "stop the line" and manual approval steps

pipeline:

```
		Developers
     		|
     		v
	  AWS   Code   Commit
	  |               |
	  v               v
AWS CloudFormation  AWS CodeBuild
      |				  |
      v 			  v
Amazon ECS - - ->  Amazon ECR

```
Likewise, as we mentioned, there could be other tools, as well.
(Realistic Version)
```
		Developers
     		|
     		v
	  	  GitHub
	  	    |
	  	    v
	  ~~~~ Jenkins ~~~~
	  |               |
	  v               |
Infra/Provisioning	  |
      |				  |
      v 			  v
Cont Sched/Orch - - ->Container Image Repository
(Kubernetes?)

```


