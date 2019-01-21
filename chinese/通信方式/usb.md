# usb 通信
## [AOA](https://developer.android.com/adk/index.html )协议
```xml
Google推出的 Android开放配件协议AOA（Android Open Accessory Protocol）及配件开发工具包ADK（Accessory Development Kit）
提供了Android设备与Android配件通过USB或蓝牙进行通信的API，
为基于Android系统的智能设备控制外设提供了条件。
利用Android，系统可以连接从家用电器到重型机械、机器人等多种设备。
```

当前项目用到的是 android 的[AOA协议](http://www.hackermi.com/2015-04/aoa-analyse/)。遥控器作为主机供电，移动设备（手机、平板）作为从机。
使用方法请自行查阅相关文档。

## 使用场景一 ：android设备作为从机
[参考资料](http://www.hackermi.com/2015-04/aoa-analyse/)

![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201801231626_osChina_usb配件模式.png)

这种模式是Android定义的一种新的通讯模式，它将usb配件作为协议交互的主要角色，配件内置USB Embedded Host端，可以为Android手机供电，并且识别Android手机，建立数据通道。使得配件成为一个简化版的PC Host端。


## usb通信demo
### MusbAccessoryManager.java
```java
/**
 * usb 操作管理类
 */
public class MusbAccessoryManager
{
	private Context mCtx;
	/**文件操作符*/
	private ParcelFileDescriptor fileDescriptor;
	/**输入流*/
	private FileInputStream inputStream;
	/**输出流*/
	private FileOutputStream outputStream;
	/**读线程*/
	private AccessoryReadThread readThread;
	/**写线程*/
	private AccessoryWriteThread writeThread;
	/**解析线程*/
	private BytesDataRepackThread repackThread ;
	/**usb 通信是否在进行*/
	private boolean enableThread;
	/**发送队列*/
	private BlockingQueue<MyBytesTransfer> outQueue;
	/**读取队列*/
	private BlockingQueue<byte[]> readQuene;
	/**回调接口 - 状态标记*/
	private IOperateResultCallBack iUsbResultCallBack ;
	/**回调接口 - byte[] 传输*/
	//private IBytesTransferInterface iGetBytesInterface ;
	/**解析后的数据对象 回调-*/
    private IUsbPackageTransferInterface iusbpackageTransInterface ;
	/**读入流 时的休眠间隔  微秒*/
	private final long READ_STRING_SLEEPTIME= 20L;
	/**写入流 时的休眠间隔*/
	private final long WRITE_STRING_SLEEPTIME= 60L;
	/**解析 byte[] 时的休眠间隔*/
	private final long REPACKAGE_SLEEPTIME= 0L;
	/** 调整读取大小*/
	private byte[] buffer= new byte[2048 * 3] ;

	public MusbAccessoryManager(Context context  , IOperateResultCallBack iUsbResultCallBack ,
								IUsbPackageTransferInterface iusbpackageTransInterface)	{
		this.mCtx = context;
		fileDescriptor = null;
		inputStream = null;
		outputStream = null;
		readThread = null;
		writeThread = null;
		repackThread =null ;
		enableThread = false;
		this.iUsbResultCallBack= iUsbResultCallBack;
		this.iusbpackageTransInterface = iusbpackageTransInterface;
		outQueue = new LinkedBlockingQueue< >(Integer.MAX_VALUE);
		readQuene = new LinkedBlockingQueue<>(Integer.MAX_VALUE) ;
	}

	/**
	 * 添加数据 到发送队列
	 */
	public void addMessage(MyBytesTransfer msg)	{
		try	{
			outQueue.put(msg);
		}catch (Exception e)	{
			MyLogUtils.mLog_iNormal("usb设备== 发送异常1 ="+e.getMessage());
		}
	}

	/**
	 * 打开USB通信
	 */
	public void OpenAccessory(UsbAccessory accessory)  {
		if (!enableThread)		{
	    	fileDescriptor = ((UsbManager) mCtx.getSystemService(Context.USB_SERVICE)).openAccessory(accessory);
	    	if (fileDescriptor != null)	{
	    		FileDescriptor fd = fileDescriptor.getFileDescriptor();
	    		inputStream = new FileInputStream(fd);
	    		outputStream = new FileOutputStream(fd);

	    		if (inputStream == null || outputStream == null)	{
					// 读取流异常
					iUsbResultCallBack.onResultCallBack(MyStateConfig.STATE_2);
					return;
				}
				//连接成功
				iUsbResultCallBack.onResultCallBack(MyStateConfig.STATE_14);
    			enableThread = true;
    			readThread = new AccessoryReadThread(inputStream);
    			readThread.start();
    			writeThread = new AccessoryWriteThread(outputStream);
    			writeThread.start();
				repackThread = new BytesDataRepackThread() ;
				repackThread.start();
	    	}else{
				toastMsg("fileDescriptor==null "   ) ;
			}
		}
    }//


	/**
	 * 关闭连接、清空队列、停止线程
	 */
    public void CloseAccessory()  {
    	try	{
			if (fileDescriptor != null)		{
				fileDescriptor.close();
			}
			if (outputStream != null)		{
				outputStream.close();
			}
			if (inputStream != null)	{
				inputStream.close();
			}
		}catch (Exception ie)	{
			ie.printStackTrace();
		}
		outQueue.clear();
		readQuene.clear();
		enableThread = false;
    	fileDescriptor = null;
    	outputStream = null;
    	inputStream = null;
    }

    /**
     读取子线程
     */
	private class AccessoryReadThread extends Thread  {
    	private FileInputStream inStream;

    	public AccessoryReadThread(FileInputStream stream) 	{
			  this.inStream = stream;
	   	}
    	@Override
    	public void run() 	{
			while (enableThread) 	{
				try {
					int realReaded = inStream.read(buffer);
					// 将Buffer中有效的数据拷贝出来
					if(realReaded>0) 	{
						byte [] realBytes = MyStringUtils.subBytes(buffer, 0 , realReaded) ;
						readQuene.put(realBytes);
					}
				} catch (Exception e) {
					com.walkera.base.utils.MyLogUtils.mLog_iNormal("usb设备== 接收异常="+e.getMessage());
					MyApplication.usbReadStreamState=  MyStateConfig.STATE_8;
					//发生异常，继续执行
					continue;
				}
				MyApplication.usbReadStreamState=  MyStateConfig.STATE_9;
				try {
					TimeUnit.MICROSECONDS.sleep(READ_STRING_SLEEPTIME);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			} // end while

    	} // end run
    }//

	/**
     *发送子线程
	 */
	private class AccessoryWriteThread extends Thread 	{
		private FileOutputStream outStream;
		public AccessoryWriteThread(FileOutputStream outputStream) 	{
			this.outStream = outputStream;
		}

		@Override
		public void run()  {
			MyBytesTransfer msg;
			while (enableThread){
				try{
					if (outStream != null)	{
						msg = outQueue.poll();
						if (msg != null)	{
							sendByteDataToServer(msg.bytes );
							msg = null;
						}else{
						}
					} else{
						MyApplication.usbWriteStreamState=  MyStateConfig.STATE_11;
						outQueue.clear();
					}
					MyApplication.usbWriteStreamState=  MyStateConfig.STATE_10;
					try {
						Thread.sleep(WRITE_STRING_SLEEPTIME);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}catch (Exception ex)
				{
					com.walkera.base.utils.MyLogUtils.mLog_iNormal("发送测试3=  msg="+ex.getMessage());
					MyApplication.usbWriteStreamState=  MyStateConfig.STATE_11;
				}
			}
		}

		/**
		 * 将数据发送给服务器
    */
		private void sendByteDataToServer(byte[] buffer) {
	    	try	{
	    		if (outStream != null)	{
					iUsbResultCallBack.onResultCallBack(MyStateConfig.STATE_13);
					outStream.write(buffer, 0, buffer.length);
					outStream.flush();
		    	}
	    	}catch (IOException ex)	{
				iUsbResultCallBack.onResultCallBack(MyStateConfig.STATE_12);
	    	}
	    }//
	}

	/**
	 * 解析 重组包 子线程
	 */
	private class BytesDataRepackThread extends Thread{
		@Override
		public void run()	{
			while (enableThread)	{
				if(readQuene!=null)		{
					byte[] realBytes =	readQuene.poll() ;
					if(realBytes!=null)	{
						rePackageForBytes(realBytes) ;
					}
				}
				try	{
					Thread.sleep(REPACKAGE_SLEEPTIME);
				} catch (InterruptedException e){
					e.printStackTrace();
				}

			} //  while
		}//end run
	}

	/***
	 * 重组包 ， 将遥控器 、 飞控、视屏数据分开 .
   */
	private void rePackageForBytes(byte[] bytes)	{
		if(bytes!=null)	{
			int size =bytes.length ;
			for(int i=0 ; i< size ;i++)	{
				MyBytesTransfer myBytesTransfer = MyUsbUnPackTools.unPackageofUsb(bytes[i]) ;
				if(myBytesTransfer!=null)	{
					iusbpackageTransInterface.transferUsbPackageBean(myBytesTransfer);
				}
			}
		} // end if
	} //

	private void toastMsg(String msg){
		Toast.makeText(mCtx, msg ,Toast.LENGTH_SHORT) .show();
	}
}
```

### UsbMsgClient.java
```java
/**
 * 功能描述：  负责 用usb方式的通信方式 给其他部件提供发送和接收数据的功能 <br>
 */
public class UsbMsgClient{
    private Context mCtx ;
    /**usb管理类*/
    private UsbManager usbmanager ;
    /**usb 操作管理类*/
    private MusbAccessoryManager mubAccessoryManager;
    /**对外暴露的接口 ，以便其他模块获取 对应的数据*/
    private IUsbMsgBackInterface iUsbMsgBackInterface ;

    /**
     * @param iUsbMsgBackInterface   对外暴露的接口 ，以便其他模块获取 对应的数据
     * @param iUsbResultCallBack  usb 返回状态码
     */
    public UsbMsgClient (Context mCtx ,IUsbMsgBackInterface iUsbMsgBackInterface ,IOperateResultCallBack iUsbResultCallBack)  {
        this.mCtx = mCtx;
        this.iUsbMsgBackInterface= iUsbMsgBackInterface ;
        usbmanager=((UsbManager) mCtx.getSystemService(Context.USB_SERVICE)) ;
        mubAccessoryManager = new MusbAccessoryManager(mCtx , iUsbResultCallBack   ,iusbpackageTransInterface);
        discoverAccessories();
    }//

    /**回调 来自usb的数据*/
    private IUsbPackageTransferInterface iusbpackageTransInterface = new IUsbPackageTransferInterface()  {
        @Override
        public void transferUsbPackageBean(MyBytesTransfer myBytesTransfer)  {
            if(myBytesTransfer != null)    {
                // 4759图传  -  3678飞控    - 5869 遥控器
                if( Arrays.equals(myBytesTransfer.msgType, MyStateConfig.VEDEO_DATA_RECEIVE))  {  //视屏数据
                    iUsbMsgBackInterface.getUsbVedioByte(myBytesTransfer);
                }else if(Arrays.equals(myBytesTransfer.msgType, MyStateConfig.FC_DATA_RECEIVE))  {
                    //飞控数据
                    iUsbMsgBackInterface.getUsbFcByte(myBytesTransfer);
                }else if(Arrays.equals(myBytesTransfer.msgType, MyStateConfig.RC_DATA_RECEIVE))  {
                    //遥控器数据
                    iUsbMsgBackInterface.getUsbRcByte(myBytesTransfer);

                }
            }
        }
    } ;

    /**
     * 扫描usb设备 发现从设备
     */
    private void discoverAccessories()  {
        UsbAccessory[] list = usbmanager.getAccessoryList();
        if (list != null && list.length!=0)  {
            openTheAccessory(list[0]);
        }else  {
            //MyToastTools.toastWarning(mCtx ,mCtx.getString( R.string.v5_str_1));
        }
    }//

    /**
     * 连接 指定设备
     */
    private void openAccessory(UsbAccessory accessory)  {
        mubAccessoryManager.OpenAccessory(accessory);
        // 延时 n秒 发送打开session命令
        new Handler().postDelayed(new Runnable()  {
            @Override
            public void run()  {
                byte[] openSession = { (byte)0xDD , (byte)0xDF  ,(byte)0x06 ,(byte)0x00 } ;
                sendBytesToDevices(openSession);
            }
        } , 2000) ;
    }

    /**
     * 连接USB设备
     */
    private void openTheAccessory(UsbAccessory accessory)  {
        if (accessory == null)  {
            MyToastTools.toastWarning(mCtx ,mCtx.getString( R.string.v5_str_2));
        }else {
            if (usbmanager.hasPermission(accessory))  {
                openAccessory(accessory);
            }else  {
                com.walkera.base.utils.MyLogUtils.mLog_iNormal("usb设备== 无权限1");
            }
        }
    }

    /**
     * 通知遥控器关闭
     */
    private void closeSession()  {
        byte[] closeSession = { (byte)0xDD , (byte)0xDF  ,(byte)0x07 ,(byte)0x00 } ;
        sendBytesToDevices(closeSession);
    }

    /**
     * 关闭usb收发工具
     */
    public void closeUsbMsgClinet()  {
        closeSession();
        new Handler().postDelayed(new Runnable()  {
            @Override
            public void run()  {
                closeAccessory();
            }
        }, 500);
    }

    /**
     * 断开设备
     */
    private void closeAccessory(){
        if (mubAccessoryManager != null)  {
            mubAccessoryManager.CloseAccessory();
        }
    }

    /**
     * 发送数据
     */
    public void sendBytesToDevices( byte[] data )  {
        if (mubAccessoryManager != null)  {
            mubAccessoryManager.addMessage(new MyBytesTransfer(data , MyStateConfig.STATE_6,null));
        }
    }
}
```



### MyStateConfig.java
```java
public class MyStateConfig{
    /**请求usb访问的自定义权限*/
    public static final String ACTION_USB_ACCESSORY_PERMISSION = "com.wk.USB_ACCESSORY_PERMISSION";
    /**当前参数无意义 */
    public final  static int STATE_NULL = -100 ;
    /**所有的状态的初始化状态 */
    public final  static int STATE_0 = 0 ;
    /** usb退出 */
    public final  static int STATE_1 =1 ;
    /** usb连接异常 , 读取流异常 */
    public final static int STATE_2 =2 ;
    /** usb 权限被拒绝了 */
    public final static int STATE_3 =3;
    /**接收usb byte 正式*/
    public final static int STATE_4 =4;
    /**接收usb byte 测试*/
    public final static int STATE_5 =5;
    /**发送的bytes[]*/
    public final static int STATE_6 =6;
    /**接收到的byte[]*/
    public final static int STATE_7 =7;
    /**usb输入流异常*/
    public final static int STATE_8 =8;
    /**usb输入流正常*/
    public final static int STATE_9 =9;
    /**usb输出流正常*/
    public final static int STATE_10 =10;
    /**usb输出流异常*/
    public final static int STATE_11 =11;
    /**发送失败*/
    public final static int STATE_12 =12;
    /**发送成功*/
    public final static int STATE_13 =13;
    /**Usb连接成功*/
    public final static int STATE_14 =14;
    /**属于相机数据 */
    public final  static int STATE_15 = 15 ;
}
```

### xml中的一些配置、usb插入、拔出监听
请参考官网

### 使用
```java
/**usb收发工具*/
private UsbMsgClient usbMsgClient ;
//初始化usb数据接收器
usbMsgClient = new UsbMsgClient( mContext ,iUsbMsgBackInterface ,iUsbResultCallBack) ;
//发送byte[]数据
usbMsgClient.sendBytesToDevices(code);
//关闭连接
usbMsgClient.closeUsbMsgClinet();

/**
 * 接收 usb 返回数据
 */
private IUsbMsgBackInterface iUsbMsgBackInterface = new IUsbMsgBackInterface(){
    /**视屏数据*/
    @Override
    public void getUsbVedioByte(MyBytesTransfer vedioData)  {
        //解码...
    }

    /**飞控数据*/
    @Override
    public void getUsbFcByte(MyBytesTransfer fcData)  {
        //解析飞控数据
    }

    /**遥控器数据*/
    @Override
    public void getUsbRcByte(MyBytesTransfer rcData)  {
        //解析遥控器数据
    }
} ;

/**
 * 接收 usb 操作、状态码
 */
private IOperateResultCallBack iUsbResultCallBack = new IOperateResultCallBack(){
    @Override
    public void onResultCallBack(int state ){
        //状态码
    }
};
```
