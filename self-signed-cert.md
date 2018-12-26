Enable SSL using self-signed certificate 

Source: https://www.youtube.com/watch?v=av1dW9Hd7MM - IBM MQ SSL - Linux | Enabling SSL in between two Queue Managers on Linux Server by two way communication

```
EAST QM01

DIS QMGR CERTLABL
     3 : DIS QMGR CERTLABL
AMQ8408I: Display Queue Manager details.
   QMNAME(QM01)                            CERTLABL(ibmwebspheremqqm01)

Create key.kdb on QM01. Observe the file size of key.kdb
runmqckm -keydb -create -db key.kdb -pw 12345 -type cms -expire 365 -stash 

Create a self-signed certificate. Observe the file size changes of key.kdb
runmqckm -cert -create -db key.kdb -pw 12345 -label ibmwebspheremqqm01 -dn "CN=MQFELLOWEAST,O=ABC,C=US" -size 1024 -x509version 3 -expire 365

Extract the public cert from the self-signed certificate key.kdb
runmqckm -cert -extract -db key.kdb -pw 12345 -label ibmwebspheremqqm01 -target qm01.arm -format ascii

WEST QMR01

DIS QMGR CERTLABL
     2 : DIS QMGR CERTLABL
AMQ8408I: Display Queue Manager details.
   QMNAME(QMR01)                           CERTLABL(ibmwebspheremqqmr01)

Create key.kdb on QMR01. Observe the file size of key.kdb
runmqckm -keydb -create -db key.kdb -pw 12345 -type cms -expire 365 -stash 

Create a self-signed certificate. Observe the file size changes of key.kdb
runmqckm -cert -create -db key.kdb -pw 12345 -label ibmwebspheremqqmr01 -dn "CN=MQFELLOWWEST,O=ABC,C=US" -size 1024 -x509version 3 -expire 365

Extract the public cert from the self-signed certificate key.kdb
runmqckm -cert -extract -db key.kdb -pw 12345 -label ibmwebspheremqqmr01 -target qmr01.arm -format ascii

Copy the pem file of WEST / QMR01 so that qm01.arm can be copied to that location.

chmod 600 mqfellow-us-west-2.pem

scp -i "mqfellow-us-west-2.pem" qm01.arm ec2-user@IP2:/tmp

Copy the QMR01 public arm file. 

cd /var/mqm/qmgrs/QMR01/ssl/
chmod 600 blockchain.pem
scp -i "blockchain.pem" qmr01.arm ec2-user@IP1:/tmp

Exit to root and chown the arm file in west/qmr01

chown mqm.mqm /tmp/qm01.arm

Exit to root and chown the arm file in west/qm01

chown mqm.mqm /tmp/qmr01.arm

su mqm to copy and import the arm file.

cd /var/mqm/qmgrs/QM01/ssl/
mv /tmp/qmr01.arm .

import qmr01.arm in qm01

runmqckm -cert -add -db key.kdb -pw 12345 -label ibmwebspheremqqmr01 -file qmr01.arm -format ascii

import qm01.arm in qmr01

su mqm
cd /var/mqm/qmgrs/QMR01/ssl/
mv /tmp/qm01.arm .

runmqckm -cert -add -db key.kdb -pw 12345 -label ibmwebspheremqqm01 -file qm01.arm -format ascii

alter the channel in QM01

DIS CHSTATUS(*)
     4 : DIS CHSTATUS(*)
AMQ8417I: Display Channel Status details.
   CHANNEL(QM01.QMR01)                     CHLTYPE(SDR)
   CONNAME(52.42.174.228(9000))            CURRENT
   RQMNAME(QMR01)                          STATUS(RUNNING)
   SUBSTATE(MQGET)                         XMITQ(QMR01)
AMQ8417I: Display Channel Status details.
   CHANNEL(QMR01.QM01)                     CHLTYPE(RCVR)
   CONNAME(52.42.174.228)                  CURRENT
   RQMNAME(QMR01)                          STATUS(RUNNING)
   SUBSTATE(RECEIVE) 

https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_9.1.0/com.ibm.mq.dev.doc/q113220_.htm

ECDHE_ECDSA_AES_128_CBC_SHA256

ALTER CHANNEL(QM01.QMR01) CHLTYPE(SDR) SSLCIPH(TLS_RSA_WITH_AES_256_CBC_SHA256)
ALTER CHANNEL(QMR01.QM01) CHLTYPE(RCVR) SSLCIPH(TLS_RSA_WITH_AES_256_CBC_SHA256)
REFRESH SECURITY TYPE(SSL)

on QMR01

DIS CHSTATUS(*)
     2 : DIS CHSTATUS(*)
AMQ8417I: Display Channel Status details.
   CHANNEL(QM01.QMR01)                     CHLTYPE(RCVR)
   CONNAME(52.86.146.171)                  CURRENT
   RQMNAME(QM01)                           STATUS(RUNNING)
   SUBSTATE(RECEIVE)                    
AMQ8417I: Display Channel Status details.
   CHANNEL(QMR01.QM01)                     CHLTYPE(SDR)
   CONNAME(52.86.146.171(1414))            CURRENT
   RQMNAME(QM01)                           STATUS(RUNNING)
   SUBSTATE(MQGET)                         XMITQ(QM01)


ALTER CHANNEL(QMR01.QM01) CHLTYPE(SDR) SSLCIPH(TLS_RSA_WITH_AES_256_CBC_SHA256)
ALTER CHANNEL(QM01.QMR01) CHLTYPE(RCVR) SSLCIPH(TLS_RSA_WITH_AES_256_CBC_SHA256)
REFRESH SECURITY TYPE(SSL)

```


