
# Table of Contents

1.  [Netperf exploration](#org1b20fbb)
    1.  [Changing the RR size](#orgafd8cbd)
    2.  [Accounting for the conneciton setup](#org0f8e3ef)
    3.  [Now lets get some performance out of it](#org6029737)
        1.  [Saturating the link](#org0320d65)


<a id="org1b20fbb"></a>

# Netperf exploration

Before running these, need to start the netserver daemon

    netserver

    netperf

This is the normal TCP<sub>STREAM</sub> test, used to measure large file transfers. Not super relevant to this project as we want to measure request-response time but good to see what TCP is good at.

    netperf -t TCP_RR -c -C

In this test (TCP<sub>RR</sub>) netperf measures the request/response performance using TCP. It gives the transmission rate in messages per second.


<a id="orgafd8cbd"></a>

## Changing the RR size

    netperf -t TCP_RR -c -C -- -r 1,1

    netperf -t TCP_RR -c -C -- -r 128,128

    netperf -t TCP_RR -c -C -- -r 1k,1k

    netperf -t TCP_RR -c -C -- -r 16k,16k

We can see in this test that the transmission rate starts to tank when the size goes up


<a id="org0f8e3ef"></a>

## Accounting for the conneciton setup

We can see that when we use the TCP<sub>CRR</sub> test which accounts for the connection setup time, the transmission rate tanks

    netperf -t TCP_CRR -c -C -- -r 1,1

    netperf -t TCP_CRR -c -C -- -r 128,128

    netperf -t TCP_CRR -c -C -- -r 1k,1k

    netperf -t TCP_CRR -c -C -- -r 16k,16k


<a id="org6029737"></a>

## Now lets get some performance out of it

<https://www.ibm.com/docs/en/aix/7.2?topic=tuning-tcp-request-response-workload>

TCP<sub>NODELAY</sub> is a socket option that controls whether to use delayed acknowledgments or not. Delayed acknowledgments can slow down request/response workloads as it can wait up to 200ms to piggyback an acknowledgment on a response packet.

Netperf allows us to enable TCP<sub>NODELAY</sub> with the -D flag.

    netperf -t TCP_RR -c -C -- -r 1,1 -D

    netperf -t TCP_RR -c -C -- -r 1k,1k -D

    netperf -t TCP_RR -c -C -- -r 16k,16k -D

We can see that we get a significant performance increase with this.


<a id="org0320d65"></a>

### Saturating the link

We can adjust the socket buffer sizes to better saturate the link. We can use the bandwidth delay product

