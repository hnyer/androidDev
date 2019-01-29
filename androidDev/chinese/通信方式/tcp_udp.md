# tcp_udp
进行tcp通信时，需要注意处理 粘包的问题。然后控制好休眠时间 和 接收的 buffer[] 大小。

##  TCP标志位 (位码)

标记 | 含义
---|---
SYN|synchronous 建立联机
ACK|acknowledgement 确认
PSH|push传送
FIN|finish结束
RST|reset重置
URG|urgent紧急

## 术语
### Sequence number
顺序号码
### Acknowledge number
确认号码
### MSL
Maximum Segment Lifetime ，报文最长存活时间

## tcp相关问题
### 为什么连接的时候是三次握手，2次不行吗?
为了让服务器、客户端端彼此知道 自己+对方的 接收、发送功能正常。 自己模拟一下就知道至少需要三次。

![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802071556_osChina_TCP三次握手.png)

### 为什么连接的时候是三次握手，关闭的时候却是四次挥手？
```xml
关闭连接时，当Server端收到FIN报文时，很可能并不会立即关闭SOCKET，
所以只能先回复一个ACK报文，告诉Client端，"你发的FIN报文我收到了"。
只有等到我Server端所有的报文都发送完了，我才能发送FIN报文，因此不能一起发送。
故需要四步挥手。 （为了争取一段缓冲时间，完成正在进行的数据传输）
```

![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802071556_osChina_tcp四次挥手.png)

### 为什么客户端在TIME-WAIT状态必须等待2MSL的时间？
```xml
为了保证客户端发送的最后一个ACK报文段能够到达服务器。
因为这个ACK报文段有可能丢失，因而使处在LAST-ACK状态的服务器收不到对已发送的FIN+ACK报文段的确认。
服务器会超时重传这个FIN+ACK报文段。而客户端就能在2MSL时间内收到这个重传的FIN+ACK报文段。
接着客户端重传一次确认，重新启动2MSL计时器。
最后客户端和服务器都正常进入到CLOSED状态。
```


##  粘包处理
目前不是最优方法，速度有点慢。想到高效的方法再补充。
```java
public class AirUnPackTools{
    /**帧头*/
    private static final byte packHead1= (byte) 0xDD;
    /**是否在有效数据之内*/
    private static boolean isContinue = false ;
    private static byte mlastByte =(byte) 0x00 ;
    /**byte索引 ， 用来指示 关键字 和 长度 */
    private static  int mByteIndex = 0;
    /**当前帧的长度*/
    private static  int mFrameLengh =0 ;
    /**临时list*/
    private static List<Byte>  lastBytesList = new ArrayList<>() ;
    /**数据段 数据长度 */
    private static  int mbyteCount = 0;
    /**遥控器返回的消息类型 = 0x55 ,5a ,5b ， 00为默认状态，无意义*/
    private static byte msgType =0x00 ;

    /**
     * 对来自 遥控端的 bytes数据解包
     */
    public static DronePacket320 unPackageofGround(byte mByte){
        DronePacket320 packetGround  = null ;
        //判断是否是帧开头
        switch (mByte)  {
            case packHead1:
                // 连续出现2个DD ，说明这是帧头
                if(mlastByte == packHead1 )  {
                    lastBytesList.clear();
                    lastBytesList.add(packHead1) ;
                    mByteIndex = 0;
                    isContinue = true ;
                }
                break;
        }

        if(isContinue) {
           boolean reslut =  getUnpackagedFrame(mByte);
            if(reslut)  {
                packetGround = new DronePacket320() ;
                packetGround.msgType = msgType ;
                packetGround.bytes =  listToBytesArray(lastBytesList) ;
            }else{
                packetGround = null ;
            }
        }else{
            packetGround = null ;
        }
        mlastByte =  mByte ;
        return  packetGround ;
    }

    public static byte [] listToBytesArray(List<Byte> list)  {
        byte[] bytes = new byte[list.size()] ;
        for(int i= 0; i<list.size() ;i++)  {
            bytes[i] = list.get(i) ;
        }
        return  bytes;
    }

    private static boolean getUnpackagedFrame( byte mBytes ){
        mByteIndex ++ ;

        if(mByteIndex == 2)   {  // 帧类型
            lastBytesList.add(mBytes) ;
            msgType  =mBytes;
            return  false ;
        }else  if(mByteIndex == 3)  {  //长度
            lastBytesList.add(mBytes) ;
            mbyteCount = 0;
            mFrameLengh = MyStringUtils.byte1ToInt(mBytes) ;
            return  false ;
        } else{
            mbyteCount ++ ;
            lastBytesList.add(mBytes) ;
            if(mbyteCount == mFrameLengh +1 )  {
                //数据段+校验位读取完毕 ，返回组装标记true
                isContinue =false ;
                return  true ;
            }else {
                return  false ;
            }
        } //end  else
    }//
}

```


