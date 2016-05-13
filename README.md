# logstash-asterisk

logstash-asterisk is a sample [Logstash](https://www.elastic.co/products/logstash) configuration file for parsing Asterisk's CDR custom file and send it to [Elasticsearch](https://www.elastic.co/products/elasticsearch).

## Requirements
* Queues name pattern in Asterisk like NAME_NNNN (for example, SALES_3102)
* Variable wait4answer to get time how long the caller is waiting before someone answered the call from the queue (see example below)
* Variable direction (0 - internal, 1 - outgoing, 2 - incoming)

### wait4answer
```
[wait4answer]
exten = s,1,Set(CDR(wait4answer)=$[ROUND(${EPOCH}-${WAIT4ANSWER})])
 same = n,Return(${CDR(wait4answer)})

exten = 1,1,NoOp()
 same = n,Set(__WAIT4ANSWER=${EPOCH})
 same = n,Queue(SALES_3102,t,,,300,,,wait4answer)
 same = n,Hangup()
```
