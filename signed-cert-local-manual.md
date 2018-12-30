```


Automation

auto - create csr request - and stashed put in s3. label is required.
manual - upload root, intermediate, external root, and host cert to s3/github
provision -



===============
5th attempt - using ibm mq v8 - it will also work in v9

east

runmqakm -keydb -create -db key.kdb -pw 12345 -type cms -stash
runmqakm -cert -list -db key.kdb -stashed
runmqakm -certreq -create -db key.kdb -stashed -label ibmwebspheremqqm01 -dn "CN=mqfelloweast.host,O=MQFellow,OU=IT,L=Springfield,ST=Illinois,C=US" -size 2048 -file mqfelloweast.csr
runmqakm -certreq -list -db key.kdb -stashed

openssl x509 -text -noout -in bundle1.txt - COMODO RSA Domain Validation Secure Server CA
openssl x509 -text -noout -in bundle2.txt - COMODO RSA Certification Authority
openssl x509 -text -noout -in bundle3.txt - AddTrust External CA Root


runmqakm -cert -add -db key.kdb -label "COMODO RSA Domain Validation Secure Server CA" -file bundle1.txt -stashed

runmqakm -cert -add -db key.kdb -label "COMODO RSA Certification Authority" -file bundle2.txt -stashed

runmqakm -cert -add -db key.kdb -label "AddTrust External CA Root" -file bundle3.txt -stashed

runmqakm -cert -receive -db key.kdb -file mqfelloweast_host.crt -stashed -format ascii 

$ runmqakm -cert -receive -db key.kdb -file mqfelloweast_host.crt -stashed -format ascii 
bash-4.2$ runmqakm -cert -list -db key.kdb -stashed
Certificates found
* default, - personal, ! trusted, # secret key
!   "COMODO RSA Domain Validation Secure Server CA"
!   "COMODO RSA Certification Authority"
!   "AddTrust External CA Root"
-   ibmwebspheremqqm01

west

runmqakm -keydb -create -db key.kdb -pw 12345 -type cms -stash
runmqakm -cert -list -db key.kdb -stashed
runmqakm -certreq -create -db key.kdb -stashed -label ibmwebspheremqqmr01 -dn "CN=mqfellowwest.host,O=MQFellow,OU=IT,L=Springfield,ST=Illinois,C=US" -size 2048 -file mqfellowwest.csr
runmqakm -certreq -list -db key.kdb -stashed
runmqakm -cert -add -db key.kdb -label "COMODO RSA Domain Validation Secure Server CA" -file bundle1.txt -stashed
runmqakm -cert -add -db key.kdb -label "COMODO RSA Certification Authority" -file bundle2.txt -stashed
runmqakm -cert -add -db key.kdb -label "AddTrust External CA Root" -file bundle3.txt -stashed
runmqakm -cert -receive -db key.kdb -file mqfellowwest_host.crt -stashed -format ascii 
runmqakm -cert -list -db key.kdb -stashed

$ runmqakm -cert -list -db key.kdb -stashed
Certificates found
* default, - personal, ! trusted, # secret key
!   "COMODO RSA Domain Validation Secure Server CA"
!   "COMODO RSA Certification Authority"
!   "AddTrust External CA Root"
-   ibmwebspheremqqmr01


REFRESH SECURITY TYPE (SSL) 
and
endmqm -w QMR01;strmqm QMR01


The fix - This is missed -> "AddTrust External CA Root". It only shows up in namecheap dashboard. Relying on the attached file in the email did not work as it is only 2 certs.



EAST

DIS CHS(*) ALL
    12 : DIS CHS(*) ALL
AMQ8417: Display Channel Status details.
   CHANNEL(QM01.QMR01)                     CHLTYPE(SDR)
   BATCHES(0)                              BATCHSZ(50)
   BUFSRCVD(0)                             BUFSSENT(0)
   BYTSRCVD(0)                             BYTSSENT(0)
   CHSTADA(2018-12-30)                     CHSTATI(06.44.54)
   COMPHDR(NONE,NONE)                      COMPMSG(NONE,NONE)
   COMPRATE(0,0)                           COMPTIME(0,0)
   CONNAME(52.42.174.228(9000))            CURLUWID(0000000000000000)
   CURMSGS(0)                              CURRENT
   CURSEQNO(0)                             EXITTIME(0,0)
   HBINT(300)                              INDOUBT(NO)
   JOBNAME(0000332D00000001)               LOCLADDR( )
   LONGRTS(999999999)                      LSTLUWID(0000000000000000)
   LSTMSGDA( )                             LSTMSGTI( )
   LSTSEQNO(0)                             MCASTAT(RUNNING)
   MONCHL(OFF)                             MSGS(0)
   NETTIME(0,0)                            NPMSPEED(FAST)
   RQMNAME( )                              SHORTRTS(10)
   SECPROT(NONE)                           SSLCERTI( )
   SSLKEYDA( )                             SSLKEYTI( )
   SSLPEER( )                              SSLRKEYS(0)
   STATUS(BINDING)                         STOPREQ(NO)
   SUBSTATE(NETCONNECT)                    XBATCHSZ(0,0)
   XMITQ(QMR01)                            XQTIME(0,0)
   RVERSION( )                             RPRODUCT( )
AMQ8417: Display Channel Status details.
   CHANNEL(QMR01.QM01)                     CHLTYPE(RCVR)
   BATCHES(0)                              BATCHSZ(50)
   BUFSRCVD(1)                             BUFSSENT(1)
   BYTSRCVD(268)                           BYTSSENT(268)
   CHSTADA(2018-12-30)                     CHSTATI(06.45.52)
   COMPHDR(NONE,NONE)                      COMPMSG(NONE,NONE)
   COMPRATE(0,0)                           COMPTIME(0,0)
   CONNAME(52.42.174.228)                  CURLUWID(0000000000000000)
   CURMSGS(0)                              CURRENT
   CURSEQNO(0)                             EXITTIME(0,0)
   HBINT(300)                              INDOUBT(NO)
   JOBNAME(000032E100000014)               LOCLADDR(::ffff:172.17.1.212(1414))
   LSTLUWID(0000000000000000)              LSTMSGDA( )
   LSTMSGTI( )                             LSTSEQNO(0)
   MCASTAT(RUNNING)                        MCAUSER(mqm)
   MONCHL(OFF)                             MSGS(0)
   NPMSPEED(FAST)                          RQMNAME(QMR01)
   SECPROT(TLSV12)                      
   SSLCERTI(CN=COMODO RSA Domain Validation Secure Server CA,O=COMODO CA Limited,L=Salford,ST=Greater Manchester,C=GB)
   SSLKEYDA( )                             SSLKEYTI( )
   SSLPEER(SERIALNUMBER=00:94:71:A1:9C:58:86:E3:7E:F5:92:4C:08:3B:F2:B3:AB,CN=mqfellowwest.host,OU=PositiveSSL,OU=Domain Control Validated,UNSTRUCTUREDNAME=www.mqfellowwest.host,UNSTRUCTUREDNAME=mqfellowwest.host)
   SSLRKEYS(0)                             STATUS(RUNNING)
   STOPREQ(NO)                             SUBSTATE(RECEIVE)
   XBATCHSZ(0,0)                           RVERSION(08000004)
   RPRODUCT(MQMM)             

WEST

DIS CHS(*) ALL
     9 : DIS CHS(*) ALL
AMQ8417: Display Channel Status details.
   CHANNEL(QMR01.QM01)                     CHLTYPE(SDR)
   BATCHES(0)                              BATCHSZ(50)
   BUFSRCVD(1)                             BUFSSENT(1)
   BYTSRCVD(268)                           BYTSSENT(268)
   CHSTADA(2018-12-30)                     CHSTATI(06.45.51)
   COMPHDR(NONE,NONE)                      COMPMSG(NONE,NONE)
   COMPRATE(0,0)                           COMPTIME(0,0)
   CONNAME(52.86.146.171(1414))            CURLUWID(3665285C01010010)
   CURMSGS(0)                              CURRENT
   CURSEQNO(0)                             EXITTIME(0,0)
   HBINT(300)                              INDOUBT(NO)
   JOBNAME(0000335700000001)               LOCLADDR(172.17.1.133(35088))
   LONGRTS(999999999)                      LSTLUWID(0000000000000000)
   LSTMSGDA( )                             LSTMSGTI( )
   LSTSEQNO(0)                             MCASTAT(RUNNING)
   MONCHL(OFF)                             MSGS(0)
   NETTIME(0,0)                            NPMSPEED(FAST)
   RQMNAME(QM01)                           SHORTRTS(8)
   SECPROT(TLSV12)                      
   SSLCERTI(CN=COMODO RSA Domain Validation Secure Server CA,O=COMODO CA Limited,L=Salford,ST=Greater Manchester,C=GB)
   SSLKEYDA( )                             SSLKEYTI( )
   SSLPEER(SERIALNUMBER=00:98:7E:0C:BD:64:65:42:79:DC:9D:8C:17:45:06:35:08,CN=mqfelloweast.host,OU=PositiveSSL,OU=Domain Control Validated,UNSTRUCTUREDNAME=www.mqfelloweast.host,UNSTRUCTUREDNAME=mqfelloweast.host)
   SSLRKEYS(0)                             STATUS(RUNNING)
   STOPREQ(NO)                             SUBSTATE(MQGET)
   XBATCHSZ(0,0)                           XMITQ(QM01)
   XQTIME(0,0)                             RVERSION(08000004)
   RPRODUCT(MQMM)                



===============
4th attempt


runmqakm -keydb -create -db key.kdb -pw 12345 -type cms -stash
runmqakm -cert -list -db key.kdb -stashed
runmqakm -certreq -create -db key.kdb -stashed -label ibmwebspheremqqm01 -dn "CN=mqfelloweast.host,O=MQFellow,OU=IT,L=Springfield,ST=Illinois,C=US" -size 2048 -file mqfelloweast.csr
runmqakm -certreq -list -db key.kdb -stashed
runmqakm -cert -add -db key.kdb -label "COMODO RSA Domain Validation Secure Server CA" -file bundle1.txt -stashed
runmqakm -cert -add -db key.kdb -label "COMODO RSA Certification Authority" -file bundle2.txt -stashed
runmqakm -cert -receive -db key.kdb -file mqfelloweast_host.crt -stashed -format ascii 

==========

Third attempt

east

runmqakm -keydb -create -db key.kdb -pw 12345 -type cms -stash
runmqakm -cert -list -db key.kdb -stashed
runmqakm -certreq -create -db key.kdb -stashed -label ibmwebspheremqqm01 -dn "CN=www.mqfelloweast.host,O=MQFellow,OU=IT,L=Springfield,ST=Illinois,C=US" -size 2048 -file mqfelloweast.csr
runmqakm -certreq -list -db key.kdb -stashed
runmqakm -cert -add -db key.kdb -label "COMODO RSA Domain Validation Secure Server CA" -file bundle1.txt -stashed
runmqakm -cert -add -db key.kdb -label "COMODO RSA Certification Authority" -file bundle2.txt -stashed

runmqakm -cert -receive -db key.kdb -file www_mqfelloweast_host.crt -stashed -format ascii 
CTGSK2146W An invalid certificate chain was found.
Additional untranslated info: No certificate chain built
Additional untranslated info: GSKKM_VALIDATIONFAIL_SUBJECT: [Class=]GSKVALMethod::PKIX[Issuer=]CN=COMODO RSA Domain Validation Secure Server CA,O=COMODO CA Limited,L=Salford,ST=Greater Manchester,C=GB[#=]00df08a8fbc3b221c1270aee04eaaa8ee5[Subject=]CN=www.mqfelloweast.host,OU=PositiveSSL,OU=Domain Control Validated
CTGSK2043W Key entry validation failed.

runmqakm -cert -list -db key.kdb -stashed
Certificates found
* default, - personal, ! trusted, # secret key
!   "COMODO RSA Domain Validation Secure Server CA"
!   "COMODO RSA Certification Authority"
-   ibmwebspheremqqm01
bash-4.2$ 

endmqm -w QM01;strmqm QM01


==========
west


runmqakm -keydb -create -db key.kdb -pw 12345 -type cms -stash
runmqakm -cert -list -db key.kdb -stashed
runmqakm -certreq -create -db key.kdb -stashed -label ibmwebspheremqqmr01 -dn "CN=www.mqfellowwest.host,O=MQFellow,OU=IT,L=Springfield,ST=Illinois,C=US" -size 2048 -file mqfellowwest.csr
runmqakm -certreq -list -db key.kdb -stashed
runmqakm -cert -add -db key.kdb -label "COMODO RSA Domain Validation Secure Server CA" -file bundle1.txt -stashed
runmqakm -cert -add -db key.kdb -label "COMODO RSA Certification Authority" -file bundle2.txt -stashed

runmqakm -cert -receive -db key.kdb -file www_mqfellowwest_host.crt -stashed -format ascii 
CTGSK2146W An invalid certificate chain was found.
Additional untranslated info: No certificate chain built
Additional untranslated info: GSKKM_VALIDATIONFAIL_SUBJECT: [Class=]GSKVALMethod::PKIX[Issuer=]CN=COMODO RSA Domain Validation Secure Server CA,O=COMODO CA Limited,L=Salford,ST=Greater Manchester,C=GB[#=]00df08a8fbc3b221c1270aee04eaaa8ee5[Subject=]CN=www.mqfelloweast.host,OU=PositiveSSL,OU=Domain Control Validated
CTGSK2043W Key entry validation failed.

runmqakm -cert -list -db key.kdb -stashed
Certificates found
* default, - personal, ! trusted, # secret key
!   "COMODO RSA Domain Validation Secure Server CA"
!   "COMODO RSA Certification Authority"
-   ibmwebspheremqqm01
bash-4.2$ 

endmqm -w QMR01;strmqm QMR01

runmqakm -cert -add -db key.kdb -label "COMODORoot" -file bundle1.txt -stashed
runmqakm -cert -add -db key.kdb -label "COMODOInt" -file bundle2.txt -stashed



=======

## Using CA-signed certificates for mutual authentication of two queue managers

https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.5.0/com.ibm.mq.sec.doc/q014200_.htm

### Create key database

To create a key database by using the command line, use either of the following commands:
https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.5.0/com.ibm.mq.sec.doc/q012680_.htm

$ runmqakm -keydb -create -db key.kdb -pw ABC123456##abcd -type cms -stash -fips -strong 
bash-4.2$ ls -al
total 20
drwxr-sr-x.  2 mqm mqm   65 Dec 30 01:23 .
drwxrwsr-x. 26 mqm mqm 4096 Dec 30 01:21 ..
-rw-------.  1 mqm mqm   88 Dec 30 01:23 key.crl
-rw-------.  1 mqm mqm   88 Dec 30 01:23 key.db
-rw-------.  1 mqm mqm   88 Dec 30 01:23 key.rdb
-rw-------.  1 mqm mqm  193 Dec 30 01:23 key.sth

### Cert request

https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.5.0/com.ibm.mq.sec.doc/q012780_.htm

runmqakm -certreq -create -db key.kdb -pw ABC123456##abcd -label ibmwebspheremqqm01 -dn "CN=www.mqfelloweast.host" -size 2048 -file mqfelloweast.csr -fips -sig_alg SHA512WithRSA 

$ ls -al
total 28
drwxr-sr-x.  2 mqm mqm   90 Dec 30 01:33 .
drwxrwsr-x. 26 mqm mqm 4096 Dec 30 01:21 ..
-rw-------.  1 mqm mqm   88 Dec 30 01:33 key.crl
-rw-------.  1 mqm mqm   88 Dec 30 01:33 key.kdb
-rw-------.  1 mqm mqm 5088 Dec 30 01:33 key.rdb
-rw-------.  1 mqm mqm  193 Dec 30 01:33 key.sth
-rw-------.  1 mqm mqm  871 Dec 30 01:33 mqfelloweast.csr

### Import certificates

Receiving personal certificates into a key repository on UNIX, Linux and Windows systems

Adding a CA certificate (or the public part of a self-signed certificate) into a key repository, on UNIX, Linux, and Windows systems

Root Cert
$ runmqckm -cert -add -db key.kdb -pw ABC123456##abcd -label "COMODO RSA Domain Validation Secure Server CA" -file bundle1.txt -format ascii

Intermediate
$ runmqckm -cert -add -db key.kdb -pw ABC123456##abcd -label "COMODO RSA Certification Authority" -file bundle2.txt -format ascii

Add the CA-signed certificate to the key repository for each queue manager

$ runmqckm -cert -receive -file www_mqfelloweast_host.crt -db key.kdb -pw ABC123456##abcd -format ascii 
Warning: Validation failed: Missing intermediate or root certificate.

Validate

$ runmqakm -cert -list -db key.kdb -pw ABC123456##abcd
Certificates found
* default, - personal, ! trusted, # secret key
!   "COMODO RSA Domain Validation Secure Server CA"
!   "COMODO RSA Certification Authority"
-   ibmwebspheremqqm01
bash-4.2$ 


==============


runmqakm -keydb -create -db key.kdb -pw ABC123456##abcd -type cms -stash -fips -strong 
runmqakm -certreq -create -db key.kdb -pw ABC123456##abcd -label ibmwebspheremqqmr01 -dn "CN=www.mqfellowwest.host" -size 2048 -file mqfellowwest.csr -fips -sig_alg SHA512WithRSA 
runmqckm -cert -add -db key.kdb -pw ABC123456##abcd -label "COMODO RSA Domain Validation Secure Server CA" -file bundle1.txt -format ascii
runmqckm -cert -add -db key.kdb -pw ABC123456##abcd -label "COMODO RSA Certification Authority" -file bundle2.txt -format ascii
runmqckm -cert -receive -file www_mqfellowwest_host.crt -db key.kdb -pw ABC123456##abcd -format ascii 
$ runmqakm -cert -list -db key.kdb -pw ABC123456##abcd
Certificates found
* default, - personal, ! trusted, # secret key
!   "COMODO RSA Domain Validation Secure Server CA"
!   "COMODO RSA Certification Authority"
-   ibmwebspheremqqmr01


===============





### Generate CSR

$ rm -rf key* mq*
$ runmqakm -keydb -create -db key.kdb -pw 12345 -stash
bash-4.2$ ls -al
total 16
drwxr-sr-x. 2 mqm mqm  66 Dec 29 21:42 .
drwxrws---. 3 mqm mqm 102 Dec 29 19:42 ..
-rw-------. 1 mqm mqm  88 Dec 29 21:42 key.crl
-rw-------. 1 mqm mqm  88 Dec 29 21:42 key.kdb
-rw-------. 1 mqm mqm  88 Dec 29 21:42 key.rdb
-rw-------. 1 mqm mqm 193 Dec 29 21:42 key.sth

$ runmqakm -cert -list -db key.kdb -pw 12345
No certificates were found.

runmqckm, and runmqakm commands
https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.5.0/com.ibm.mq.ref.adm.doc/q083820_.htm

$ runmqakm -certreq -create -db key.kdb -label ibmwebspheremqqm01 -size 2048 -sig_alg SHA512WithRSA -san_dnsname www.mqfelloweast.host -dn CN=www.mqfelloweast.host -file mqfelloweast.csr -pw 12345
bash-4.2$ ls -al
total 24
drwxr-sr-x. 2 mqm mqm   90 Dec 29 21:42 .
drwxrws---. 3 mqm mqm  102 Dec 29 19:42 ..
-rw-------. 1 mqm mqm   88 Dec 29 21:42 key.crl
-rw-------. 1 mqm mqm   88 Dec 29 21:42 key.kdb
-rw-------. 1 mqm mqm 5088 Dec 29 21:42 key.rdb
-rw-------. 1 mqm mqm  193 Dec 29 21:42 key.sth
-rw-------. 1 mqm mqm  984 Dec 29 21:42 mqfelloweast.csr

$ runmqakm -cert -list -db key.kdb -pw 12345
No certificates were found.

$ runmqakm -certreq -list -db key.kdb -pw 12345
Certificates requests found
	ibmwebspheremqqm01

$ runmqakm -certreq -details -db key.kdb -pw 12345 -label ibmwebspheremqqm01
Label : ibmwebspheremqqm01
Key Size : 2048
Subject : CN=www.mqfelloweast.host
Public Key
    30 82 01 22 30 0D 06 09 2A 86 48 86 F7 0D 01 01
    01 05 00 03 82 01 0F 00 30 82 01 0A 02 82 01 01
    00 BD 34 25 BE BD 5A D4 2E C8 0D A8 6F 92 85 E9
    D4 11 01 F9 11 CB C5 83 07 1D CA 7F B0 50 E2 23
    1C 5A D3 E5 57 C2 32 6B 30 A9 D6 97 97 D0 CC 5D
    0B 89 63 2F EC 2A 2E E4 BC 47 3A D0 12 D3 7E D2
    63 E5 C0 2C 69 66 32 BF DD 69 D2 82 51 51 FC A0
    21 41 FE 36 34 B2 1D 10 1D 25 F3 18 15 59 53 3B
    B9 05 4D 80 39 18 21 82 63 E8 65 60 31 8D 3D A9
    F6 22 92 51 E3 E7 38 C1 2A A4 F9 51 7B BE C0 21
    A8 51 D8 A6 94 CA 76 6A 16 C5 44 38 37 9D 20 93
    48 66 44 F2 17 D2 62 55 10 49 6B 34 69 C3 D2 E4
    19 D5 03 B5 1C F4 BF 59 03 94 E3 7F 3B D5 E2 1E
    7D 12 70 07 47 B0 36 1B 7C 1D 3C 05 D9 56 26 53
    FB DD 98 C2 5A B2 08 A3 27 0F 27 23 5A FE 40 60
    58 5A 86 5A F9 8F 7A F0 47 58 22 8B F2 96 6A 07
    34 A5 D4 2D 4A 52 BE 5F 5C 9C 2F E4 B3 C5 9E 62
    D4 78 6F 9C DB BE EC 8F 8C AD D7 DF C8 D6 EC A9
    99 02 03 01 00 01
Public Key Type : RSA (1.2.840.113549.1.1.1)
Fingerprint : 
91ac82e8e096b685f382466e55a4bdbe
55a9911d
Attributes
Extension Reqs
Extensions
    subjectAlternativeName
        dNSName: www.mqfelloweast.host
Signature Algorithm : SHA512WithRSASignature (1.2.840.113549.1.1.13)
Value
    8E 3B B9 52 15 AA F7 D3 2A 00 E9 63 E3 3F 0D 04
    83 F1 16 FC 88 E3 8B E7 C4 7E 85 54 5C 14 6E 2E
    95 FB BF 93 58 3F 20 A5 66 82 9C 2D 75 FF EE 15
    79 59 DF D6 9E 09 5E F7 03 6C 2B 9C 9F AA 58 E5
    43 CA 1C 60 D0 AA 92 7B AF 1D C7 9D 5E 6C E0 20
    D6 1C FF 52 1A A2 DE A1 DA B3 91 C9 23 26 2F D6
    11 22 EB 5A AF BB 24 18 C5 FF A5 AC C8 46 64 99
    25 9B D5 E3 3A B9 11 83 41 FA DC A7 B6 7C 30 78
    63 0F 99 AE EB C4 0D D9 54 74 D5 A5 3F 47 E6 D7
    BB 40 61 0A 34 24 33 FD 45 27 68 74 A9 2D 29 F2
    45 B1 B3 3B B4 36 85 CC 86 AA 1F 61 C4 F0 19 C7
    DE A6 21 8D 4F 50 E2 73 B5 24 CA 9F C3 B2 F5 5C
    2F 5C E9 E4 39 73 6C 82 92 6C 79 B9 F7 40 3C 6D
    EE 4E E0 42 06 DE 66 09 C6 92 F6 2E 5E CF BC A5
    A6 D1 9D 8B 6D 11 5F B7 BC 20 7D A0 64 80 7B D2
    D8 7F 83 66 84 B3 08 7A C7 17 13 A2 17 F3 59 94
bash-4.2$ 

$ cat mqfellowwest.csr 
-----BEGIN NEW CERTIFICATE REQUEST-----
MIICmDCCAYACAQAwIDEeMBwGA1UEAxMVd3d3Lm1xZmVsbG93ZWFzdC5ob3N0MIIB
IjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAvTQlvr1a1C7IDahvkoXp1BEB
+RHLxYMHHcp/sFDiIxxa0+VXwjJrMKnWl5fQzF0LiWMv7Cou5LxHOtAS037SY+XA
LGlmMr/dadKCUVH8oCFB/jY0sh0QHSXzGBVZUzu5BU2AORghgmPoZWAxjT2p9iKS
UePnOMEqpPlRe77AIahR2KaUynZqFsVEODedIJNIZkTyF9JiVRBJazRpw9LkGdUD
tRz0v1kDlON/O9XiHn0ScAdHsDYbfB08BdlWJlP73ZjCWrIIoycPJyNa/kBgWFqG
WvmPevBHWCKL8pZqBzSl1C1KUr5fXJwv5LPFnmLUeG+c277sj4yt19/I1uypmQID
AQABoDMwMQYJKoZIhvcNAQkOMSQwIjAgBgNVHREEGTAXghV3d3cubXFmZWxsb3dl
YXN0Lmhvc3QwDQYJKoZIhvcNAQENBQADggEBAI47uVIVqvfTKgDpY+M/DQSD8Rb8
iOOL58R+hVRcFG4ulfu/k1g/IKVmgpwtdf/uFXlZ39aeCV73A2wrnJ+qWOVDyhxg
0KqSe68dx51ebOAg1hz/Uhqi3qHas5HJIyYv1hEi61qvuyQYxf+lrMhGZJklm9Xj
OrkRg0H63Ke2fDB4Yw+ZruvEDdlUdNWlP0fm17tAYQo0JDP9RSdodKktKfJFsbM7
tDaFzIaqH2HE8BnH3qYhjU9Q4nO1JMqfw7L1XC9c6eQ5c2yCkmx5ufdAPG3uTuBC
Bt5mCcaS9i5ez7ylptGdi20RX7e8IH2gZIB70th/g2aEswh6xxcTohfzWZQ=
-----END NEW CERTIFICATE REQUEST-----

### Signing request to namecheap.com 

https://www.namecheap.com/support/knowledgebase/article.aspx/467/67/how-to-generate-csr-certificate-signing-request-code
https://ap.www.namecheap.com/domains/ssl/activate/5603594/domainName/productlist

In namecheap dashboard, ensure that REDIRECT EMAIL - Catch All for the domain is enabled and pointed to your email address. It will allow you to receive the CSR validation email.

Domains secured: www.mqfelloweast.host

Check if we got your server right: Any other server (cPanel, Apache, NGINX, etc.)

Next

DCV method: Email

Approver email: admin@mqfelloweast.host

Next

Admin email: your-email

Next 

You will get instructions on how to complete domain control validation (DCV) on the next step. When you complete DCV, certificate authority will check your request and issue SSL according to submitted information

SSL will be sent to: your-email

Domains secured & DCV method: www.mqfelloweast.host
get email at admin@mqfelloweast.host

Submit

Great! We initiated the activation of your certificate. To complete the process, you need to finalize the Domain Control Validation procedure.

If you’re using email DCV method, follow instruction from the certificate authority in your inbox.
If you’re using HTTP-based DCV method, go to Certificate Details page to download the validation file.
If you’re using DNS-based DCV method, go to Certificate Details page to get the necessary host records.

On the email:

ORDER #196079145 - Domain Control Validation for www.mqfelloweast.host

Follow the instruction mentioned.

Afterwards, on the email - ORDER #196079145 - Your PositiveSSL Certificate for www.mqfelloweast.host

Download the attached file - www_mqfelloweast_host.zip

www_mqfelloweast_host.ca-bundle
www_mqfelloweast_host.crt

### Import the certificate

Source intruction - https://www.youtube.com/watch?v=rB_DLMm4tOY&list=PLpFOFgc5UfWE4pHHIweA8TGF-agIG42TT&index=5

Copy the files to /var/mqm/qmgrs/QMR01/ssl/signed-ssl - Using terminal or S3

www_mqfelloweast_host.ca-bundle
www_mqfelloweast_host.crt

$ ls -al
total 36
drwxr-sr-x. 2 mqm mqm  162 Dec 29 22:13 .
drwxrws---. 3 mqm mqm  102 Dec 29 19:42 ..
-rw-------. 1 mqm mqm   88 Dec 29 21:42 key.crl
-rw-------. 1 mqm mqm   88 Dec 29 21:42 key.kdb
-rw-------. 1 mqm mqm 5088 Dec 29 21:42 key.rdb
-rw-------. 1 mqm mqm  193 Dec 29 21:42 key.sth
-rw-------. 1 mqm mqm  984 Dec 29 21:42 mqfelloweast.csr
-rw-r--r--. 1 mqm mqm 4104 Dec 29 22:12 www_mqfelloweast_host.ca-bundle
-rw-r--r--. 1 mqm mqm 2277 Dec 29 22:12 www_mqfelloweast_host.crt

$ openssl x509 -text -noout -in www_mqfelloweast_host.crt
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            93:fc:3d:84:80:ca:5c:67:37:71:21:bc:d5:62:e0:fd
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=GB, ST=Greater Manchester, L=Salford, O=COMODO CA Limited, CN=COMODO RSA Domain Validation Secure Server CA
        Validity
            Not Before: Dec 29 00:00:00 2018 GMT
            Not After : Dec 29 23:59:59 2019 GMT
        Subject: OU=Domain Control Validated, OU=PositiveSSL, CN=www.mqfellowwest.host
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:b5:4a:e6:f5:3f:7e:01:79:1e:39:de:0e:ef:73:
                    9c:fa:0e:67:57:e1:88:8d:83:63:c1:1c:ff:2c:61:
                    63:5f:9b:8d:6f:d0:7f:69:2d:2b:76:f0:de:9a:4c:
                    ae:a0:87:fb:40:76:e9:49:8e:3a:99:2d:a4:5d:31:
                    11:6e:0f:56:b7:36:57:d8:28:82:47:e5:38:d7:47:
                    6a:9b:7e:09:5d:9c:68:73:c6:dc:a1:89:ba:cb:2b:
                    26:ea:24:9b:c7:0c:95:c7:ef:f9:d7:21:b1:0b:d6:
                    22:27:e3:65:82:e4:a0:16:9f:af:b0:8e:b3:ca:ea:
                    c8:90:8d:5c:3f:8c:8d:ae:7f:ef:63:2d:db:7e:4b:
                    ce:31:98:9f:16:8b:89:61:06:4c:a5:cf:de:4b:36:
                    13:0c:60:a6:ba:d3:db:79:62:83:82:65:cc:fe:43:
                    49:31:42:e6:74:69:25:26:fc:70:0c:6e:3c:19:f9:
                    50:6f:0c:1c:36:4c:73:64:60:8c:7c:34:16:67:35:
                    54:76:d5:33:95:97:b1:08:c0:7b:2b:3b:4d:ed:76:
                    e3:ac:be:89:61:63:1b:3f:b2:36:04:42:bd:4a:3e:
                    23:d5:ee:1a:0a:57:39:b4:fa:8b:51:68:ce:2e:9e:
                    05:45:f6:88:86:d7:8d:35:ef:b3:bc:89:05:da:cd:
                    47:8b
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Authority Key Identifier: 
                keyid:90:AF:6A:3A:94:5A:0B:D8:90:EA:12:56:73:DF:43:B4:3A:28:DA:E7

            X509v3 Subject Key Identifier: 
                67:E1:03:66:27:68:61:CA:2A:BB:1C:5A:6C:D6:7F:4D:47:77:08:12
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Basic Constraints: critical
                CA:FALSE
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication, TLS Web Client Authentication
            X509v3 Certificate Policies: 
                Policy: 1.3.6.1.4.1.6449.1.2.2.7
                  CPS: https://secure.comodo.com/CPS
                Policy: 2.23.140.1.2.1

            X509v3 CRL Distribution Points: 

                Full Name:
                  URI:http://crl.comodoca.com/COMODORSADomainValidationSecureServerCA.crl

            Authority Information Access: 
                CA Issuers - URI:http://crt.comodoca.com/COMODORSADomainValidationSecureServerCA.crt
                OCSP - URI:http://ocsp.comodoca.com

            X509v3 Subject Alternative Name: 
                DNS:www.mqfellowwest.host, DNS:mqfellowwest.host
            CT Precertificate SCTs: 
                Signed Certificate Timestamp:
                    Version   : v1(0)
                    Log ID    : BB:D9:DF:BC:1F:8A:71:B5:93:94:23:97:AA:92:7B:47:
                                38:57:95:0A:AB:52:E8:1A:90:96:64:36:8E:1E:D1:85
                    Timestamp : Dec 29 22:02:14.137 2018 GMT
                    Extensions: none
                    Signature : ecdsa-with-SHA256
                                30:46:02:21:00:93:A0:D8:A3:AC:99:76:5E:AD:38:40:
                                7B:1F:63:41:3B:83:FE:07:E1:FA:31:04:E9:AE:98:57:
                                51:F9:8D:9D:89:02:21:00:DB:75:FA:FC:21:B3:3A:0A:
                                01:55:05:4C:E4:F7:BC:0A:37:90:F2:A7:F5:68:56:EC:
                                74:6B:23:47:B4:32:2F:57
                Signed Certificate Timestamp:
                    Version   : v1(0)
                    Log ID    : 74:7E:DA:83:31:AD:33:10:91:21:9C:CE:25:4F:42:70:
                                C2:BF:FD:5E:42:20:08:C6:37:35:79:E6:10:7B:CC:56
                    Timestamp : Dec 29 22:02:14.242 2018 GMT
                    Extensions: none
                    Signature : ecdsa-with-SHA256
                                30:45:02:20:78:DA:AE:75:12:3E:6D:60:6A:3F:8B:A9:
                                9D:D0:1D:4F:ED:37:CC:C4:2A:71:29:40:55:3D:D0:22:
                                59:B6:97:FD:02:21:00:97:4D:A9:4D:FF:DC:78:52:3C:
                                F6:B9:A4:14:0A:36:30:65:CE:44:86:2B:B1:15:01:A8:
                                49:DA:2A:E0:77:AB:0D
    Signature Algorithm: sha256WithRSAEncryption
         0f:53:09:c7:63:5e:bd:8d:57:26:6f:2f:43:71:74:20:2f:b2:
         8c:98:19:f1:95:97:20:ff:3d:2c:0b:68:31:e3:bf:b8:5f:25:
         ef:75:9a:19:53:4a:96:02:dc:12:ab:34:28:e0:75:1b:39:3e:
         b2:ba:21:4c:05:ff:25:de:75:f4:8b:22:2d:9d:f4:64:91:7e:
         b7:0d:6d:05:72:d3:a2:57:16:bb:b9:18:41:1d:3f:b1:c6:fa:
         5d:50:f0:05:ad:39:a3:fc:97:21:43:f9:7d:be:6d:2c:9b:e4:
         71:a5:f5:cd:66:c5:74:c8:4d:ab:01:35:f3:99:ce:fe:8a:20:
         03:df:79:62:31:12:17:0f:b0:fd:2c:8a:66:6d:b1:b2:db:46:
         de:19:38:b5:dd:2d:e9:61:76:ea:f8:1a:57:89:84:eb:75:ae:
         ec:36:b5:44:84:32:47:ab:35:78:ce:c0:d1:ce:c8:22:fa:6e:
         a6:b0:d5:5b:2b:d4:e5:f8:cd:ff:45:8a:1d:24:20:b4:db:ba:
         b7:54:bb:56:c2:c7:9d:fb:61:b5:24:8d:13:1b:59:cb:ed:20:
         6e:ad:39:39:56:08:8b:7d:cd:b7:9b:ad:a5:a3:03:af:fb:06:
         f4:82:cf:12:02:75:a3:41:7f:65:dc:18:46:85:68:87:bf:fd:
         43:68:89:27
bash-4.2$ 

Extract the bundle into three files. bundle1.txt and bundle2.txt

$ ls -al
total 44
drwxr-sr-x. 2 mqm mqm  200 Dec 29 22:19 .
drwxrws---. 3 mqm mqm  102 Dec 29 19:42 ..
-rw-r--r--. 1 mqm mqm 2151 Dec 29 22:19 bundle1.txt
-rw-r--r--. 1 mqm mqm 1952 Dec 29 22:19 bundle2.txt
-rw-------. 1 mqm mqm   88 Dec 29 21:42 key.crl
-rw-------. 1 mqm mqm   88 Dec 29 21:42 key.kdb
-rw-------. 1 mqm mqm 5088 Dec 29 21:42 key.rdb
-rw-------. 1 mqm mqm  193 Dec 29 21:42 key.sth
-rw-------. 1 mqm mqm  984 Dec 29 21:42 mqfellowwest.csr
-rw-r--r--. 1 mqm mqm 4104 Dec 29 22:12 www_mqfelloweast_host.ca-bundle
-rw-r--r--. 1 mqm mqm 2277 Dec 29 22:12 www_mqfelloweast_host.crt
bash-4.2$ 

Identify which one is the root and intermediate

$ openssl x509 -text -noout -in bundle1.txt
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            2b:2e:6e:ea:d9:75:36:6c:14:8a:6e:db:a3:7c:8c:07
    Signature Algorithm: sha384WithRSAEncryption
        Issuer: C=GB, ST=Greater Manchester, L=Salford, O=COMODO CA Limited, CN=COMODO RSA Certification Authority
        Validity
            Not Before: Feb 12 00:00:00 2014 GMT
            Not After : Feb 11 23:59:59 2029 GMT
        Subject: C=GB, ST=Greater Manchester, L=Salford, O=COMODO CA Limited, CN=COMODO RSA Domain Validation Secure Server CA
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:8e:c2:02:19:e1:a0:59:a4:eb:38:35:8d:2c:fd:
                    01:d0:d3:49:c0:64:c7:0b:62:05:45:16:3a:a8:a0:
                    c0:0c:02:7f:1d:cc:db:c4:a1:6d:77:03:a3:0f:86:
                    f9:e3:06:9c:3e:0b:81:8a:9b:49:1b:ad:03:be:fa:
                    4b:db:8c:20:ed:d5:ce:5e:65:8e:3e:0d:af:4c:c2:
                    b0:b7:45:5e:52:2f:34:de:48:24:64:b4:41:ae:00:
                    97:f7:be:67:de:9e:d0:7a:a7:53:80:3b:7c:ad:f5:
                    96:55:6f:97:47:0a:7c:85:8b:22:97:8d:b3:84:e0:
                    96:57:d0:70:18:60:96:8f:ee:2d:07:93:9d:a1:ba:
                    ca:d1:cd:7b:e9:c4:2a:9a:28:21:91:4d:6f:92:4f:
                    25:a5:f2:7a:35:dd:26:dc:46:a5:d0:ac:59:35:8c:
                    ff:4e:91:43:50:3f:59:93:1e:6c:51:21:ee:58:14:
                    ab:fe:75:50:78:3e:4c:b0:1c:86:13:fa:6b:98:bc:
                    e0:3b:94:1e:85:52:dc:03:93:24:18:6e:cb:27:51:
                    45:e6:70:de:25:43:a4:0d:e1:4a:a5:ed:b6:7e:c8:
                    cd:6d:ee:2e:1d:27:73:5d:dc:45:30:80:aa:e3:b2:
                    41:0b:af:bd:44:87:da:b9:e5:1b:9d:7f:ae:e5:85:
                    82:a5
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Authority Key Identifier: 
                keyid:BB:AF:7E:02:3D:FA:A6:F1:3C:84:8E:AD:EE:38:98:EC:D9:32:32:D4

            X509v3 Subject Key Identifier: 
                90:AF:6A:3A:94:5A:0B:D8:90:EA:12:56:73:DF:43:B4:3A:28:DA:E7
            X509v3 Key Usage: critical
                Digital Signature, Certificate Sign, CRL Sign
            X509v3 Basic Constraints: critical
                CA:TRUE, pathlen:0
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication, TLS Web Client Authentication
            X509v3 Certificate Policies: 
                Policy: X509v3 Any Policy
                Policy: 2.23.140.1.2.1

            X509v3 CRL Distribution Points: 

                Full Name:
                  URI:http://crl.comodoca.com/COMODORSACertificationAuthority.crl

            Authority Information Access: 
                CA Issuers - URI:http://crt.comodoca.com/COMODORSAAddTrustCA.crt
                OCSP - URI:http://ocsp.comodoca.com

    Signature Algorithm: sha384WithRSAEncryption
         4e:2b:76:4f:92:1c:62:36:89:ba:77:c1:27:05:f4:1c:d6:44:
         9d:a9:9a:3e:aa:d5:66:66:01:3e:ea:49:e6:a2:35:bc:fa:f6:
         dd:95:8e:99:35:98:0e:36:18:75:b1:dd:dd:50:72:7c:ae:dc:
         77:88:ce:0f:f7:90:20:ca:a3:67:2e:1f:56:7f:7b:e1:44:ea:
         42:95:c4:5d:0d:01:50:46:15:f2:81:89:59:6c:8a:dd:8c:f1:
         12:a1:8d:3a:42:8a:98:f8:4b:34:7b:27:3b:08:b4:6f:24:3b:
         72:9d:63:74:58:3c:1a:6c:3f:4f:c7:11:9a:c8:a8:f5:b5:37:
         ef:10:45:c6:6c:d9:e0:5e:95:26:b3:eb:ad:a3:b9:ee:7f:0c:
         9a:66:35:73:32:60:4e:e5:dd:8a:61:2c:6e:52:11:77:68:96:
         d3:18:75:51:15:00:1b:74:88:dd:e1:c7:38:04:43:28:e9:16:
         fd:d9:05:d4:5d:47:27:60:d6:fb:38:3b:6c:72:a2:94:f8:42:
         1a:df:ed:6f:06:8c:45:c2:06:00:aa:e4:e8:dc:d9:b5:e1:73:
         78:ec:f6:23:dc:d1:dd:6c:8e:1a:8f:a5:ea:54:7c:96:b7:c3:
         fe:55:8e:8d:49:5e:fc:64:bb:cf:3e:bd:96:eb:69:cd:bf:e0:
         48:f1:62:82:10:e5:0c:46:57:f2:33:da:d0:c8:63:ed:c6:1f:
         94:05:96:4a:1a:91:d1:f7:eb:cf:8f:52:ae:0d:08:d9:3e:a8:
         a0:51:e9:c1:87:74:d5:c9:f7:74:ab:2e:53:fb:bb:7a:fb:97:
         e2:f8:1f:26:8f:b3:d2:a0:e0:37:5b:28:3b:31:e5:0e:57:2d:
         5a:b8:ad:79:ac:5e:20:66:1a:a5:b9:a6:b5:39:c1:f5:98:43:
         ff:ee:f9:a7:a7:fd:ee:ca:24:3d:80:16:c4:17:8f:8a:c1:60:
         a1:0c:ae:5b:43:47:91:4b:d5:9a:17:5f:f9:d4:87:c1:c2:8c:
         b7:e7:e2:0f:30:19:37:86:ac:e0:dc:42:03:e6:94:a8:9d:ae:
         fd:0f:24:51:94:ce:92:08:d1:fc:50:f0:03:40:7b:88:59:ed:
         0e:dd:ac:d2:77:82:34:dc:06:95:02:d8:90:f9:2d:ea:37:d5:
         1a:60:d0:67:20:d7:d8:42:0b:45:af:82:68:de:dd:66:24:37:
         90:29:94:19:46:19:25:b8:80:d7:cb:d4:86:28:6a:44:70:26:
         23:62:a9:9f:86:6f:bf:ba:90:70:d2:56:77:85:78:ef:ea:25:
         a9:17:ce:50:72:8c:00:3a:aa:e3:db:63:34:9f:f8:06:71:01:
         e2:82:20:d4:fe:6f:bd:b1

 $ openssl x509 -text -noout -in bundle2.txt
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            27:66:ee:56:eb:49:f3:8e:ab:d7:70:a2:fc:84:de:22
    Signature Algorithm: sha384WithRSAEncryption
        Issuer: C=SE, O=AddTrust AB, OU=AddTrust External TTP Network, CN=AddTrust External CA Root
        Validity
            Not Before: May 30 10:48:38 2000 GMT
            Not After : May 30 10:48:38 2020 GMT
        Subject: C=GB, ST=Greater Manchester, L=Salford, O=COMODO CA Limited, CN=COMODO RSA Certification Authority
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (4096 bit)
                Modulus:
                    00:91:e8:54:92:d2:0a:56:b1:ac:0d:24:dd:c5:cf:
                    44:67:74:99:2b:37:a3:7d:23:70:00:71:bc:53:df:
                    c4:fa:2a:12:8f:4b:7f:10:56:bd:9f:70:72:b7:61:
                    7f:c9:4b:0f:17:a7:3d:e3:b0:04:61:ee:ff:11:97:
                    c7:f4:86:3e:0a:fa:3e:5c:f9:93:e6:34:7a:d9:14:
                    6b:e7:9c:b3:85:a0:82:7a:76:af:71:90:d7:ec:fd:
                    0d:fa:9c:6c:fa:df:b0:82:f4:14:7e:f9:be:c4:a6:
                    2f:4f:7f:99:7f:b5:fc:67:43:72:bd:0c:00:d6:89:
                    eb:6b:2c:d3:ed:8f:98:1c:14:ab:7e:e5:e3:6e:fc:
                    d8:a8:e4:92:24:da:43:6b:62:b8:55:fd:ea:c1:bc:
                    6c:b6:8b:f3:0e:8d:9a:e4:9b:6c:69:99:f8:78:48:
                    30:45:d5:ad:e1:0d:3c:45:60:fc:32:96:51:27:bc:
                    67:c3:ca:2e:b6:6b:ea:46:c7:c7:20:a0:b1:1f:65:
                    de:48:08:ba:a4:4e:a9:f2:83:46:37:84:eb:e8:cc:
                    81:48:43:67:4e:72:2a:9b:5c:bd:4c:1b:28:8a:5c:
                    22:7b:b4:ab:98:d9:ee:e0:51:83:c3:09:46:4e:6d:
                    3e:99:fa:95:17:da:7c:33:57:41:3c:8d:51:ed:0b:
                    b6:5c:af:2c:63:1a:df:57:c8:3f:bc:e9:5d:c4:9b:
                    af:45:99:e2:a3:5a:24:b4:ba:a9:56:3d:cf:6f:aa:
                    ff:49:58:be:f0:a8:ff:f4:b8:ad:e9:37:fb:ba:b8:
                    f4:0b:3a:f9:e8:43:42:1e:89:d8:84:cb:13:f1:d9:
                    bb:e1:89:60:b8:8c:28:56:ac:14:1d:9c:0a:e7:71:
                    eb:cf:0e:dd:3d:a9:96:a1:48:bd:3c:f7:af:b5:0d:
                    22:4c:c0:11:81:ec:56:3b:f6:d3:a2:e2:5b:b7:b2:
                    04:22:52:95:80:93:69:e8:8e:4c:65:f1:91:03:2d:
                    70:74:02:ea:8b:67:15:29:69:52:02:bb:d7:df:50:
                    6a:55:46:bf:a0:a3:28:61:7f:70:d0:c3:a2:aa:2c:
                    21:aa:47:ce:28:9c:06:45:76:bf:82:18:27:b4:d5:
                    ae:b4:cb:50:e6:6b:f4:4c:86:71:30:e9:a6:df:16:
                    86:e0:d8:ff:40:dd:fb:d0:42:88:7f:a3:33:3a:2e:
                    5c:1e:41:11:81:63:ce:18:71:6b:2b:ec:a6:8a:b7:
                    31:5c:3a:6a:47:e0:c3:79:59:d6:20:1a:af:f2:6a:
                    98:aa:72:bc:57:4a:d2:4b:9d:bb:10:fc:b0:4c:41:
                    e5:ed:1d:3d:5e:28:9d:9c:cc:bf:b3:51:da:a7:47:
                    e5:84:53
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Authority Key Identifier: 
                keyid:AD:BD:98:7A:34:B4:26:F7:FA:C4:26:54:EF:03:BD:E0:24:CB:54:1A

            X509v3 Subject Key Identifier: 
                BB:AF:7E:02:3D:FA:A6:F1:3C:84:8E:AD:EE:38:98:EC:D9:32:32:D4
            X509v3 Key Usage: critical
                Digital Signature, Certificate Sign, CRL Sign
            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Certificate Policies: 
                Policy: X509v3 Any Policy

            X509v3 CRL Distribution Points: 

                Full Name:
                  URI:http://crl.usertrust.com/AddTrustExternalCARoot.crl

            Authority Information Access: 
                OCSP - URI:http://ocsp.usertrust.com

    Signature Algorithm: sha384WithRSAEncryption
         64:bf:83:f1:5f:9a:85:d0:cd:b8:a1:29:57:0d:e8:5a:f7:d1:
         e9:3e:f2:76:04:6e:f1:52:70:bb:1e:3c:ff:4d:0d:74:6a:cc:
         81:82:25:d3:c3:a0:2a:5d:4c:f5:ba:8b:a1:6d:c4:54:09:75:
         c7:e3:27:0e:5d:84:79:37:40:13:77:f5:b4:ac:1c:d0:3b:ab:
         17:12:d6:ef:34:18:7e:2b:e9:79:d3:ab:57:45:0c:af:28:fa:
         d0:db:e5:50:95:88:bb:df:85:57:69:7d:92:d8:52:ca:73:81:
         bf:1c:f3:e6:b8:6e:66:11:05:b3:1e:94:2d:7f:91:95:92:59:
         f1:4c:ce:a3:91:71:4c:7c:47:0c:3b:0b:19:f6:a1:b1:6c:86:
         3e:5c:aa:c4:2e:82:cb:f9:07:96:ba:48:4d:90:f2:94:c8:a9:
         73:a2:eb:06:7b:23:9d:de:a2:f3:4d:55:9f:7a:61:45:98:18:
         68:c7:5e:40:6b:23:f5:79:7a:ef:8c:b5:6b:8b:b7:6f:46:f4:
         7b:f1:3d:4b:04:d8:93:80:59:5a:e0:41:24:1d:b2:8f:15:60:
         58:47:db:ef:6e:46:fd:15:f5:d9:5f:9a:b3:db:d8:b8:e4:40:
         b3:cd:97:39:ae:85:bb:1d:8e:bc:dc:87:9b:d1:a6:ef:f1:3b:
         6f:10:38:6f
bash-4.2$ 

Add the trusted root cert in key.kdb

$ runmqakm -cert -add -db key.kdb -file bundle1.txt -label "COMODO RSA Domain Validation Secure Server CA" -pw 12345

$ runmqakm -cert -list -db key.kdb -pw 12345
Certificates found
* default, - personal, ! trusted, # secret key
!	"COMODO RSA Domain Validation Secure Server CA"

Add the trusted intermediate cert in key.kdb

$ runmqakm -cert -add -db key.kdb -file bundle2.txt -label "COMODO RSA Certification Authority" -pw 12345

$ runmqakm -cert -list -db key.kdb -pw 12345
Certificates found
* default, - personal, ! trusted, # secret key
!	"COMODO RSA Domain Validation Secure Server CA"
!	"COMODO RSA Certification Authority"

Import the CRT that we received.

$ runmqakm -certreq -list -db key.kdb -pw 12345 
Certificates requests found
	ibmwebspheremqqm01

Option 1 - same error as below but works. The error is okay, per https://www.youtube.com/watch?v=rB_DLMm4tOY&list=PLpFOFgc5UfWE4pHHIweA8TGF-agIG42TT&index=5

$ runmqakm -cert -receive -db key.kdb -pw 12345 -file www_mqfellowwest_host.crt
CTGSK2146W An invalid certificate chain was found.
Additional untranslated info: No certificate chain built
Additional untranslated info: GSKKM_VALIDATIONFAIL_SUBJECT: [Class=]GSKVALMethod::PKIX[Issuer=]CN=COMODO RSA Domain Validation Secure Server CA,O=COMODO CA Limited,L=Salford,ST=Greater Manchester,C=GB[#=]0093fc3d8480ca5c67377121bcd562e0fd[Subject=]CN=www.mqfellowwest.host,OU=PositiveSSL,OU=Domain Control Validated
CTGSK2043W Key entry validation failed.

$ runmqakm -cert -list -db key.kdb -pw 12345
Certificates found
* default, - personal, ! trusted, # secret key
!	"COMODO RSA Domain Validation Secure Server CA"
!	"COMODO RSA Certification Authority"
-	ibmwebspheremqqmr01

Note: For fixing the failed error above - delete the cert

$ runmqakm -cert -delete -label ibmwebspheremqqmr01 -db key.kdb -pw 12345
bash-4.2$ runmqakm -cert -list -db key.kdb -pw 12345
Certificates found
* default, - personal, ! trusted, # secret key
!	"COMODO RSA Domain Validation Secure Server CA"
!	"COMODO RSA Certification Authority"

Note: cert dissappeared. Re-issue the cert from namecheap/provider which means start from beginning.

Option 2 - In MQ the errors CTGSK2042W and CTGSK2043W occur when running runmqakm. ikeyman does not cause those errors. This option is trusted enabled

https://developer.ibm.com/answers/questions/404186/in-mq-the-errors-ctgsk2042w-and-ctgsk2043w-occur-w/

$ runmqakm -cert -receive -file www_mqfelloweast_host.crt -db key.kdb -pw 12345 -format ascii -fips false 

CTGSK2146W An invalid certificate chain was found.
Additional untranslated info: No certificate chain built
Additional untranslated info: GSKKM_VALIDATIONFAIL_SUBJECT: [Class=]GSKVALMethod::PKIX[Issuer=]CN=COMODO RSA Domain Validation Secure Server CA,O=COMODO CA Limited,L=Salford,ST=Greater Manchester,C=GB[#=]00a1b5566bfe97442fb4830a2434787e45[Subject=]CN=www.mqfellowwest.host,OU=PositiveSSL,OU=Domain Control Validated
CTGSK2043W Key entry validation failed.

$ runmqakm -cert -list -db key.kdb -pw 12345
Certificates found
* default, - personal, ! trusted, # secret key
!	"COMODO RSA Domain Validation Secure Server CA"
!	"COMODO RSA Certification Authority"
-	ibmwebspheremqqm01


$ runmqakm -cert -modify -db key.kdb -pw 12345 -label ibmwebspheremqqm01 -trust enable 
bash-4.2$ runmqakm -cert -list -db key.kdb -pw 12345
Certificates found
* default, - personal, ! trusted, # secret key
!	"COMODO RSA Domain Validation Secure Server CA"
!	"COMODO RSA Certification Authority"
-	ibmwebspheremqqm01


$ runmqakm -cert -setdefault -db key.kdb -pw 12345 -label ibmwebspheremqqm01
$ runmqakm -cert -list -db key.kdb -pw 12345
Certificates found
* default, - personal, ! trusted, # secret key
!	"COMODO RSA Domain Validation Secure Server CA"
!	"COMODO RSA Certification Authority"
*-	ibmwebspheremqqmr01

9:09
https://www.youtube.com/watch?v=rB_DLMm4tOY&list=PLpFOFgc5UfWE4pHHIweA8TGF-agIG42TT&index=5 

$ runmqakm -cert -details -db key.kdb -pw 12345 -label ibmwebspheremqqm01
Label : ibmwebspheremqqm01
Key Size : 2048
Version : X509 V3
Serial : 00a1b5566bfe97442fb4830a2434787e45
Issuer : "CN=COMODO RSA Domain Validation Secure Server CA,O=COMODO CA Limited,L=Salford,ST=Greater Manchester,C=GB"
Subject : "CN=www.mqfellowwest.host,OU=PositiveSSL,OU=Domain Control Validated"
Not Before : December 29, 2018 12:00:00 AM GMT+00:00

Not After : December 29, 2019 11:59:59 PM GMT+00:00

Public Key
    30 82 01 22 30 0D 06 09 2A 86 48 86 F7 0D 01 01
    01 05 00 03 82 01 0F 00 30 82 01 0A 02 82 01 01
    00 C8 08 40 B9 5A 3F 96 87 E7 BC 81 02 BA 4E BC
    D9 EA 5F 6E 28 F9 11 61 DC 11 21 0F 2E 6F A5 97
    DD CA 42 28 26 4B E8 6A C8 37 A7 35 9B 64 C8 35
    9C 7B 0E AB C2 6F 92 13 A9 74 32 64 CD A3 5B 08
    9A 71 BC E2 91 8B D7 7D DC A5 0A 5A 28 67 69 E5
    C6 C6 B6 07 97 14 DE 55 9F 39 46 A2 85 C7 54 18
    39 77 05 F4 03 26 22 DE EA 46 DB AC 0F 66 69 89
    1A 58 70 A4 23 EC CB 69 97 68 F2 80 3B 73 62 73
    CD 0B 50 D5 69 A7 09 CB 60 B1 B0 D6 EE 89 55 30
    87 BF 27 A5 1C F0 77 5A F9 7C 62 1F 52 C1 1F 9A
    A5 0E B2 AC 1C AB BA 03 6B 54 08 EF 73 7A 7A 05
    02 8E BB 47 23 13 BA 83 05 C7 DA E2 80 7F 07 38
    C0 62 21 5F 91 5E 27 1B 20 02 F9 E6 31 76 BD 84
    81 2A 71 BD 68 77 2C EC 98 92 07 F8 CE E1 5B 3E
    67 28 C1 2E 11 78 03 51 1A 63 31 27 53 B7 11 0B
    23 3B 3E 9F 2F 03 CB 23 B5 57 4F 8A 56 C9 21 4B
    37 02 03 01 00 01
Public Key Type : RSA (1.2.840.113549.1.1.1)
Fingerprint : SHA1 : 
    D6 5C 39 F0 19 A1 5D 1B 9D 7E D7 57 3D AB DB C9
    99 B9 6C AB
Fingerprint : MD5 : 
    9F 24 50 78 F4 9B EF 1E AE 59 E3 8D 44 41 20 FA
Fingerprint : SHA256 : 
    B2 0D 48 F3 A7 1C A1 91 1C 42 A1 97 5E 7C 81 C6
    3E DD E4 50 7D D0 B9 DA 27 EC C5 41 DF D1 79 F2
Extensions
    AuthorityKeyIdentifier
      keyIdentifier:
    90 AF 6A 3A 94 5A 0B D8 90 EA 12 56 73 DF 43 B4
    3A 28 DA E7
      authorityIdentifier:
      authorityCertSerialNumber:
    SubjectKeyIdentifier
      keyIdentifier:
    51 BA 56 6A 6C 0C 03 7D 04 C0 93 C3 96 BF 9B 31
    E6 F7 BD 19
    key usage: digitalSignature, keyEncipherment
        critical
    basicConstraints
        ca = false
        critical
    eku
        serverAuth
        clientAuth
    certificatePolicies
        1.3.6.1.4.1.6449.1.2.2.7
        2.23.140.1.2.1
    CRLDistributionPoints
      fullname:
      uniformResourceID:      keyIdentifier: GSKASNObject: OBJECT(tag=22, class=0) value: -----BEGIN HEX-----
16 43 68 74 74 70 3A 2F 2F 63 72 6C 2E 63 6F 6D     .Chttp://crl.com
6F 64 6F 63 61 2E 63 6F 6D 2F 43 4F 4D 4F 44 4F     odoca.com/COMODO
52 53 41 44 6F 6D 61 69 6E 56 61 6C 69 64 61 74     RSADomainValidat
69 6F 6E 53 65 63 75 72 65 53 65 72 76 65 72 43     ionSecureServerC
41 2E 63 72 6C                                      A.crl
-----END HEX-----


    AuthorityInfoAccess
    PKIX_AD_CA_Issuer (1.3.6.1.5.5.7.48.2)
      accessLocation:uniformResourceID: http://crt.comodoca.com/COMODORSADomainValidationSecureServerCA.crt
    PKIX_AD_OCSP (1.3.6.1.5.5.7.48.1)
      accessLocation:uniformResourceID: http://ocsp.comodoca.com
    subjectAlternativeName
        dNSName: www.mqfellowwest.host
        dNSName: mqfellowwest.host
     (1.3.6.1.4.1.11129.2.4.2)
        Value
    04 81 F2 00 F0 00 77 00 BB D9 DF BC 1F 8A 71 B5
    93 94 23 97 AA 92 7B 47 38 57 95 0A AB 52 E8 1A
    90 96 64 36 8E 1E D1 85 00 00 01 67 FC 36 02 E8
    00 00 04 03 00 48 30 46 02 21 00 83 54 ED 52 62
    9E 77 98 69 53 B7 6E 04 60 88 05 11 C4 AA F3 8F
    56 95 34 E8 CE F7 1E DE C4 A4 1B 02 21 00 FA C3
    7C 80 7F 59 41 96 0D 02 C7 96 97 7E AE 2D BE 1E
    37 E5 1D E3 56 BB 8E B3 AD E7 E4 FC BB 08 00 75
    00 74 7E DA 83 31 AD 33 10 91 21 9C CE 25 4F 42
    70 C2 BF FD 5E 42 20 08 C6 37 35 79 E6 10 7B CC
    56 00 00 01 67 FC 36 03 13 00 00 04 03 00 46 30
    44 02 20 7B A2 BB A9 45 01 07 C3 F0 7F 5D 38 C3
    32 EB AB 81 3F 94 BF 30 62 B6 5E F4 F8 6E FE 19
    66 D0 F1 02 20 20 63 2F 35 FE 3C E0 3A 39 6E 9F
    A0 0A 13 7E 98 3E 6A 2B D3 FA 1F 8F 71 B8 8B 00
    39 2A 27 1C 4D
Signature Algorithm : SHA256WithRSASignature (1.2.840.113549.1.1.11)
Value
    5D BE FE F1 B9 7B 08 36 A6 CD 44 FB 0F B4 50 83
    39 EF D0 C6 94 3A 1F AD 52 FB B4 F1 6E AB 43 32
    13 CA 3C 06 15 D6 FC D7 86 F1 28 4C AE B6 37 56
    F1 F1 CC 4E 09 6D 00 CD 19 93 04 17 E2 9F 4B 20
    D3 72 E7 E8 FB 33 DD 66 FB 5D 5B AD 47 92 4C 22
    14 E6 F2 6F 50 AF 3A DB 67 BA 99 49 7C 22 3D 08
    4E 61 DC D3 7D 3C AE B6 53 14 5A D1 7F 3D 22 1E
    A5 CD 79 75 6B 93 6E 42 D1 53 42 AB 92 36 52 49
    1D DE 2F 2F 8A 8D CA 5F B2 57 06 69 DE F9 1F 68
    39 4A C4 E1 6B 36 72 50 E9 78 21 D5 54 C5 B7 09
    F8 85 21 2C BB E7 73 B6 D4 73 B7 89 B1 96 AB 5A
    A3 90 A3 1C 94 21 BF B8 57 E8 E9 89 D9 56 C0 26
    D8 8D 6F EF 31 BD 8D 8F FA 1E 56 E9 2E 1C 8A 91
    35 2B 00 7D DC E2 57 3A A7 30 35 D6 0B E8 73 8E
    67 13 7E B1 9E 17 F3 98 1A B0 5B 52 30 BF A1 82
    2D 64 B2 C6 8F B3 EC 16 93 FE 31 77 4A BC 6F 44
Trust Status : Enabled
bash-4.2$ 

copy the signed-ssl folder to ssl and move the original one to backup.

Refresh security

$ runmqsc QMR01

REFRESH SECURITY TYPE(SSL)
     6 : REFRESH SECURITY TYPE(SSL)
AMQ8560I: IBM MQ security cache refreshed.

DIS CHSTATUS(QMR01.QM01) ALL
    13 : DIS CHSTATUS(QMR01.QM01) ALL
AMQ8417I: Display Channel Status details.
   CHANNEL(QMR01.QM01)                     CHLTYPE(SDR)
   BATCHES(0)                              BATCHSZ(50)
   BUFSRCVD(0)                             BUFSSENT(0)
   BYTSRCVD(0)                             BYTSSENT(0)
   CHSTADA(2018-12-29)                     CHSTATI(23.30.50)
   COMPHDR(NONE,NONE)                      COMPMSG(NONE,NONE)
   COMPRATE(0,0)                           COMPTIME(0,0)
   CONNAME(52.86.146.171(1414))            CURLUWID(0000000000000000)
   CURMSGS(0)                              CURRENT
   CURSEQNO(0)                             EXITTIME(0,0)
   HBINT(300)                              INDOUBT(NO)
   JOBNAME(0000000000000000)               LOCLADDR( )
   LONGRTS(999999999)                      LSTLUWID(0000000000000000)
   LSTMSGDA( )                             LSTMSGTI( )
   LSTSEQNO(0)                             MCASTAT(NOT RUNNING)
   MONCHL(OFF)                             MSGS(0)
   NETTIME(0,0)                            NPMSPEED(FAST)
   RQMNAME( )                              SHORTRTS(9)
   SECPROT(NONE)                           SSLCERTI( )
   SSLKEYDA( )                             SSLKEYTI( )
   SSLPEER( )                              SSLRKEYS(0)
   STATUS(RETRYING)                        STOPREQ(NO)
   SUBSTATE( )                             XBATCHSZ(0,0)
   XMITQ(QM01)                             XQTIME(0,0)
   RVERSION( )                             RPRODUCT( )

Current error:

----- amqrccca.c : 439 --------------------------------------------------------
12/29/2018 11:31:51 PM - Process(14223.1) User(mqm) Program(runmqchl)
                    Host(ip-172-17-1-106.us-west-2.compute.internal) Installation(Installation1)
                    VRMF(9.1.0.0) QMgr(QMR01)
                    Time(2018-12-29T23:31:51.943Z)
                    ArithInsert1(8)
                    CommentInsert1(QMR01.QM01)
                    CommentInsert2(gsk_get_cert_by_label)
                   
AMQ9654E: Validation checks for the remote personal certificate failed. The
channel did not start.

EXPLANATION:
An SSL certificate received from the remote system was not corrupt but failed
validation checks on something other than its ASN.1 fields and date. It is
possible that the certificate chain could not be built for for one of the
following reasons: - The certificate Subject DN is more than 1024 characters
long. - The DN contains unsupported duplicate attribute values. - The DN is
missing. 

The channel is 'QMR01.QM01'; in some cases its name cannot be determined and so
is shown as '????'.
ACTION:
Ensure that the remote system has a valid personal certificate and restart the
channel.

It is expected as remote server uses self-signed cert.

```
