# WireShark

## Session 1

[Based on this Video](https://www.youtube.com/watch?v=y13zH-8OPE8)

## what is Ethernet Frame size

- Ethernet Frame
  - _Preamble 7 bytes_
  - _Start Field Delimiter(SFD) 1 byte_
  - _Destination MAC address 6 bytes_
  - _SOURCE MAC Address 6 Bytes_
  - _Type Field 2 bytes_
  - _1500 bytes MTU (Maximum Transmission UNIT)_
  - _CRC 4 Bytes_
    - _Total 1526 Bytes_ out of which _`Preamble`, `SFD`, `CRC` Bytes are transparent - responsibility of the Network Card_
- ***There is no delimiter in the Ethernet packet unlike other protocols***
- ***Until Ethernet is done transmitting - is the delimiter -***
- There is a beginning of a Ethernet frame but no end

- TCp Characteristics
  - Connection oriented - _Syn, Syn, Ack_ - in networking parlance ;)
  - Reliable ??
    - _Sequence numbers_
    - _Re-transmissions_
  - _Flow-Control_
  - _Stream-Oriented_ - Like Garden HOSE - nO Beginning nO End
    - ***Applications give data to TCP in stream oriented fashion, there is no beginning there is no end***

> ***UDP's unreliability means that it's up to the application to recover data when there is a `packet` loss***
> ***UDP doesn't worry about the packet loss - technically it is nothing less than TCP***

## TCP  

> TCP makes a copy of the data to be sent and keeps it in the send-buffer

- _In the world of TCP there are two BUffers_
  - _Client Side -  `receive-buffer` = TCP Window Size and on the wire_
  - _Server Side - `send-buffer`_
    - _send buffer - is not exposed anywhere - except for the server and the application - `not on the wire`_
  - __ (at 17:21)

### The Mechanism

- TCP pours data in the `send-buffer`
- Application Trusts TCP to deliver the Data  ***AND TCP DOESN'T HAVE THE LUXURY TO LOoSE THE DATA sent by the application, TCP cannot ask data back from the Application ( By Design ;) )***
- When the `send-buffer` is full , TCP cannot accept anymore data from the Application
- TCP then starts to Packet-ize the Data ( Done by IP layer actually  - to Ethernet Frames)
- Total number of Bytes : x => packet is Byte Size => packets are then number from `1 to x`
  - _Sequence Number analysis becomes easier to do_
  - There is nothing magical about TCP sequence number or acknowledgement number - All they say is how many bytes have I Sent to you
- Receiver has `receive-buffer` because TCP/IP can transmit data faster than any Application requesting it can process
  - _It's like a waiting room_
- If there is a packet loss before it reaches `receive-buffer`
  - _Byte missing sequence is the indicator_
  - _Note, Server keeps receiving Ack number for the bytes received_
  - _if the ACK for the Byte is received or not received **IMPLIES** that the Bytes prior to these have been received_
  - _`send-buffer` is cleared for the bytes already received_
  - _And the sequence of bytes starting from the missing byte, are sent again_
  
- Bandwidth Delay Product (BDP)
  - _You can only transfer One FUll-Window-Size per round trip_
    - _In almost all cases send and the receive buffer have the same size_
      - _These buffers are allocated when applications open up a socket_
      - _There size are the same_
    - Every socket, at the end-point, when opens a connection, requires a send and a receive buffer at the end-point
      - _Because TCP transmits and receives data at the same time_
    - _Latency between a client and server means the time taken in a round-trip_
    - _(BDP * 2) are the number of bits in transit, why multiplied by 2 because along the way there are routers and switches and they have there own buffers, that means more data in transit - technically oO(<_
  - _BUFFERING IS GOOD BUT BREAKS TCP - FOR TCP IT SKEWS THE FEEDBACK_
  - _1460 is the maximum-segment-size (MSS)(or the packet size) in the TCP WORLD - if we peel of IP and Ethernet from the data packet_
  - _TCP RULE IS - AS SOON AS TWO FULL `MSS` and  RECEIVER ACKNOWLEDGES IMMEDIATELY_
  
> NOTE : In networking parlance its bits/sec and in App Dev parlance its bYTE/sec. 1 kilo bits = 1000bits and 1 kilo bytes is 1024 bytes

#### Sequence Number

- ***IT IS NOTHING MORE THAN `HOW MANY BYTEs` A GIVEn TCP CONNECTION `HAS SENT SO FAR` IN AN ONGOING COMMUNICATION***
- _For example_
  - _IF the TCP packet has `Seq: 1` & `Len: 124` = This packet has first 124 bytes and the next/upcoming packet will have `Seq: 125`_ : pretty straight forward
  - _Next packet has `Seq: 125` & `Len: 165` and next `Seq: 290`_
  - _`Ack` received for the packet above sent packet will have `Ack: 290`_
  
> NOTE: TCP (UNIX, LINUX) rule : RECEIVE TWO full `MSS` and then SEND `ACK`, Microsoft RULE - `ANY TWO packets` you receive send `ACK`

- in `UNIX` , `LINUX` - Delayed Acknowledgement
  - _HOW LONG DOES A CONNECTION SHOULD WAIT TO SEND DELAYED ACK as the prescribed size of data hasn't been received?_
    - _ANSWER: WINDOW waits 200ms, others wait 75ms/ Depends on the OS / we can use this number as a fingerprint of the `OS` being used_
- _Faster the `Ack` is received faster the sender will clean the respective buffer_

#### Revisit TCP Handshake

- first `Syn` packet is received by the sender  - It;s a knock on the door to open up a connection
- this `Syn` packet:
  - _Segment Size is 1460 bytes_
- Sender sends a (`Syn`, `Ack`) {it is a way of saying i am ready to sync and take this is a acknowledgment also to your `Syn`} packet to signal it is ready
- Receiver sends a `Ack` to acknowledge the above (`Syn`,`Ack`)
- _Delayed `Ack` during happens a transmission (NON window OS) when two consecutive un-`ack`-ed packets are received and their total size is less than two full size `MSS`_
  - _Delayed `Ack` can be tunned or can be turned off_

- if an `Ack` goes missing - for the `=Sender=` it doesn't really matter because it doesn't know which  `Ack` got lost - As soon as the `=Sender=`  receives the next `Ack` and it's next-expected-seq number is taken into consideration

<hr/>

### Re-transmission Timeout(RTO)

> Finito ;)
