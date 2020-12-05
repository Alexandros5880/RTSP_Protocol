# RTSP Protocol:

#### OPTIONS:
    Client->Server:
        OPTIONS rtsp://example.com/media.mp4 RTSP/1.0
        CSeq: 1
        Require: implicit-play
        Proxy-Require: gzipped-messages

    Server->Client:
        RTSP/1.0 200 OK
        CSeq: 1
        Public: DESCRIBE, SETUP, TEARDOWN, PLAY, PAUSE
           
#### DESCRIBE:
    Client->Server:
        DESCRIBE rtsp://example.com/media.mp4 RTSP/1.0
        CSeq: 2

    Server->Client:
        RTSP/1.0 200 OK
        CSeq: 2
        Content-Base: rtsp://example.com/media.mp4
        Content-Type: application/sdp
        Content-Length: 460
        
        m=video 0 RTP/AVP 96
        a=control:streamid=0
        a=range:npt=0-7.741000
        a=length:npt=7.741000
        a=rtpmap:96 MP4V-ES/5544
        a=mimetype:string;"video/MP4V-ES"
        a=AvgBitRate:integer;304018
        a=StreamName:string;"hinted video track"
        m=audio 0 RTP/AVP 97
        a=control:streamid=1
        a=range:npt=0-7.712000
        a=length:npt=7.712000
        a=rtpmap:97 mpeg4-generic/32000/2
        a=mimetype:string;"audio/mpeg4-generic"
        a=AvgBitRate:integer;65790
        a=StreamName:string;"hinted audio track"
        
#### SETUP:
    Client->Server:
        SETUP rtsp://example.com/media.mp4/streamid=0 RTSP/1.0
        CSeq: 3
        Transport: RTP/AVP;unicast;client_port=8000-8001
    Server->Client:
        RTSP/1.0 200 OK
        CSeq: 3
        Transport: RTP/AVP;unicast;client_port=8000-8001;server_port=9000-9001;ssrc=1234ABCD
        Session: 12345678
    Client->Server:
        SETUP rtsp://example.com/media.mp4/streamid=1 RTSP/1.0
        CSeq: 3
        Transport: RTP/AVP;unicast;client_port=8002-8003
        Session: 12345678
    Server->Client:
        RTSP/1.0 200 OK
        CSeq: 3
        Transport: RTP/AVP;unicast;client_port=8002-8003;server_port=9002-9003;ssrc=1234ABCD
        Session: 12345678
        
#### PLAY:
    Client->Server:
        PLAY rtsp://example.com/media.mp4 RTSP/1.0
        CSeq: 4
        Range: npt=5-20
        Session: 12345678
    
    Server->Client:
        RTSP/1.0 200 OK
        CSeq: 4
        Session: 12345678
        RTP-Info: url=rtsp://example.com/media.mp4/streamid=0;seq=9810092;rtptime=3450012
        
#### PAUSE:
    Client->Server:
        PAUSE rtsp://example.com/media.mp4 RTSP/1.0
        CSeq: 5
        Session: 12345678
    
    Server->Client:
        RTSP/1.0 200 OK
        CSeq: 5
        Session: 12345678
        
#### RECORD:
    Client->Server:
        RECORD rtsp://example.com/media.mp4 RTSP/1.0
        CSeq: 6
        Session: 12345678
    
    Server->Client:
        RTSP/1.0 200 OK
        CSeq: 6
        Session: 12345678
        
#### ANNOUNCE:
    Client->Server:
        ANNOUNCE rtsp://example.com/media.mp4 RTSP/1.0
        CSeq: 7
        Date: 23 Jan 1997 15:35:06 GMT
        Session: 12345678
        Content-Type: application/sdp
        Content-Length: 332
    
        v=0
        o=mhandley 2890844526 2890845468 IN IP4 126.16.64.4
        s=SDP Seminar
        i=A Seminar on the session description protocol
        u=http://www.cs.ucl.ac.uk/staff/M.Handley/sdp.03.ps
        e=mjh@isi.edu (Mark Handley)
        c=IN IP4 224.2.17.12/127
        t=2873397496 2873404696
        a=recvonly
        m=audio 3456 RTP/AVP 0
        m=video 2232 RTP/AVP 31
    
    Server->Client:
        RTSP/1.0 200 OK
        CSeq: 7
        
#### TEARDOWN:
    Client->Server:
        TEARDOWN rtsp://example.com/media.mp4 RTSP/1.0
        CSeq: 8
        Session: 12345678
    
    Server->Client:
        RTSP/1.0 200 OK
        CSeq: 8
        
#### GET_PARAMETER:
   Client->Server:
        GET_PARAMETER rtsp://example.com/media.mp4 RTSP/1.0
        CSeq: 9
        Content-Type: text/parameters
        Session: 12345678
        Content-Length: 15
    
          packets_received
          jitter
    
    Server->Client:
        RTSP/1.0 200 OK
        CSeq: 9
        Content-Length: 46
        Content-Type: text/parameters
        
        packets_received: 10
        jitter: 0.3838
        
#### SET_PARAMETER:
    Client->Server:
        SET_PARAMETER rtsp://example.com/media.mp4 RTSP/1.0
        CSeq: 10
        Content-length: 20
        Content-type: text/parameters
    
        barparam: barstuff
    
    Server->Client:
        RTSP/1.0 451 Invalid Parameter
        CSeq: 10
        Content-length: 10
        Content-type: text/parameters
        
        barparam
        
#### REDIRECT:
    Server->Client:
        REDIRECT rtsp://example.com/media.mp4 RTSP/1.0
        CSeq: 11
        Location: rtsp://bigserver.com:8001
        Range: clock=19960213T143205Z-
        
        
#### Embedded (Interleaved) Binary Data:
    Client->Server:
        SETUP rtsp://example.com/media.mp4 RTSP/1.0
        CSeq: 3
        Transport: RTP/AVP/TCP;interleaved=0-1
    
    Server->Client:
        RTSP/1.0 200 OK
        CSeq: 3
        Date: 05 Jun 1997 18:57:18 GMT
        Transport: RTP/AVP/TCP;interleaved=0-1
        Session: 12345678
    
    Client->Server:
        PLAY rtsp://example.com/media.mp4 RTSP/1.0
        CSeq: 4
        Session: 12345678
    
    Server->Client:
        RTSP/1.0 200 OK
        CSeq: 4
        Session: 12345678
        Date: 05 Jun 1997 18:59:15 GMT
        RTP-Info: url=rtsp://example.com/media.mp4;seq=232433;rtptime=972948234
    
    Server->Client:
        $\000{2 byte length}{"length" bytes data, w/RTP header}
    Server->Client:
        $\000{2 byte length}{"length" bytes data, w/RTP header}
    Server->Client:
        $\001{2 byte length}{"length" bytes  RTCP packet}