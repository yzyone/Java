
# 使用RestTemplate调用接口上传文件 #

## 场景 ##

接口接受一个文件，缓存在本地，验证文件的完整性及内容，然后将文件上传至云服务器；

下面只写利用RestTemplate将文件上传至云服务器，至于文件上传以及缓存在本地可以参考：JAVA文件上传：RESTFUL接口接收上传文件，缓存在本地

## 代码 ##

	@Test
	public void restTemplateTransferFile(){
	    final String filePath = "F:";
	    final String fileName = "testFile.txt";
	    final String url = "http://localhost:8080/file/upload";
	
	    RestTemplate restTemplate = new RestTemplate();
	
	    //设置请求头
	    HttpHeaders headers = new HttpHeaders();
	    MediaType type = MediaType.parseMediaType("multipart/form-data");
	    headers.setContentType(type);
	
	    //设置请求体，注意是LinkedMultiValueMap
	    FileSystemResource fileSystemResource = new FileSystemResource(filePath+"/"+fileName);
	    MultiValueMap<String, Object> form = new LinkedMultiValueMap<>();
	    form.add("file", fileSystemResource);
	    form.add("filename",fileName);
	
	　　//用HttpEntity封装整个请求报文
	    HttpEntity<MultiValueMap<String, Object>> files = new HttpEntity<>(form, headers);
	
	    String s = restTemplate.postForObject(url, files, String.class);
	    System.out.println(s);
	}


利用HttpHeader将请求头的设置为"multipart/form-data";

利用FileSystemResource封装文件内容，将请求体用LinkedMultiValueMap封装；

再用HttpEntity封装整个请求报文；

最后利用RestTemplate post请求。


## 注意 ##

这里我使用的是原生的RestTemplate（直接new的），比如使用OAuth2RestTemplate用上面这种方式传输文件，可能出现RestClientException,原因可能是OAuth2RestTemplate的HttpMessageConvert不能匹配MultiValueMap。 

参考：https://blog.csdn.net/kahhy/article/details/78115698

不需要在意别人的眼光, 坦诚做人. https://www.cnblogs.com/theRhyme/