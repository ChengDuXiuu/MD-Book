`引言:`

​	Spring IOC 实现 依赖于 ==注解==和==反射==。下面简单说下实现原理，以后有时间代码实现



## IOC出现原因

* 有状态的对象

	有状态的对象即兑现不光要进行功能处理还需要保存数据。例如User 即是有状态的对象。因为每个用户对应的User中数据都不一样。

* 无状态的对象

	即只做功能处理不需要保存数据。常见的有 Service、DAO、接口等。它们大多给定参数然后返回不同的数据。偏

​	之前讲Ioc的时候也讲过，主要作用是==松耦合==和==管理Bean==。今天我们换个角度理解的更加透彻。

在没有出现Spring之前，我们的项目架构主要为三层架构，当然有了Spring任然是三层架构。在三层架构中，我们实现一个curd 都需要在 mapper  ->  dao  ->  service  ->  controller中添加。几乎每一个pojo都对应这么一套代码，==而且mapper、dao、service 大多基础CRUD都是无状态对象==，但是我们在Spring之前在使用这些无状态对象是都需要new一个对象。当然我们可以设计成单利模式对象，但是我们需要为每一个无状态对象手动设置单例。很是麻烦，IOC出现根本原因就是为了 给无状态对象进行单例。当然这只是IOC的一部分。我认为是最重要的一部分。



## 思路

1. 声明注解

	```java
	@Target(ElementType.TYPE)
	@Retention(RetentionPolicy.RUNTIME)
	public @interface Bean {
	}
	@Target(ElementType.FIELD)
	@Retention(RetentionPolicy.RUNTIME)
	public @interface Autowired {
	}
	```

2. 使用注解

	```java
	public interface StudentService {
	    public void study();
	}
	@Bean //自定义Bean
	public class StudentServcieImpl implements StudentService {
	    @Override
	    public void study() {
	        System.out.println("我要上清华~~");
	    }
	}
	```

3. 定义ioc容器 : 存放接口和实例

	```java
	public class ApplicationContext {
	    public static ConcurrentHashMap<Class, Object> context = new ConcurrentHashMap<>();
	
	}
	```

	

4. 包扫描

	* 将当前项目的target/class下class文件扫描
	* 筛选出拥有 目标注解@Bean的 字节码文件，
	* 根据文件路径 得到 字节码全限定类名并使用 反射 构造实例
	* 字节码文件接口 放入到 IOC容器 map-key，字节码接口对应的实现类实例 放入到 IOC容器 map-value
	* 目标注解@Autowired的字段 进行赋值

	```java
	public class ScannerClass {
	    private String filePath;
	
	    //加载全部的类的实例
	    public  void initContextByAnnotation() {
	        filePath = ScannerClass.class.getClassLoader().getResource("").getFile();
	        //加载service层
	        loadOneFromService(new File(filePath));
	        //加载controller层
	        loadOneFromController(new File(filePath));
	    }
	
	    private void loadOneFromService(File fileParent) {
	        if (fileParent.isDirectory()) {
	            File[] childrenFiles = fileParent.listFiles();
	            if (childrenFiles == null || childrenFiles.length == 0) {
	                return;
	            }
	            for (File child : childrenFiles) {
	                if (child.isDirectory()) {
	                    //如果是个文件夹就继续调用该方法,使用了递归
	                    loadOneFromService(child);
	                } else {
	                    //文件路径转变成全类名
	                    String pathWithClass = child.getAbsolutePath().substring(filePath.length() - 1);
	                    //过滤其他文件，只保留class文件
	                    if (pathWithClass.contains(".class")) {
	                        //win 去掉.class后缀，并且把 \ 替换成 .
	                        //String fullName = pathWithClass.replaceAll("\\\\", ".").replace(".class", "");
	                        //mac 去掉.class后缀，并且把 \ 替换成 .
	                        String fullName = pathWithClass.replaceAll("/", ".").replace(".class", "").replaceFirst("\\.", "");
	                        try {
	                            Class<?> aClass = Class.forName(fullName);
	
	                            //过滤接口
	                            if (!aClass.isInterface()) {
	                                //筛选拥有 目标注解 的 实现类
	                                Bean annotation = aClass.getAnnotation(Bean.class);
	                                if (annotation != null) {
	                                    Object instance = aClass.newInstance();
	                                    if (aClass.getInterfaces().length > 0 ) {
	                                        //如果有接口把接口的class当成key，实例对象当成value
	                                        System.out.println("正在加载【" + aClass.getInterfaces()[0] + "】,实例对象是：" + instance.getClass().getName());
	                                        ApplicationContext.context.put(aClass.getInterfaces()[0], instance);
	                                    } else {
	                                        //如果有接口把自己的class当成key，实例对象当成value
	                                        System.out.println("正在加载【" + aClass.getName() + "】,实例对象是：" + instance.getClass().getName());
	                                        ApplicationContext.context.put(aClass, instance);
	                                    }
	                                }
	                            }
	                        } catch (ClassNotFoundException | IllegalAccessException | InstantiationException e) {
	                            e.printStackTrace();
	                        }
	                    }
	                }
	            }
	        }
	    }
	    private void loadOneFromController(File fileParent){
	        if (fileParent.isDirectory()) {
	            File[] childrenFiles = fileParent.listFiles();
	            if (childrenFiles == null || childrenFiles.length == 0) {
	                return;
	            }
	            for (File child : childrenFiles) {
	                if (child.isDirectory()) {
	                    //如果是个文件夹就继续调用该方法,使用了递归
	                    loadOneFromController(child);
	                } else {
	                    //文件路径转变成全类名
	                    String pathWithClass = child.getAbsolutePath().substring(filePath.length() - 1);
	                    //过滤其他文件，只保留class文件
	                    if (pathWithClass.contains(".class")) {
	                        //win 去掉.class后缀，并且把 \ 替换成 .
	                        //String fullName = pathWithClass.replaceAll("\\\\", ".").replace(".class", "");
	                        //mac 去掉.class后缀，并且把 \ 替换成 .
	                        String fullName = pathWithClass.replaceAll("/", ".").replace(".class", "").replaceFirst("\\.", "");
	                        try {
	                            Class<?> aClass = Class.forName(fullName);
	
	                            //过滤接口
	                            if (!aClass.isInterface()) {
	                                //筛选拥有 目标注解 的类成员属性进行赋值
	                                Field[] fields = aClass.getDeclaredFields();
	                                Arrays.stream(fields).forEach(item ->{
	                                    Autowired autowired = item.getDeclaredAnnotation(Autowired.class);
	                                    if (null != autowired){
	                                        item.setAccessible(true);
	                                        Object o = null;
	                                        try {
	                                            o = item.getDeclaringClass().newInstance();
	                                            item.set(o,ApplicationContext.context.get(item.getType()));
	                                        } catch (InstantiationException | IllegalAccessException e) {
	                                            e.printStackTrace();
	                                        }
	                                    }
	                                });
	                            }
	                        } catch (ClassNotFoundException e) {
	                            e.printStackTrace();
	                        }
	                    }
	                }
	            }
	        }
	    }
	}
	```

5. 使用

	```java
	public class IocTest {
	    @Autowired
	    private static StudentService studentService;
	    public static void main(String[] args) throws ClassNotFoundException, IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException, NoSuchFieldException {
	        //todo 1.包扫描 实例
	        new ScannerClass().initContextByAnnotation();
	        //todo 2.使用
	        studentService.study();
	
	    }
	}
	```

	

	