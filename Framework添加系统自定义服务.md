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
	```
4. 在自定义源码目录下添加CustomServiceManager.java

	```  
	```  
	
5. 在frameworks/base/services/java/com/android/server/SystemServer.java中添加自定义服务

	```  
	```  
	
6. 在frameworks/base/core/java/android/app/ContextImpl.java中注册服务

	```  
	```