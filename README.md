# distributed-qmanager
Distributed Queue Manager scenario that uses Remote Queue, Sender, Receiver and XMITQ

## EAST / WEST Installation

Use mq-install-east.sh as the pattern for creating the stack in AWS East region. Review the corresponding userdata file. Ensure that your AWS credential specifies the region.

Similarly, the mq-install-west.sh script will install the stack on AWS West region. The keypair should be generated on both east/west region. The elastic ip should also be generated.

To delete for each region, run the mq-delete.sh script.

## TODO

* SSL
* Backup to S3

