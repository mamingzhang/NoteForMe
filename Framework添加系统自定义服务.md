#Framework添加系统自定义服务（Android5.1）  
***  

1. 在framework/base/core/java/android/app/目录（后面添加自定义服务都是在自己定义的包目录下，组合定义包目录可参考其他文章）下添加aidl文件ICustomService.aidl

	```  
	interface ICustomService {  
		void customService();
	}
	```  
  
2. 修改framework/base/Android.mk，将ICustomService.aidl添加到文件中，否则编译通不过

	```
	LOCAL_SRC_FILES += frameworks/base/core/java/custom/package/ICustomService.aidl
	```  
	
3. 在自定义源码目录下添加CustomService.java，实现ICustomService.aidl

	```  
	public class CustomService extends ICustomService.Stub {

    	private static final String TAG = CustomService.class.getSimpleName();

    	public static final String SERVICE_NAME = "custom_service";

    	public void customService() {
        	Log.v(TAG, "customService called");
    	}
	}
	```
	
4. 在自定义源码目录下添加CustomServiceManager.java

	```  
	public class CustomServiceManager {

    	private Context context;
    	private ICustomService customService;

    	public static ICustomService getCustomService() {
        	IBinder binder = ServiceManager.getService(CustomService.SERVICE_NAME);
        	return ICustomService.Stub.asInterface(binder);
    	}

    	public CustomServiceManager(Context context, ICustomService customService) {
        	this.context = context;
        	this.customService = customService;
    	}

    	public void customService() {
        	if (customService != null) {
            	try {
                	customService.customService();
            	} catch (RemoteException ex) {
                	ex.printStackTrace();
            	}
        	}
    	}
	}
	```  
	
5. 在frameworks/base/services/java/com/android/server/SystemServer.java中添加自定义服务

	``` 
	try {
    	Slog.i(TAG, "Custom Service");  
      	ServiceManager.addService(CustomService.SERVICE_NAME, new CustomService());
    } catch (Throwable e) {    
    	reportWtf("starting Custom Service", e);
    }
	```  
	
6. 在frameworks/base/core/java/android/app/ContextImpl.java中注册服务

	```  
	registerService(CustomService.SERVICE_NAME, new ServiceFetcher() {
    	@Override    
    	public Object createService(ContextImpl ctx) {        
    		return new CustomServiceManager(ctx, CustomServiceManager.getCustomService());    
    		}
    	});
	```  
	
7. 由于Android5.1中有SeAndroid系统，这个系统中维护了一个系统服务的白名单，如果你要添加的系统服务不在这个白名单中，那么添加系统服务将会失败，这个白名单的路径是：external/sepolicy/service_contexts  

	```  
	custom_service 		u:object_r:system_server_service:s0
	```
