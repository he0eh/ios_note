#Multithreading
##1、在另外的线程中执行block
	dispatch_queue_t queue = _;
	dispatch_async(queue, ^{ });
	
##2、获取主线程（UI线程）
	dispatch_queue_t mainQ = dispatch_get_main_queue();
	NSOperationQueue *mainQ = [NSOperationQueue mainQueue];
	
##3、创建队列
	 dispatch_queue_t otherQ ＝ dispatch_queue_create("name", NULL);
	 
##4、