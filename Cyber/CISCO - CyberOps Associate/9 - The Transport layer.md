The table identifies and describes the ten fields in a TCP header.

|**TCP Header Field**|**Description**|
|---|---|
|**Source Port**|A 16-bit field used to identify the source application by port number.|
|**Destination Port**|A 16-bit field used to identify the destination application by port number.|
|**Sequence Number**|A 32-bit field used for data reassembly purposes.|
|**Acknowledgment Number**|A 32-bit field used to indicate that data has been received and the next byte expected from the source.|
|**Header Length**|A 4-bit field known as ʺdata offsetʺ that indicates the length of the TCP segment header.|
|**Reserved**|A 6-bit field that is reserved for future use.|
|**Control bits**|A 6-bit field that includes bit codes, or flags, which indicate the purpose and function of the TCP segment.|
|**Window size**|A 16-bit field used to indicate the number of bytes that can be accepted at one time.|
|**Checksum**|A 16-bit field used for error checking of the segment header and data.|
|**Urgent**|A 16-bit field used to indicate if the contained data is urgent.|

|**UDP Header Field**|**Description**|
|---|---|
|**Source Port**|A 16-bit field used to identify the source application by port number.|
|**Destination Port**|A 16-bit field used to identify the destination application by port number.|
|**Length**|A 16-bit field that indicates the length of the UDP datagram header.|
|**Checksum**|A 16-bit field used for error checking of the datagram header and data.|

These are the functions of the three-way handshake:
- It establishes that the destination device is present on the network.
- It verifies that the destination device has an active service and is accepting requests on the destination port number that the initiating client intends to use.
- It informs the destination device that the source client intends to establish a communication session on that port number.

The six control bits flags are as follows:
- **URG** - Urgent pointer field significant
- **ACK** - Acknowledgment flag used in connection establishment and session termination
- **PSH** - Push function
- **RST** - Reset the connection when an error or timeout occurs
- **SYN** - Synchronize sequence numbers used in connection establishment
- **FIN** - No more data from sender and used in session termination

During session setup, an initial sequence number (ISN) is set.
The sequence (SEQ) number and acknowledgement (ACK) number are used together to confirm receipt of the bytes of data contained in the transmitted segments.
The SEQ number identifies the first byte of data in the segment being transmitted. TCP uses the ACK number sent back to the source to indicate the next byte that the receiver expects to receive. This is called expectational acknowledgement.

![[Pasted image 20250214171243.png]]

A common MSS is 1,460 bytes when using IPv4. A host determines the value of its MSS field by subtracting the IP and TCP headers from the Ethernet maximum transmission unit (MTU). On an Ethernet interface, the default MTU is 1500 bytes. Subtracting the IPv4 header of 20 bytes and the TCP header of 20 bytes, the default MSS size will be 1460 bytes, as shown in the figure.





















































































































































