```java
public class DronePacket320{
    public short msgType;
    public byte[] bytes;
}
```


```java
//使用
for (int i = 0; i < bytesGet.length; i++){
    DronePacket320 packetAir = AirUnPackTools.unPackageofGround(tcpResultBean.reslut[i]);
    if (packetAir != null){
        //处理格式化后的数据...
    }
}
```


## 第三方库 netty
用来连接tcp、接收 和发送数据的

```Java
public interface MyNettyCallBack{
    /**tcp成功建立连接*/
    public static final int Key_State_connect=1 ;
    /**tcp断开*/
    public static final int Key_State_disConnect=2 ;
    /**tcp正在重连*/
    public static final int Key_State_isReConnect=3 ;
    /**连接发生异常*/
    public static final int Key_State_error=4;
    /**连接手动关闭*/
    public static final int Key_State_close=5;
    /**连接正常-正在接收数据*/
    public static final int Key_State_recevieData=6;
    /**服务器返回信息给客户端*/
    public void sendMsgToUser(byte[] reslut , int state ,String stateMsg) ;
}
```


```java
public class NettyTcpTools{
    private int port;
    private String host;
    private SocketChannel socketChannel;
    private Bootstrap bootstrap;
    private boolean isConnect = false;
    /**是否需要重连*/
    private boolean isRepeate = false;
    /**用来区分是用户主动断开还是异常导致断开<br>用户主动断开的话就不用去重连*/
    private  boolean isStopByUser =false ;
    /**通信管道*/
    private ChannelFuture future = null;
    /**状态回调*/
    private MyNettyCallBack myNettyCallBack ;
    /**线程池工具*/
    private MyThreadPoolTools myThreadPoolTools ;

    public NettyTcpTools(String host , int port){
        this.port = port;
        this.host = host;
        bootstrap = new Bootstrap();
        bootstrap.channel(NioSocketChannel.class);
        //保持长连接
        bootstrap.option(ChannelOption.SO_KEEPALIVE, true);
        //自动调整下一次缓冲区建立时分配的空间大小，避免内存的浪费
        bootstrap.option(ChannelOption.RCVBUF_ALLOCATOR, new AdaptiveRecvByteBufAllocator(1024, 1024 * 32, 1024 * 64));
        bootstrap.group( new NioEventLoopGroup());
        bootstrap.remoteAddress(host, port);
        bootstrap.handler(new ChannelInitializer<SocketChannel>() {
            @Override
            protected void initChannel(SocketChannel socketChannel) throws Exception {
                //超时处理
                socketChannel.pipeline().addLast(new IdleStateHandler(3, 3, 3 ,TimeUnit.SECONDS));
                //接收服务器信息
                socketChannel.pipeline().addLast(  new NettyClientHandler());
            }
        });
    }


    private void gotoConnectTcp(){
        closeThreadPool();
        myThreadPoolTools= new MyThreadPoolTools();

        Runnable task = new Runnable() {
            @Override
            public void run() {
                isStopByUser = false ;
                try {
                    if (future != null)  {
                        boolean isActive = future.channel().isActive() ;
                        boolean isOpen = future.channel().isOpen() ;
                        MyLogUtils.mLog_iNormal("连接测试：isActive=" + isActive +"  isOpen="+ isOpen);
                       /* 这个地方注意了： 如果 没有关闭，会导致多个tcp连接
                        使用 网络调试助手.exe 进行调试查看*/
                        future.channel().close();
                    }

                    future = bootstrap.connect(new InetSocketAddress(host, port)).sync();
                    if (future.isSuccess())  {
                        socketChannel = (SocketChannel) future.channel();
                        sendMsgToUser(null ,MyNettyCallBack.Key_State_connect ,"connect server  Success");
                        isConnect = true;
                        return;
                    }
                } catch (Exception cause) {
                    sendMsgToUser(null ,MyNettyCallBack.Key_State_error ,"error2:"+cause.toString());
                }
                //连接状态在此处处理
                repeateTcp();
            }
        };
        if (myThreadPoolTools == null) {
            myThreadPoolTools= new MyThreadPoolTools();
        }
        myThreadPoolTools.addTask(task);
    }


    public  void startTcpTools(){
      gotoConnectTcp();
    }

    private void closeThreadPool(){
        if(myThreadPoolTools!=null)  {
            myThreadPoolTools.shutdownThreadPool();
            myThreadPoolTools=null ;
        }
    }


    public void nettySendBytes(final  byte[] bytesSend)  {
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                if (isConnect)  {
                    ByteBuf buf = Unpooled.buffer(bytesSend.length);
                    buf.writerIndex();
                    buf.writeBytes(bytesSend);
                    socketChannel.writeAndFlush(buf);
                }
            }
        } ;
        if (myThreadPoolTools == null) {
            myThreadPoolTools= new MyThreadPoolTools();
        }
        myThreadPoolTools.addTask(runnable);
    }


    public class NettyClientHandler extends SimpleChannelInboundHandler<ByteBuf>{
        @Override
        protected void channelRead0(ChannelHandlerContext channelHandlerContext,ByteBuf byteBuf) throws Exception
        {
            //这里是接受服务端发送过来的消息
            byte[] result  = new byte[byteBuf.readableBytes()];
            byteBuf.readBytes(result);
            /*这种方式会定长读取，后面有很多00 00 00
            byte[] bytes = byteBuf.array();*/
            sendMsgToUser(result ,MyNettyCallBack.Key_State_recevieData ,"channelRead0");
        }


        /**这里是断线要进行的操作*/
        @Override
        public void channelInactive(ChannelHandlerContext ctx) throws Exception  {
            super.channelInactive(ctx);
            ctx.close() ;
            if(!isStopByUser)  {
                sendMsgToUser(null ,MyNettyCallBack.Key_State_disConnect ,"channelInactive");
                repeateTcp();
            }
        }

        /**这里是出现异常的话要进行的操作*/
        @Override
        public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
            //ctx.close() ;
            sendMsgToUser(null ,MyNettyCallBack.Key_State_error ,"error1:"+cause.toString());
            repeateTcp();
        }

        /**用来处理读写超时的 自定义操作*/
        @Override
        public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception  {
            super.userEventTriggered(ctx, evt);
            if (evt instanceof IdleStateEvent) {
                IdleStateEvent event = (IdleStateEvent) evt;

                if (event.state().equals(IdleState.READER_IDLE)) {
                    // 在指定的时间内没有收到服务器的反馈信息
                } else if (event.state().equals(IdleState.WRITER_IDLE)) {
                    //在指定的时间内没有像服务器发送过信息
                } else if (event.state().equals(IdleState.ALL_IDLE)) {
                    //在指定的时间内 既没有收到过信息 也没有发送过信息
                }

            }
        }
    }


    /**
     * 设置消息回调接口，用来获取从服务器返回的消息。
     */
    public void setMyNettyCallBack(MyNettyCallBack myNettyCallBack)  {
        this.myNettyCallBack =  myNettyCallBack ;
    }

    private void sendMsgToUser(byte[] result , int state ,String sendMsgToUser)  {
        if (myNettyCallBack != null) {
            myNettyCallBack.sendMsgToUser(result ,state ,sendMsgToUser);
        }
    }

    /**
     * tcp重连
     */
    private void repeateTcp()  {
        if(isStopByUser)    {
           return;
        }
        if (!isRepeate) {
            if (isConnect)  {
                isConnect = false;
                //断线监听在此处处理
            }
            isRepeate = true;
            sendMsgToUser(null ,MyNettyCallBack.Key_State_isReConnect ,"repeateTcp");
            try {
                TimeUnit.SECONDS.sleep(5);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            isRepeate = false;
            startTcpTools();
        }
    }

    /***
     * 关闭 tcp连接
     */
    public void nettyToolsClose()  {

        resetState();
        closeThreadPool();
        if (future != null && future.channel() != null)  {
            if (future.channel().isOpen())
            {
                sendMsgToUser(null ,MyNettyCallBack.Key_State_close ,"close");
                future.channel().close();

            }
        }
    }

    private void resetState()  {
        isConnect = false;
        isRepeate=false;
        isStopByUser = true ;
    }
}
```
