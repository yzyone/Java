# Java NIO ByteBuffer读取文件 #

---

## FileChannel 和 ByteBuffer ##

从JDK1.4以后就提供java.nio的包，nio主要提供字节与字符的映射、内存映射文件和文件加锁机制

其中内存映射文件在读取大文件时可能会用上，因为内存映射不是直接把文件加载到JVM内存空间

而是借用操作系统对文件的读取，这经历了由当前Java态进入到操作系统内核态，再由操作系统读取文件，

并返回数据到当前Java态的过程。由Java态进入操作系统内核态离不开nio包中两个重要的类

FileChannel 和 ByteBuffer。FileChannel表示文件通道，可以从FileInputStream、FileOutputStream

以及RandomAccessFile对象获取文件通道，你可以从文件通道直接读取文件，也可以使用“内存映射”

即使用通道，将文件内存映射到ByteBuffer，可以映射一部分内容，也可以映射全部内容，使用内存映射

能大幅提高我们操作大文件的速度


## FileChannel 和 ByteBuffer文件读取 ##


```
package nio;
 
import java.io.BufferedInputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.RandomAccessFile;
import java.nio.ByteBuffer;
import java.nio.MappedByteBuffer;
import java.nio.channels.FileChannel;
import java.nio.channels.FileChannel.MapMode;
 
/**
 * 
 * Channel类似与流,数据可以从Channel读取到Buffer,也可以从Buffer写入到Channel
 * 但通道和流还是有区别,比如流只能是单向读或写,而通道可以异步读写
 * 
 * @author yli
 */
public class FileChannelTest {
	
	// 110M
	private static String file = "/mnt/hgfs/pdf/Java2核心技术II卷.高级特性.pdf";
 
	public static void main(String[] args) throws IOException {
		
		// 普通 NIO 读取
		// 每次读取1024个字节
		// readByChannelTest(1024);	// 28151毫秒
 
		// 普通 NIO 读取
		// 每次读取1个字节,每次读取1个字节太慢了
		// readByChannelTest(1);
		
		// 使用内存映射文件来读取
		// 从FileChannel拿到MappedByteBuffer,读取文件内容
		readByChannelTest3(1024);	// 61毫秒，甚至不到100毫秒
		
		// 对于一个只有110M的文件，验证使用FileChannel映射得到MappedByteBuffer
		// 就能大幅提交文件读取速度
		
		// 普通的缓冲流读取
		// readByBufferdStream();	// 3922毫秒
	}
 
	/**
	 * 使用FileChannel读取文件,并打印在控制台
	 * 
	 * @param 每次读取多少个字节
	 * @throws IOException
	 */
	public static void readByChannelTest(int allocate) throws IOException {
		long start = System.currentTimeMillis();
		FileInputStream fis = new FileInputStream(file);
 
		// 1.从FileInputStream对象获取文件通道FileChannel
		FileChannel channel = fis.getChannel();
		long size = channel.size();
 
		// 2.从通道读取文件内容
		byte[] bytes = new byte[1024];
		ByteBuffer byteBuffer = ByteBuffer.allocate(allocate);
 
		// channel.read(ByteBuffer) 方法就类似于 inputstream.read(byte)
		// 每次read都将读取 allocate 个字节到ByteBuffer
		int len;
		while ((len = channel.read(byteBuffer)) != -1) {
			// 注意先调用flip方法反转Buffer,再从Buffer读取数据
			byteBuffer.flip();
			
			// 有几种方式可以操作ByteBuffer
			// 1.可以将当前Buffer包含的字节数组全部读取出来
			//bytes = byteBuffer.array();
			// System.out.print(new String(bytes));
			
			// 2.类似与InputStrean的read(byte[],offset,len)方法读取 
			byteBuffer.get(bytes, 0, len);
			// System.out.print(new String(bytes, 0 ,len));
 
			// 3.也可以遍历Buffer读取每个字节数据
			// 一个字节一个字节打印在控制台,但这种更慢且耗时
			// while(byteBuffer.hasRemaining()) {
			// System.out.print((char)byteBuffer.get());
			// }
 
			// 最后注意调用clear方法,将Buffer的位置回归到0
			byteBuffer.clear();
 
		}
 
		// 关闭通道和文件流
		channel.close();
		fis.close();
 
		long end = System.currentTimeMillis();
		System.out.println(String.format("\n===>文件大小：%s 字节", size));
		System.out.println(String.format("===>读取并打印文件耗时：%s毫秒", end - start));
	}
 
	/**
	 * 仍然是根据FileChannel操作ByteBuffer,从ByteBuffer读取内容
	 * 通道读取文件，速度比内存映射慢很多，甚至比普通缓冲流要慢
	 * 
	 * @param allocate
	 * @throws IOException
	 */
	public static void readByChannelTest2(int allocate) throws IOException {
		long start = System.currentTimeMillis();
		FileInputStream fis = new FileInputStream(file);
 
		// 1.从FileInputStream对象获取文件通道FileChannel
		FileChannel channel = fis.getChannel();
		long size = channel.size();
 
		// 每次读取allocate个字节,计算要循环读取多少次
		long cycle = size / allocate;
		// 看是否能整数倍读完
		int mode = (int) (size % allocate);
 
		// 循环读取
		byte[] bytes;
		ByteBuffer byteBuffer = ByteBuffer.allocate(allocate);
		for (long i = 0; i < cycle; i++) {
			if (channel.read(byteBuffer) != -1) {
				byteBuffer.flip();
				bytes = byteBuffer.array();
				// System.out.print(new String(bytes));
				byteBuffer.clear();
			}
		}
 
		// 读取最后mode个字节
		if (mode > 0) {
			byteBuffer = ByteBuffer.allocate(mode);
			if (channel.read(byteBuffer) != -1) {
				byteBuffer.flip();
				bytes = byteBuffer.array();
				// System.out.print(new String(bytes));
				byteBuffer.clear();
			}
		}
 
		// 关闭通道和文件流
		channel.close();
		fis.close();
 
		long end = System.currentTimeMillis();
		System.out.println(String.format("\n===>文件大小：%s 字节", size));
		System.out.println(String.format("===>读取并打印文件耗时：%s毫秒", end - start));
	}
 
	/**
	 * 通过 FileChannel.map()拿到MappedByteBuffer
	 * 使用内存文件映射，速度会快很多
	 * 
	 * @throws IOException
	 */
	public static void readByChannelTest3(int allocate) throws IOException {
		long start = System.currentTimeMillis();
		
		RandomAccessFile fis = new RandomAccessFile(new File(file), "rw");
		FileChannel channel = fis.getChannel();
		long size = channel.size();
		
		// 构建一个只读的MappedByteBuffer
		MappedByteBuffer mappedByteBuffer = channel.map(MapMode.READ_ONLY, 0, size);
		
		// 如果文件不大,可以选择一次性读取到数组
		// byte[] all = new byte[(int)size];
		// mappedByteBuffer.get(all, 0, (int)size);
		// 打印文件内容
		// System.out.println(new String(all));
		
		// 如果文件内容很大,可以循环读取,计算应该读取多少次
		byte[] bytes = new byte[allocate];
		long cycle = size / allocate;
		int mode = (int)(size % allocate);
		//byte[] eachBytes = new byte[allocate];
		for (int i = 0; i < cycle; i++) {
			// 每次读取allocate个字节
			mappedByteBuffer.get(bytes);
			
			// 打印文件内容,关闭打印速度会很快
			// System.out.print(new String(eachBytes));
		}
		if(mode > 0) {
			bytes = new byte[mode];
			mappedByteBuffer.get(bytes);
			
			// 打印文件内容,关闭打印速度会很快
			// System.out.print(new String(eachBytes));
		}
 
		// 关闭通道和文件流
		channel.close();
		fis.close();
 
		long end = System.currentTimeMillis();
		System.out.println(String.format("\n===>文件大小：%s 字节", size));
		System.out.println(String.format("===>读取并打印文件耗时：%s毫秒", end - start));
	}
	
	
	/**
	 * 普通Java IO 缓冲流读取
	 * @throws IOException
	 */
	public static void readByBufferdStream() throws IOException {
		long start = System.currentTimeMillis();
		BufferedInputStream bis = new BufferedInputStream(new FileInputStream(file));
		long size = bis.available();
		
		int len = 0;
		int allocate = 1024;
		byte[] eachBytes = new byte[allocate];
		while((len = bis.read(eachBytes)) != -1) {
			// System.out.print(new String(eachBytes, 0, len));
		}
		
		bis.close();
		
		long end = System.currentTimeMillis();
		System.out.println(String.format("\n===>文件大小：%s 字节", size));
		System.out.println(String.format("===>读取并打印文件耗时：%s毫秒", end - start));
	}
	
 
}
```


---

版权声明：本文为CSDN博主「u012888365」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。

原文链接：https://blog.csdn.net/gzu_imis/article/details/21109753