---
layout: single
title: "Cloud Computing: IaaS, PaaS && SaaS"
author: "Danni"
header-style: text
toc: true
toc_sticky: true
lang: en
tags:
  - Cloud
---

> My experience with AWS, Azure and Google Cloud"

As of 2019, I have had a few years of experience with all three cloud computing providers, whether was doing personal projects, intrenship or coursework, 

![hadoop dessert](/assets/images/post/cloud.jpeg)
<small class="img-hint">[10 reasons to give cloud computing a go](https://hackernoon.com/10-reasons-to-give-cloud-computing-a-go-dc3184ec550e)</small>


### Cloud Computing was about "Resource Management"

The initial objective of realizing cloud computing is to better manage all the resources: **computing**, **networking** and **storage**. Imagine how the internet world was like before the term of "cloud computing" happens, buying a computer means the processing power depends solely on the CPU the computer has and the storage is already determined unless we add a hard drive or convert part of the data from PC to external drives, which may differ from 10G to 2T in terms of size.

Try applying the same thing to the data center, where every sever in that room can be accessed online via routers. If people want to use resources provided by such devices, how do we manage them? How to make sure we can access them whenever, wherever we want and can customize our needs like storage size? 

All of these needs have actually been satisfied by the development of cloud computing, because it can easily manage the resources with flexibility in computing, networking and storage. However, such flexibility has come a long way from the beginning. 

### Virtulization

Now we have to talk about **Virtualization** -- the process of running a virtual instance of a computer system in a layer abstracted from the actual hardware. Most commonly, it refers to running multiple operating systems on a computer system simultaneously.

##### Why virtulization?

There are many reasons why people utilize virtualization in computing. To desktop users, the most common use is to be able to <u>run applications meant for a different operating system without having to switch computers or reboot into a different system</u>. For administrators of servers, virtualization also offers the ability to run different operating systems, but perhaps, more importantly, it offers a way to <u>segment a large system into many smaller parts, allowing the server to be used more efficiently by a number of different users or applications with different needs</u>. It also allows for <u>isolation, keeping programs running inside of a virtual machine safe from the processes taking place in another virtual machine on the same host.</u>

##### Hypervisor

A hypervisor is a program for creating and running virtual machines. 

##### Difference between a container and a virtual machine?

**Linux Container**: contain applications in a way that keep them isolated from the host system that they run on. Containers allow a developer to package up an application with all of the parts it needs, such as libraries and other dependencies, and ship it all out as one package. And they are designed to make it easier to provide a consistent experience as developers and system administrators move code from development environments into production in a fast and replicable way.

While both containers and virtual machines allow for <u>running applications in an isolated environment, allowing you to stack many onto the same machine</u> as if they are separate computers, **containers are not full, independent machines**.

A container is actually just an isolated process that shared the same Linux kernel as the host operating system, as well as the libraries and other files needed for the execution of the program running inside of the container, often with a network interface such that the container can be exposed to the world in the same way as a virtual machine. Typically, containers are designed to run a single program, as opposed to emulating a full multi-purpose server.

##### Difference between virtualization and cloud?

> Disclaimer: I do not own any copyright in the following paragraph. It's shamelessly borrowed from this [RedHat post](https://www.redhat.com/en/topics/cloud-computing/cloud-vs-virtualization).

**Virtualization** is the technology that separates functions from hardware, while clouds rely on that split. It's easy to confuse the 2, particularly because they both revolve around creating useful environments from abstract resources.

The easiest way to describe the difference is from a purely Infrastructure-as-a-Service (IaaS) perspective. At the base of cloud computing is a stable operating system (like Linux®). This is the layer that gives users independence across public, private, and hybrid environments. Assuming intranet access, internet access, or both is already established, virtualization is what creates clouds. Software called a hypervisor sits on top of physical hardware and abstracts the machine's resources. These resources can be raw processing power, storage, or cloud-based applications containing all the runtime code and resources required to deploy it

**If the process stops here, it's not cloud computing—it's just virtualization.** Virtual resources need to be allocated into centralized pools before they're called clouds, and those clouds need to be orchestrated by management and automation software before it's considered cloud computing. Clouds deliver the added benefits of self-service access, automated infrastructure scaling, and dynamic resource pools, which most clearly distinguish it from traditional virtualization.

||Virtulization | Cloud|
|------|--------|------|
|**Definition** | Technology | Methodology|
| **Purpose** | Create multiple simulated environments from 1 physical hardware system |Pool and automate virtual resources for on-demand use |
| **Use** | Deliver packaged resources to specific users for a specific purpose |Deliver variable resources to groups of users for a variety of purposes |
| **Configuration** |Image-based | Template-based |
| **Lifespan** |Years (long-term) | Hours to months (short-term)|
| **Cost** | High capital expenditures (CAPEX), low operating expenses (OPEX) |Private cloud: High CAPEX, low OPEX 
Public cloud: Low CAPEX, high OPEX |
| **Scalability** |Scale up |Scale out |
| **Workload** | Stateful | Stateless|
| **Tenancy** |Single tenant |Multiple tenants |

If you already have a virtual infrastructure, you can create a cloud by pooling virtual resources together, orchestrating them using management and automation software, and creating a self-service portal for users.

##### The Automation of Virtualization

Though virtualizing a machine didn't take too much time, it's very time-comsuming if people needed to manage and configure all the machines manually as the network scaled up. That's when **Scheduler** comes in -- helping users find a perfect spot in the resource pool that satisfies their storage/CPU/Hard drive needs. 

At this step, we can finally call it **Cloud**, not just virtualization.

### My Experience

> This part just stands for my personal views and experience.

![cloud compare](/assets/images/post/compare.jpeg)
<small class="img-hint">Fantastic Four in the Cloud :)</small>

