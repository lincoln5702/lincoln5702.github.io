
* decoupling of data streams and systems
* for a single company or system we have source system and the target system client side and the server side
* ![](attachments/kafka%20demo.png)
![](attachments/kafka%20usage.png)

![](attachments/kafka%20real%20use%20case.png)



the format is :

real world events( iot , insurances , logistics shipping ) -> producer(application that we write ) -> kafka cluster( made up of brokers)

brokers can be anything (container , vms , machines )
with their own local storage

glossary terms:
* producer:
* consumer:
* broker:
* topic:
* zookeper:


topic:
![](attachments/topic.png)

topic partition and segments
![](attachments/apache_kafka%20topic%20partition%20and%20segments.png)

