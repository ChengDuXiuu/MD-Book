## 操作方式

1. 9300端口 ：TCP长连接 

	* spring-data-elasticsearch:transport-api.jar

		> springboot版本不同，transport-api.jar 包也不同，不能全适配ES
		>
		> ES 7.X已经不建议使用，ES 8以后要废弃 。和type同命运

2. 9200端口 ：HTTP
	* JestClient ：非官网、更新慢
	* RestTemplate ：模拟发送HTTP请求，但是ES很多操作都需要自己封装，麻烦，跟别说 DSL了。
	* HttpClient : 同上
	* ElasticSearch-Rest-Client ： 官网提供的RestClient ，封装了ES操作，API层次分明、版本完全适配