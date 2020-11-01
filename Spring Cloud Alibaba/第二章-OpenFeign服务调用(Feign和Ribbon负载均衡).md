## Spring Cloud 整合OpenFeign

> 第一章已讲到 ：服务已经注册到Nacos内，多个服务之间如何调用呢？ OpenFeign

1. ==服务调用方==导入OpenFeign依赖包

	```xml
	<dependency>
	    <groupId>org.springframework.cloud</groupId>
	    <artifactId>spring-cloud-starter-openfeign</artifactId>
	</dependency>
	```

2. ==服务提供方==加入代码

	```java
	@Autowired
	private CouponService couponService;
	
	@RequestMapping("/member/list")
	public R memberCoupons(){
	    List<Map<String, Object>> mapList=couponService.listMaps();
	    List<CouponEntity> lists=new ArrayList<>();
	    mapList.forEach(x -> {
	        CouponEntity entity=new CouponEntity();
	        entity.setId(Long.parseLong(x.get("id").toString()));
	        entity.setCouponType(Integer.parseInt(x.get("couponType").toString()));
	        entity.setCouponName(x.get("couponName").toString());
	        lists.add(entity);
	    });
	    return R.ok().put("coupons", lists);
	}
	```

3. ==服务调用方==调用==服务提供方==中的方法

	* ==服务调用方==提供接口 接口中调用远程服务 「通过`服务名`和`controller请求URL`」

		```java
		package com.shuai.gulimall.gulimallmember;
		
		import org.springframework.boot.SpringApplication;
		import org.springframework.boot.autoconfigure.SpringBootApplication;
		import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
		import org.springframework.cloud.openfeign.EnableFeignClients;
		import org.springframework.cloud.openfeign.FeignClient;
		
		@EnableFeignClients(basePackages = "com.shuai.gulimall.gulimallmember.openFeign")    // 开启远程调用  服务启动时扫描此包  从而获得远程服务方法  然后在本模块中使用
		@EnableDiscoveryClient //服务注册功能开启
		@SpringBootApplication
		public class GulimallMemberApplication {
		
		    public static void main(String[] args) {
		        SpringApplication.run(GulimallMemberApplication.class, args);
		    }
		
		}
		```

4. ==服务调用方==调用接口中远程服务提供的方法

  ```java
  @Autowired
  private CouponFeignService couponFeignService;
  
  @RequestMapping("/coupons")
  public R test(){
      MemberEntity memberEntity=new MemberEntity();
      memberEntity.setNickname("会员张三");
  
      R membercoupons=couponFeignService.memberCoupons();
      return R.ok().put("member", memberEntity).put("coupons", membercoupons.get("coupons"));
  }
  ```

  ```java
  import com.shuai.common.utils.R;
  import org.springframework.cloud.openfeign.FeignClient;
  import org.springframework.stereotype.Component;
  import org.springframework.web.bind.annotation.RequestMapping;
  
  // todo 1. 告知 服务提供方 暴露的服务名称
  @FeignClient("service-provider-coupon")
  @Component
  public interface CouponFeignService {
      // todo 2. 拷贝 服务提供方 的方法完整签名
      //@RequestMapping("/member/list")
      // todo 3. 将方法请求路径补全
      @RequestMapping("gulimallcoupon/coupon/member/list")
      public R memberCoupons();
  
  
  
      // todo 4. 想要调用其他的方法，循环 【2 3】 步骤即可
  }
  ```

5. 测试

  ![image-20200916215000677](第二章-OpenFeign服务调用(Feign和Ribbon负载均衡).assets/image-20200916215000677.png)

