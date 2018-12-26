# distributed-qmanager
Distributed Queue Manager scenario that uses Remote Queue, Sender, Receiver and XMITQ

### Non-ssl EAST / WEST Installation

Use [mq-install-east.sh](https://github.com/mqfellow/distributed-qmanager/blob/master/mq-install-east.sh) as the pattern for creating the stack in AWS East region. Review the corresponding userdata file. Ensure that your AWS credential specifies the region.

Similarly, the [mq-install-west.sh](https://github.com/mqfellow/distributed-qmanager/blob/master/mq-install-west.sh) script will install the stack on AWS West region. The keypair should be generated on both east/west region. The elastic ip should also be generated.

To delete for each region, run the [mq-delete.sh](https://github.com/mqfellow/distributed-qmanager/blob/master/mq-delete.sh) script.

It uses [east-userdata](https://github.com/mqfellow/distributed-qmanager/blob/master/distributed-queuemanager-userdata-local.txt) and [west-userdata](https://github.com/mqfellow/distributed-qmanager/blob/master/distributed-queuemanager-userdata-remote.txt).

### Self-signed Certificate SSL Setup

[Manual installation](https://github.com/mqfellow/distributed-qmanager/blob/master/self-signed-cert.md)

#### Automatic Installation

Generate the self-signed ssl for qm01 and qmr01 and upload it to s3. This is the automated ssl script that is based on the [Manual installation](https://github.com/mqfellow/distributed-qmanager/blob/master/self-signed-cert.md). The generation of ssl is created inside the [userdata](https://github.com/mqfellow/distributed-qmanager/blob/master/generate-self-signed-ssl-config.txt). Create a folder gen-ssl-config and download/use the [mq-install-east.sh](https://github.com/mqfellow/distributed-qmanager/blob/master/mq-install-east.sh). Modify it to use the [userdata](https://github.com/mqfellow/distributed-qmanager/blob/master/generate-self-signed-ssl-config.txt) to generate ssl. It will also upload it in s3. Once the file certs are uploaded, delete the stack using the [mq-delete.sh](https://github.com/mqfellow/distributed-qmanager/blob/master/mq-delete.sh) script.
 

### TODO

* SSL-signed
* Backup to S3

