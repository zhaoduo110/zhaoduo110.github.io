---
title: Webrtc
categories:
  - - 前端
tags:
  - Webrtc
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218900853.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218900853.jpg
abbrlink: a8296e77
date: 2021-09-16 10:44:58
---





chrome插件：chrome://webrtc-internals/



==有机会会专门写几篇关于前端webrtc相关的文章==



# webrtc统计信息

##  获取方式

**官方文档：**https://developer.mozilla.org/en-US/docs/Web/API/RTCPeerConnection/getStats

**作用：**返回关于整个连接或关于指定 MediaStreamTrack 统计信息的数据进行解析

**使用方法** 

```
promise = rtcPeerConnection.getStats(selector)
```

**例子**

```js
window.setInterval(function() {
  myPeerConnection.getStats(null).then(stats => {
    let statsOutput = "";

    stats.forEach(report => {
      statsOutput += `<h2>Report: ${report.type}</h2>\n<strong>ID:</strong> ${report.id}<br>\n` +
                     `<strong>Timestamp:</strong> ${report.timestamp}<br>\n`;

      // Now the statistics for this report; we intentially drop the ones we
      // sorted to the top above

      Object.keys(report).forEach(statName => {
        if (statName !== "id" && statName !== "timestamp" && statName !== "type") {
          statsOutput += `<strong>${statName}:</strong> ${report[statName]}<br>\n`;
        }
      });
    });

    document.querySelector(".stats-box").innerHTML = statsOutput;
  });
}, 1000);
```

**参数：**

- selector

  用于收集统计信息的MediaStreamTrack。如果该值为空(默认值)，则将收集整个RTCPeerConnection的统计信息。

**返回值：**

​	它使用提供连接统计信息的RTCStatsReport对象进行解析。report的内容取决于选择器以及连接的其他细节。

异常：

​	此方法不会抛出异常;它使用以下错误拒绝返回的report:

​	InvalidAccessError

​		没有RTCRtpSender或rtcrtreceiver的轨迹与指定的选择器匹配，或选择器与多个发送方或接收方匹配。





## RTCStatsReport

**官方文档：**https://developer.mozilla.org/zh-CN/docs/Web/API/RTCStatsReport

**说明：**

RTCStatsReport接口提供了一个统计报告，通过调用 `RTCPeerConnection.getStats()`、`rtcrtpreceier . getstats()`或者`RTCRtpSender.getStats()`方法获得。

此统计报告包含统计类别字符串名称到包含相应统计数据的对象的映射。

在RTCPeerConnection上调用getStats()使您可以指定是否希望获取连接上的出站、入站或所有流的统计信息。

getStats()的RTCRtpReceiver和RTCRtpSender版本只返回对调用它们的传入或传出流可用的统计信息。

**数据对象：**

对于每一类统计信息，都有一个其属性提供相关信息的字典。

- 共有的属性

  - type

    一个DOMString，指示对象包含的统计类型，取自enum类型 RTCStatsType。

  - id
    一个DOMString，唯一标识基于RTCStats生成该对象的检查对象。

  - timestamp

    DOMHighResTimeStamp对象，指示为该统计对象采集样本的时间。

### RTCStatsType

官方文档：https://developer.mozilla.org/en-US/docs/Web/API/RTCStatsType

RTCStatsType枚举类型(enum)是一组字符串，用于定义在RTCStatsReport对象中发现的记录中报告的统计类型。

这种类型决定记录基于哪个基于rtcstats的字典。

统计类别是枚举类型RTCStatsType的成员，如下所示:

**candidate-pair**

