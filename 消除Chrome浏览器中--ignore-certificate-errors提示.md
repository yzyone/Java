# 消除Chrome浏览器中--ignore-certificate-errors提示 #

```
import java.util.Arrays;
 
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.testng.annotations.AfterMethod;
import org.testng.annotations.BeforeMethod;
import org.testng.annotations.Test;
 
public class VisitSogouByChrome {
	WebDriver driver;
	String baseUrl;
 
	@BeforeMethod
	public void setUp() throws Exception {
		//设定连接Chrome浏览器驱动程序所在的磁盘位置，并添加为系统属性值
		baseUrl = "http://www.sogou.com";
		System.setProperty("webdriver.chrome.driver", "D:\\chromedriver.exe");
		DesiredCapabilities capabilities = DesiredCapabilities.chrome();
		capabilities.setCapability("chrome.switches", Arrays.asList("--incognito"));
		ChromeOptions options = new ChromeOptions();
		options.addArguments("--test-type");
		capabilities.setCapability("chrome.binary", "src/ucBrowserDrivers/chromedriver.exe");
		capabilities.setCapability(ChromeOptions.CAPABILITY, options);
		driver = new ChromeDriver(capabilities);
	}
	
	@Test
	public void visitSogou() {
		driver.get(baseUrl+"/");
	}
 
	@AfterMethod
	public void tearDown() throws Exception {
		driver.quit();
	}
 
}
```

去掉此提示浮动框的代码如下：

```
System.setProperty("webdriver.chrome.driver", "D:\\chromedriver.exe");
        DesiredCapabilities capabilities = DesiredCapabilities.chrome();
        capabilities.setCapability("chrome.switches", Arrays.asList("--incognito"));
        ChromeOptions options = new ChromeOptions();
        options.addArguments("--test-type");
        capabilities.setCapability("chrome.binary", "src/ucBrowserDrivers/chromedriver.exe");
        capabilities.setCapability(ChromeOptions.CAPABILITY, options);
        driver = new ChromeDriver(capabilities);
```

————————————————

版权声明：本文为CSDN博主「wsfw014」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。

原文链接：https://blog.csdn.net/wsfw014/article/details/51348368