##### Google Cloud Platform

For me, I started with Google Cloud Platform in the cloud world. Because there were some Hackathon prototype or side projects need Google Map API with. Also, at the company I'm interning for **geomapping** is always required for accuracy purpose(I can't go further due to security reasons). So yes, like most of newbies, I started with API. But after that I went on to some more basic services like compute engine, storage(didn't do much with this part) and networking. Though GCP takes the least part in this three huge vendors, I have special feelings for it. Not just because it's cheaper than AWS lol.

One thing I would argue is that AWS indeed has <u>more product line and better customer services</u>(come at me I don't care), but Google Cloud Platform performs better in **deep learning** or **data analysis** side. For example, **Big Query**, it can fully support SQL(Join specially), "pay as you go", and have OLAP DW. Imagine you need to train a Tensorflow, how easy would that be.

Also let's talk about **Kubernets**, its deployment using Google Cloud is genuinely a delight.

##### Azure

The company I'm currently interning for is a client of Microsoft so as an Infrastructure team member I spent a certain amount of time on its documentation. And just as my case suggests, Azure is enterprise focused and the most amazing thing about Azure is: **It connects flawlessly with all the other Microsoft products!** For example, directly open files stored on the cloud or using VS to develop and deploy. Its ecosystem from Iaas(VM, Network) to PaaS(Storage, AAD, SQL...) and then to SaaS(Office365, ML) is just amazing.


##### AWS

> I wanna drink the tea of MongoDB on AWS but it can have another chapter, let's focus on the performance now.

Though I heared(LOL), read and studied some tech blogs and papers of authors' experience with AWS, I never actually opened an account untill this semester when I took **Cloud Computing** as an elective course since it's required for the lab. So far I have mainly circled around **EC2** and deployed a Django project using it. Its elastic load and auto scaling performance is really soothing. 

Aside from the tech part, I think Amazon really knows its customers -- which Google and Microsoft are not as good at. It updates the product line fast enough and can keep up with market need. (Not taking MongoDB for example:).

### Reference

Special thanks to the authors of following posts. They all have unique, valuable insights and I definitely learnt a lot from them.

[CSDN: 什么是云计算？（IaaSPaaS,SaaS区别）](https://blog.csdn.net/gui951753/article/details/81488435)

[Hacker Noon: 10 reasons to give cloud computing a go](https://hackernoon.com/10-reasons-to-give-cloud-computing-a-go-dc3184ec550e)

[What's the difference between cloud and virtualization?](https://www.redhat.com/en/topics/cloud-computing/cloud-vs-virtualization)

[What is virtualization?](https://opensource.com/resources/virtualization)