[RTCIceCandidatePairStats](https://developer.mozilla.org/en-US/docs/Web/API/RTCIceCandidatePairStats)对象，提供与RTCIceTransport相关的统计信息。

当RTCPeerConnection更改其 RTCPeerConnection.iceGatheringState 时，除当前活动的传输对之外的候选对将被删除。

ICE重启时，iceGatheringState为new。在传输切换到另一个候选对后，活跃的候选对被删除;此更改无法通过其他方式检测到。

![image-20210825152531076](https://could-res-1252778021.file.myqcloud.com/img/image-20210825152531076.png)

| 值                       | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| availableIncomingBitrate | 可用入网带宽。返回一个指示候选对所代表的网络连接的可用入站能力的值。<br/>该值越高，您可以假设可用于接收数据的带宽越多。该值以 bit/s 为单位，以1秒为间隔进行计算。<br/>这没有考虑到IP开销的大小，也没有考虑任何其他传输层(如TCP或UDP)。 |
| availableOutgoingBitrate | 可用出网带宽。返回一个指示候选对所代表的网络连接的可用出站能力的值。<br>该值越高，您可以假设可用于外发数据的带宽越多。该值以 bit/s 为单位，以1秒为间隔进行计算。<br>这没有考虑到IP开销的大小，也没有考虑任何其他传输层(如TCP或UDP)。 |
| bytesReceived            | 当前候选对接收到的总有效载荷字节数（即接收到的总字节数减去任何头、填充或其他管理开销） |
| bytesSent                | 当前候选对发送的总有效载荷字节数（即发送的总字节数减去任何头、填充或其他管理开销） |
| consentRequestsSent      | 该候选对上已经发送的同意请求的总数。                         |
| currentRoundTripTime     | 该对等体通过这对ICE候选者描述的连接发送数据到远程对等体并返回所需的秒数。 |
| localCandidateId         | RTCIceCandidateStats对象中包含的数据对应于rtcicecandicandidate的唯一ID字符串，提供候选对的本地候选的统计信息。 |
| nominated                | 布尔值，如果为真，则表示该对象描述的候选对是已被提议使用的，如果其优先级在提名候选对中最高，则将(或已)使用该候选对。详细信息请参见RFC 5245 7.1.3.2.4章节。 |
| priority                 | ==废弃==。指示候选对优先级的整数值。                         |
| remoteCandidateId        | 与远程候选对象对应的唯一ID字符串，从该候选对象获取数据来构造描述连接的远程端RTCIceCandidateStats对象。 |
| requestsReceived         | 已接收到的连接检查请求的总数，包括重新传输。该值包括连接检查和STUN同意检查。 |
| requestsSent             | 已发送的连通性检查请求的总数，不包括重传。                   |
| responsesReceived        | 已接收到的连接检查响应的总数。                               |
| responsesSent            | 已发送的连接检查响应的总数。这包括连接检查请求和STUN同意请求。 |
| state                    | 一个DOMString，其值是RTCStatsIceCandidatePairState枚举类型中的值之一。<br>failed：对该候选对执行了检查，但失败。失败可能是因为没有收到响应，也可能是因为响应指出发生了不可恢复的错误。<br>frozen：尚未对该候选对执行任何检查，执行检查将被阻塞，直到另一个检查成功。一旦检查成功，这对将解冻并进入等待状态。<br>in-progress：已经对该候选对开始了检查，但检查还在继续并未结束<br>succeeded：对该候选对的检查已经成功完成<br>waiting：该候选对尚未被检查，但只要该pair是仍处于等待状态的最高优先级，就可以执行检查。<br> |
| totalRoundTripTime       | 浮点值，从发送 STUN 请求到收到响应之间的总时间。该值包括连接性检查和同意检查请求。<br>可以通过将该值除以responsesReceived来计算平均往返时间(RTT) |
| transportId              | 一个DOMString，唯一标识RTCIceTransport，该RTCIceTransport用于获取用于生成该对象的与传输相关的统计信息(如RTCTransportStats中所示)。 |



**certificate**

提供与RTCIceTransport正在使用的证书相关的统计信息的RTCCertificateStats对象。

![image-20210825153743599](https://could-res-1252778021.file.myqcloud.com/img/image-20210825153743599.png)

| 值                   | 说明 |
| -------------------- | ---- |
| base64Certificate    |      |
| fingerprint          |      |
| fingerprintAlgorithm |      |
| id                   |      |
| timestamp            |      |
| type                 |      |



**codec**

RTCCodecStats对象，包含RTP流用来发送或接收RTCPeerConnection数据的编解码器的统计信息。

不同的profile/level对应了不同的codecs信息

![image-20210825154047574](https://could-res-1252778021.file.myqcloud.com/img/image-20210825154047574.png)

| 值          | 说明 |
| ----------- | ---- |
| channels    |      |
| clockRate   |      |
| id          |      |
| mimeType    |      |
| payloadType |      |
| timestamp   |      |
| transportId |      |
| type        |      |



**csrc**

RTCContributingSourceStats对象，包含入站RTP流的 CSRC 相关的统计信息。

| 值   | 说明 |
| ---- | ---- |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |



**data-channel**

RTCDataChannelStats对象，包含关于连接上每个rtcdatchannel的统计信息。

![image-20210825154509589](https://could-res-1252778021.file.myqcloud.com/img/image-20210825154509589.png)

| 值                    | 说明 |
| --------------------- | ---- |
| bytesReceived         |      |
| bytesSent             |      |
| dataChannelIdentifier |      |
| id                    |      |
| label                 |      |
| messagesReceived      |      |
| messagesSent          |      |
| protocol              |      |
| state                 |      |
| timestamp             |      |
| type                  |      |



**peer-connection**

RTCPeerConnectionStats对象，提供了与整个对等体连接的RTCPeerConnection相关的统计信息。

![image-20210825154648792](https://could-res-1252778021.file.myqcloud.com/img/image-20210825154648792.png)

| 值                 | 说明 |
| ------------------ | ---- |
| dataChannelsClosed |      |
| dataChannelsOpened |      |
| id                 |      |
| timestamp          |      |
| type               |      |



**inbound-rtp**

[RTCInboundRtpStreamStats](https://developer.mozilla.org/en-US/docs/Web/API/RTCInboundRtpStreamStats)对象，提供关于从远程对等端接收的入站数据的统计信息。

由于这只提供与入站数据相关的统计信息，而不考虑本地对等点的状态，因此不包括任何需要这两者的知识的值，例如往返时间。如果没有连接的对等点，则此报告不可用。

![image-20210825154817753](https://could-res-1252778021.file.myqcloud.com/img/image-20210825154817753.png)

| 值                          | 说明                                                         |
| --------------------------- | ------------------------------------------------------------ |
| averageRtcpInterval         | 浮点值，表示两个连续复合RTCP包之间的平均RTCP间隔。           |
| bytesReceived               | 一个64位整数，表示到目前为止该媒体源已接收到的字节总数。     |
| codecId                     |                                                              |
| decoderImplementation       |                                                              |
| firCount                    | 整数，表示接收方发送给发送方的FIR (Full Intra Request)报文的总数。这是流滞后频率的指示器，需要跳过帧才能赶上。该值仅对视频流有效。 |
| frameHeight                 |                                                              |
| frameWidth                  |                                                              |
| framesDecoded               | 一个长整数值，表示到目前为止该媒体源已正确解码的视频帧总数。这是在没有丢弃帧的情况下渲染的帧数。仅对视频流有效。 |
| framesDropped               | 只存在于视频中。在解码前丢弃的帧总数，或者因为该帧错过了该接收器的显示期限而丢弃的帧总数。该测量从接收器创建时开始，是[RFC7004]附录A(g)中定义的累积度量 |
| framesPerSecond             |                                                              |
| framesReceived              |                                                              |
| headerBytesReceived         |                                                              |
| jitter                      |                                                              |
| keyFramesDecoded            | 只存在于视频中。它表示该RTP媒体流成功解码的关键帧的总数，如VP8[RFC6386]中的关键帧或H.264[RFC6184]中的IDR帧。这是 framesDecoded 的一个子集 |
| kind                        |                                                              |
| lastPacketReceivedTimestamp | DOMHighResTimeStamp，指示这个源收到最后一个包的时间。另一方面，timestamp属性指示统计对象生成的时间。 |
| mediaType                   |                                                              |
| nackCount                   | 1个整数，表示接收方发送的NACK报文的总数。                    |
| packetsLost                 | 丢失的RTP数据包总数。请注意，由于这个数据包的估计方式，如果收到的数据包多于发送的数据包，它可能是负数 |
| packetsReceived             | 连接上收到的任何种类的数据包的总数                           |
| pliCount                    | 一个整数，指定接收方通知发送方某一帧或多帧编码视频数据丢失的次数，使用图像丢失指示(PLI)包。这只适用于视频流。 |
| ssrc                        |                                                              |
| totalDecodeTime             |                                                              |
| totalInterFrameDelay        | 连续解码的帧之间的帧间延迟的总和，以秒为单位，在一帧被解码后记录 |
| totalSquaredInterFrameDelay | 连续解码的帧之间的帧间延迟的平方和，以秒为单位，在一帧被解码后记录 |
| trackId                     | 标识表示接收轨道的统计对象的字符串;该节点是两种类型之一:<br>RTCReceiverAudioTrackAttachmentStats或RTCReceiverVideoTrackAttachmentStats。<br>这个ID在多次调用getStats()时是稳定的。 |
| transportId                 |                                                              |



**outbound-rtp**

[RTCOutboundRtpStreamStats](https://developer.mozilla.org/en-US/docs/Web/API/RTCOutboundRtpStreamStats)对象，提供基于本地对等体发送到其对等体的传出数据的统计信息。

该信息只考虑出站RTP流，因此任何需要关于远程对等体状态信息(如往返时间)的数据都是不可用的，因为如果不知道其他对等体的状态，就无法计算这些值。

| 值   | 说明 |
| ---- | ---- |
|      |      |
|      |      |
|      |      |



**local-candidate**

[RTCIceCandidateStats](https://developer.mozilla.org/en-US/docs/Web/API/RTCIceCandidateStats)对象，提供关于ICE本地候选的统计信息;这些候选者可以在RTCIceTransport.getLocalCandidates()的输出中找到。

![image-20210825155253494](https://could-res-1252778021.file.myqcloud.com/img/image-20210825155253494.png)

| 值            | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| address       | 包含候选人地址的字符串。可以是IPv4地址、IPv6地址或完全限定域名。此属性以前被命名为ip，并且只接受ip地址。 |
| candidateType | 匹配rtciccandidate中的一个值的字符串。类型，指示对象为哪种候选对象提供统计信息。 |
| ip            |                                                              |
| isRemote      |                                                              |
| networkType   | 来自RTCNetworkType枚举类型的字符串，表示用于本地候选对象的接口类型。此属性仅适用于本地候选人。 |
| port          | 候选人使用的网络端口号。                                     |
| priority      | 候选人的优先级，对应于rtciccandidate .priority。             |
| protocol      | 指定用于在端口上传输数据的协议(tcp或udp)的字符串。           |
| transportId   | 一个唯一标识被检查的传输对象的字符串，以便获得与这些统计信息对应的候选对象相关联的RTCTransportStats。 |



**remote-candidate**

[RTCIceCandidateStats](https://developer.mozilla.org/en-US/docs/Web/API/RTCIceCandidateStats)对象，其中包含关于远程候选的RTCIceTransport的统计信息。

这可能包括诸如网络类型、协议、URL、所使用的中继类型等信息。

![image-20210825155519993](https://could-res-1252778021.file.myqcloud.com/img/image-20210825155519993.png)

| 值            | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| address       | 包含候选人地址的字符串。可以是IPv4地址、IPv6地址或完全限定域名。此属性以前被命名为ip，并且只接受ip地址。 |
| candidateType | 匹配rtciccandidate中的一个值的字符串。类型，指示对象为哪种候选对象提供统计信息。 |
| ip            |                                                              |
| isRemote      |                                                              |
| port          | 候选人使用的网络端口号。                                     |
| priority      | 候选人的优先级，对应于rtciccandidate .priority。             |
| protocol      | 指定用于在端口上传输数据的协议(tcp或udp)的字符串。           |
| transportId   | 一个唯一标识被检查的传输对象的字符串，以便获得与这些统计信息对应的候选对象相关联的RTCTransportStats。 |



**receiver**

提供指定rtcrtreceiver的统计信息。

如果kind是audio，该对象是RTCAudioReceiverStats对象;

如果kind是video，则该对象是RTCVideoReceiverStats对象。

| 值   | 说明 |
| ---- | ---- |
|      |      |
|      |      |
|      |      |



**sender**

一个包含RTCPeerConnection上的流的RTCRtpSender统计信息的对象。

如果kind为"audio"，该对象为RTCAudioSenderStats;

如果kind是"video"，该对象为RTCVideoSenderStats。

| 值   | 说明 |
| ---- | ---- |
|      |      |
|      |      |
|      |      |



**remote-inbound-rtp**

RTCRemoteInboundRtpStreamStats对象，提供关于出站RTP数据流的统计信息，但是从远程对等体的角度。

也就是说，该信息是关于出站rtp流的，但是由正在处理该流的远程设备看到。

您可以使用这些信息来做一些事情，比如确定远程对等端接收数据的情况。

| 值   | 说明 |
| ---- | ---- |
|      |      |
|      |      |
|      |      |



**remote-outbound-rtp**

[RTCRemoteOutboundRtpStreamStats](https://developer.mozilla.org/en-US/docs/Web/API/RTCRemoteOutboundRtpStreamStats)对象，它包含关于入站RTP(入站RTP)流的统计信息，但是从远程对等体的角度。

![image-20210825155925962](https://could-res-1252778021.file.myqcloud.com/img/image-20210825155925962.png)

| 值              | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| bytesSent       |                                                              |
| codecId         | 一个DOMString，它唯一地标识被检查的对象，该对象产生与这个RTP流关联的RTCCodecStats对象。 |
| kind            | 一个DOMString，如果相关的MediaStreamTrack是纯音频的，它的值是"audio";如果音轨包含视频，它的值是"video"。<br>该值将与RTCCodecStats指示的媒体类型匹配。编解码器，以及轨道的kind属性。以前称为mediaType。 |
| localId         | 一个DOMString，用于查找共享相同同步源(SSRC)的本地rcinboundrtpstreamstats对象。 |
| packetsSent     |                                                              |
| remoteTimestamp | DOMHighResTimeStamp，指定远端发送RTCRemoteOutboundRtpStreamStats对象中的统计信息的时间戳(在远程设备上)。<br>这与RTCStats字典中找到的时间戳不同;  它表示本地端点接收或生成对象统计信息的时间。 |
| reportsSent     |                                                              |
| ssrc            | 32位整数，标识该RTCRtpStreamStats对象覆盖的RTP包的源。这个值是根据RFC 3550规范生成的。 |
| transportId     | 一个DOMString唯一标识被检查的对象，用于生成与这个RTP流关联的RTCTransportStats对象。 |



**stream**

RTCMediaStreamStats类型的对象，提供关于作为RTCPeerConnection一部分的MediaStream的统计信息。

![image-20210825161339657](https://could-res-1252778021.file.myqcloud.com/img/image-20210825161339657.png)

| 值               | 说明 |
| ---------------- | ---- |
| id               |      |
| streamIdentifier |      |
| timestamp        |      |
| trackIds         |      |
| type             |      |



**track**

该对象是基于RTCMediaHandlerStats的类型之一

对于音频轨道，类型是RTCSenderAudioTrackAttachmentStats，

对于视频轨道，类型是RTCSenderVideoTrackAttachmentStats。

其中的数据提供了与特定MediaStreamTrack的RTCRtpSender附件相关的统计信息;还包括媒体级别的指标，随轨道。

![image-20210825160958203](https://could-res-1252778021.file.myqcloud.com/img/image-20210825160958203.png)

| 值                             | 说明 |
| ------------------------------ | ---- |
| audioLevel                     |      |
| concealedSamples               |      |
| concealmentEvents              |      |
| detached                       |      |
| ended                          |      |
| id                             |      |
| insertedSamplesForDeceleration |      |
| jitterBufferDelay              |      |
| jitterBufferEmittedCount       |      |
| kind                           |      |
| remoteSource                   |      |
| removedSamplesForAcceleration  |      |
| silentConcealedSamples         |      |
| timestamp                      |      |
| totalAudioEnergy               |      |
| totalSamplesDuration           |      |
| totalSamplesReceived           |      |
| trackIdentifier                |      |
| type                           |      |



**transport**

RTCTransportStats类型的对象，一个包含与RTCPeerConnection传输相关的统计信息的对象。

![image-20210825161430146](https://could-res-1252778021.file.myqcloud.com/img/image-20210825161430146.png)

| 值                           | 说明 |
| ---------------------------- | ---- |
| bytesReceived                |      |
| bytesSent                    |      |
| dtlsCipher                   |      |
| dtlsState                    |      |
| id                           |      |
| localCertificateId           |      |
| packetsReceived              |      |
| packetsSent                  |      |
| remoteCertificateId          |      |
| selectedCandidatePairChanges |      |
| selectedCandidatePairId      |      |
| srtpCipher                   |      |
| timestamp                    |      |
| tlsVersion                   |      |
| type                         |      |









