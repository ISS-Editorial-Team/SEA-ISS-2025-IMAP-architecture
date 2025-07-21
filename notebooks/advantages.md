# Advantages of this design

## Decoupling code

Since we are not connecting services directly together, but instead connecting them via queues and other similar structures, we find a huge advantage from decoupling services. In the long term, this makes maintaining, upgrading, and modifying these services much easier. Each service can have its own versions of libraries or tools. It is simple to insert new services in between existing services, or in addition to existing services - if we have a special case for some events, we can filter those events out and send them somewhere else. Alternatively, as long as we match the inputs and outputs, we can insert processing between two steps without modifying either one.

Anyone who has attempted to upgrade the versions of a highly integrated, large, and legacy system can immediately see the advantages of testing, deploying, and upgrading small pieces of a system rather than needing to lift everything at once. Making updates simpler and easier also reduces the need for large jumps in version numbers - it becomes something one person can do in a few days. 

This decoupling helps create a highly flexible and re-workable infrastructure. 

## Scaling

By dividing up processing and running small processes in the cloud, we also scale more effectively within the cloud. One of the major advantages of using the cloud is the ability to scale up as needed. However, scaling up to using larger servers and running longer code is the wrong way to go. Instead, it is preferable to scale horizontally. 

**Vertical scaling**

Vertical scaling is when you commit more resources to process more data. This is the default for most software. If you run code on one file, it takes a certain amount of CPU time and RAM. If you run code on a hundred files, it takes more CPU time and more RAM. Writing parallel code is a perfect example of scaling vertically. Rather than running in series and taking more time, parallelized code runs in parallel and takes more space. On-prem, this is the standard way of running, because you generally want to use all the resources you have available to you. 

**Horizontal scaling**

Horizontal scaling is when you scale by adding more servers for processing. So, instead of having one super strong person to pick up and move your couch, you employ a bunch of children to work together and pick up and move your couch. Code does need to be developed in a specific way to take advantage of horizontal scaling, because you have to scale externally to the code itself. 

Why is horizontal scaling preferable within the cloud? As we previously discussed, cloud providers reward you for using smaller instances. In addition to making them cheaper, these providers can also limit the number of large servers. It is also slower to start up larger servers. Due to all these factors, it is generally preferable to start up a number of small servers rather than one large server. AWS and other providers can also handle horizontal scaling for you, if you are using queues as an input. They will automatically scale up parallel processing until they hit a user-set limit. This can also help avoid some of the manual optimization for server size that is necessary when running in the cloud.

Once everything is set up to scale horizontally, we are only limited by the number of servers we can request, and the cost to run. This allows for significant amounts of scaling in the cloud if needed. 

## End user advantages

Our end users for the IMAP SDC are primarily scientists or instrument engineers. We work closely with them to ensure our system can handle all the processing requirements for all ten instruments. Over the course of development, these requirements have changed extensively, so we have fully tested the flexibility of our design. Fortunately, although we have added many new services, database tables, and queue tooling, the core design has actually remained unchanged from when it was first proposed. This is a testament to the flexibility of the microservices and queuing design. 

In addition to requesting changes within our processing code, we have also been working with our end users to explore data access and visibility into processing. Our main method of data access is a Python wrapper around a REST API (which is described in more detail at another presentation in this conference). During development, scientists are actually able to upload test data into our pipeline without (much) intervention from SDC developers. In this way, we are able to enable scientific development for other users even well before launch. This also allows for a very quick iterative development process on not only the scientific algorithms, but also on the data access methods. This will really enable us to hit the ground running soon after launch with a well-tested and well-understood system for data access. 

The scientists using our systems, and especially those working on instrument science, are very interested in getting data quickly. Since we can scale up as required, and event-driven processing does not delay processing runs unnecessarily, we are able to deliver data quickly to these scientists and make it immediately available in our API. 

Are there any end-user disadvantages? We have definitely encountered some growing pains from this new paradigm. There is less control over when and how processing runs, so having things like release notes or delaying processing because of external information is more difficult. There has also been some difficulty over frequent re-runs of data - since our goal is to have a system which runs without any human input, any time a file is updated, it triggers a cascade of re-processing for all the downstream dependencies. There isn't very much we can do to control this without human input, as it's difficult to determine what has changed and if it's important automatically. However, this does mean we expect to produce more file versions than usual. We will see if that ends up causing further problems with our end users. 


## Onboarding new developers

Using infrastructure as code gives a written, strictly defined record of the required infrastructure for running processing. As long as all this code is well-documented, it is theoretically possible for a new member of the SDC to learn how the entire system works without any help or other information. In practice, it is rather difficult to put together how the entire system works, but thanks to unit tests on the infrastructure, and the ability to roll back any broken changes, it's pretty low-impact for new developers to create and release changes. We can also deploy the entire system in a personal account for more in-depth, manual testing without affecting what is currently running.

