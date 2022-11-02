# microservices-training-ground
<a href="https://www.repostatus.org/#wip"><img src="https://www.repostatus.org/badges/latest/wip.svg" alt="Project Status: WIP – Initial development is in progress, but there has not yet been a stable, usable release suitable for the public." /></a><br>

## Description

The main goal of this project is to create an environment for learning and tests which  includes the group of microservices. 
To make this project more “real” I developed a group of microservices which main task is to observe stackoverflow topics and download interesting questions. 
The project is intentionally overcomplicated.  


For each observed stackoverflow topic/tag one instance of [go.stack-app](https://github.com/JacekKorta/go.stack-app) 
is being run. Grabbed questions are moved to RabbitMq.
Because the services can grab the same message (questions can be overlapped between different tags),
questions are deduplicated in the next step.

Service [py.deduplicator](https://github.com/JacekKorta/py.deduplicator) is responsible for deduplication.
This service reads messages from the queue, checks their id in RedisDB and if the id exists the message is dropped.
Otherwise the message is sent to exchange for deduplicated messages and the ID of message is saved in DB

In the third step, the questions are being read, analyzed and tagged. 
They are put to the exchange with the routing key which depends on the question tag received in this step. 
Service [go.message.tagger](https://github.com/JacekKorta/go.message-tagger) is responsible for this step.

As a final step, messages with additional information are sent by [go.discord-publisher](https://github.com/JacekKorta/go.discord-publisher) to the user. 

Diagram:
![mtg diagram](/mtg-diagram.png "microservices trainig ground diagram")

## Sub services

1. [go.stack-questions](https://github.com/JacekKorta/go.stack-app/tree/master)
2. py.deduplicator
3. go.message-tagger
4. go.discord-publisher


## How to run?
First download the main repository:

```bash
git clone git@github.com:JacekKorta/microservices-training-ground.git
cd microservices-training-ground
```

Then update/init all submodules
```bash
git submodule update --init
```

Setup all sub services. See each services's readme file for more information.

Finally you can start:
```bash
docker-compose up
```

---
#### Warning!
The configuration is not production ready. <br>

