### SimpleDateFormat线程不安全

1. 验证SimpleDateFormat线程不安全

```java
public class SimpleDateFormatDemoTest {
    private static SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    public static void main(String[] args) {
        //1、创建线程池
        ExecutorService pool = Executors.newFixedThreadPool(5);
        //2、为线程池分配任务
        ThreadPoolTest threadPoolTest = new ThreadPoolTest();
        for (int i = 0; i < 10; i++) {
            pool.submit(threadPoolTest);
        }
        //3、关闭线程池
        pool.shutdown();
    }

    static class ThreadPoolTest implements Runnable {

        @Override
        public void run() {
            String dateString = simpleDateFormat.format(new Date());
            try {
                Date parseDate = simpleDateFormat.parse(dateString);
                String dateString2 = simpleDateFormat.format(parseDate);
                System.out.println(Thread.currentThread().getName() + " 线程是否安全: " + dateString.equals(dateString2));
            } catch (Exception e) {
                System.out.println(Thread.currentThread().getName() + " 格式化失败 ");
            }
        }
    }
}
```

2. 解决方案
	1、不要定义为static变量，使用局部变量(**不建议在高并发场景下使用**)
	
	```
	 static class  ThreadPoolTest implements Runnable{
   
        @Override
        public void run() {
            SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
            String dateString = simpleDateFormat.format(new Date());
            try {
                Date parseDate = simpleDateFormat.parse(dateString);
                String dateString2 = simpleDateFormat.format(parseDate);
                System.out.println(Thread.currentThread().getName()+" 线程是否安全: "+dateString.equals(dateString2));
            } catch (Exception e) {
                System.out.println(Thread.currentThread().getName()+" 格式化失败 ");
            }
	     }
    }
   ```
   2、加锁：synchronized锁和Lock锁(**不建议在高并发场景下使用**)
   
   ```
       # 添加synchronized
       static class  ThreadPoolTest implements Runnable{
           @Override
           public void run() {
               try {
                   synchronized (simpleDateFormat){
                       String dateString = simpleDateFormat.format(new Date());
                       Date parseDate = simpleDateFormat.parse(dateString);
	                    String dateString2 = simpleDateFormat.format(parseDate);
	                    System.out.println(Thread.currentThread().getName()+" 线程是否安全: "+dateString.equals(dateString2));
	                }
	            } catch (Exception e) {
	                System.out.println(Thread.currentThread().getName()+" 格式化失败 ");
	            }
	        }
	        
		# 添加Lock锁
	 	private static SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
	 	private static Lock lock = new ReentrantLock();
	
	    static class  ThreadPoolTest implements Runnable{
	
	        @Override
	        public void run() {
	            try {
	                lock.lock();
	                String dateString = simpleDateFormat.format(new Date());
	                Date parseDate = simpleDateFormat.parse(dateString);
	                String dateString2 = simpleDateFormat.format(parseDate);
	                System.out.println(Thread.currentThread().getName()+" 线程是否安全: "+dateString.equals(dateString2));
	            } catch (Exception e) {
	                System.out.println(Thread.currentThread().getName()+" 格式化失败 ");
	            }finally {
	                lock.unlock();
	            }
	        }
	```
	
	3、使用ThreadLocal方式
	
	```
	private  static ThreadLocal<SimpleDateFormat> threadLocal = ThreadLocal.withInitial(()->new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));
	
	static class  ThreadPoolTest implements Runnable{
	
	        @Override
	        public void run() {
	            try {
	                    String dateString = threadLocal.get().format(new Date());
	                    Date parseDate = threadLocal.get().parse(dateString);
	                    String dateString2 = threadLocal.get().format(parseDate);
	                    System.out.println(Thread.currentThread().getName()+" 线程是否安全: "+dateString.equals(dateString2));
	            } catch (Exception e) {
	                System.out.println(Thread.currentThread().getName()+" 格式化失败 ");
	            }finally {
	                //避免内存泄漏，使用完threadLocal后要调用remove方法清除数据
	                threadLocal.remove();
	            }
	        }
	    }
	```
	
	
	
	4、使用DateTimeFormatter代替SimpleDateFormat（DateTimeFormatter是线程安全的，java 8+支持）
	
	```
	  private static DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
	
	  static class  ThreadPoolTest implements Runnable{
	
	    @Override
	    public void run() {
	      try {
	        String dateString = dateTimeFormatter.format(LocalDateTime.now());
	        TemporalAccessor temporalAccessor = dateTimeFormatter.parse(dateString);
	        String dateString2 = dateTimeFormatter.format(temporalAccessor);
	        System.out.println(Thread.currentThread().getName()+" 线程是否安全: "+dateString.equals(dateString2));
	      } catch (Exception e) {
	        e.printStackTrace();
	        System.out.println(Thread.currentThread().getName()+" 格式化失败 ");
	      }
	    }
	  }
	```
	
	
	
	5、使用FastDateFormat 替换SimpleDateFormat（FastDateFormat 是线程安全的，Apache Commons Lang包支持，java8之前推荐此用法）
	
	```
	 private static FastDateFormat fastDateFormat = FastDateFormat.getInstance("yyyy-MM-dd HH:mm:ss");
	
	  static class  ThreadPoolTest implements Runnable{
	
	    @Override
	    public void run() {
	      try {
	        String dateString = fastDateFormat.format(new Date());
	        Date parseDate =  fastDateFormat.parse(dateString);
	        String dateString2 = fastDateFormat.format(parseDate);
	        System.out.println(Thread.currentThread().getName()+" 线程是否安全: "+dateString.equals(dateString2));
	      } catch (Exception e) {
	        e.printStackTrace();
	        System.out.println(Thread.currentThread().getName()+" 格式化失败 ");
	      }
	    }
	  }
	```
	
	

> ©著作权归作者所有：来自51CTO博客作者华为云开发者社区的原创作品，如需转载，请注明出处，否则将追究法律责任
> SimpleDateFormat线程不安全了？这里有5种解决方案
> https://blog.51cto.com/u_15214399/4278391
