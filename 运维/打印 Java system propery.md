```java
// root@oe3:~ # cat PrintProperties.java
import java.util.*;

public class PrintProperties {
  public static void main(String[] args) {
    Properties props = System.getProperties();
    for (Map.Entry<Object, Object> entry : props.entrySet()) {
      System.out.println(entry.getKey() + " = " + entry.getValue());
    }
  }
}
```

```bash
javac PrintProperties.java
java PrintProperties
```
