## 工具类

### spring.core.serializer
序列化器。`SerializationDelegate` 提供了序列化和反序列化的功能。

### spring.core.style
美化输出对象。以下代码输出“[@15c21c49 array<Integer>[1, 2, 3, 4, 5, 6, 7, 8, 9]]”。

```java
import org.springframework.core.style.ToStringCreator;

public class Main {
	public static void main(String[] args) {
		int[] a = { 1 , 2 , 3 , 4 , 5 , 6 , 7 , 8 , 9 };
		System.out.println( new  ToStringCreator(a).toString());
	}
}
```