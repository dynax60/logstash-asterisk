# logstash-asterisk

logstash-asterisk is a sample of [Logstash](https://www.elastic.co/products/logstash) configuration file for parsing Asterisk's CDR custom file and to send it to [Elasticsearch](https://www.elastic.co/products/elasticsearch).

## Requirements
* Queues name pattern in Asterisk like NAME_NNNN (for example, SALES_3102)
* CDR(wait4answer) is the time in seconds how long the caller is waiting before someone answered the call from the queue (see example below)
* CDR(direction) is direction of the call (0 - internal, 1 - outgoing, 2 - incoming)
* CDR(operator) for me is a company name of called number in Russia. I take it from the [Register of Russian plan numeration](http://www.rossvyaz.ru/activity/num_resurs/registerNum/).
* CDR(region) is a geolocation of such company

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
### /etc/asterisk/cdr_custom.conf
```
[mappings]
; Our CDR log will be written to /var/log/asterisk/cdr-custom/Master.csv
; with the following schema:
Master.csv => ${CSV_QUOTE(${CDR(clid)})},${CSV_QUOTE(${CDR(src)})},${CSV_QUOTE(${CDR(dst)})},${CSV_QUOTE(${CDR(dcontext)})},${CSV_QUOTE(${CDR(channel)})},${CSV_QUOTE(${CDR(dstchannel)})},${CSV_QUOTE(${CDR(lastapp)})},${CSV_QUOTE(${CDR(lastdata)})},${CSV_QUOTE(${CDR(start)})},${CSV_QUOTE(${CDR(answer)})},${CSV_QUOTE(${CDR(end)})},${CSV_QUOTE(${CDR(duration,f)})},${CSV_QUOTE(${CDR(billsec,f)})},${CSV_QUOTE(${CDR(disposition)})},${CSV_QUOTE(${CDR(amaflags)})},${CSV_QUOTE(${CDR(accountcode)})},${CSV_QUOTE(${CDR(uniqueid)})},${CSV_QUOTE(${CDR(userfield)})},${CDR(sequence)},${CDR(direction)},${CDR(wait4answer)},${CSV_QUOTE(${CDR(operator)})},${CSV_QUOTE(${CDR(region)})}
```
