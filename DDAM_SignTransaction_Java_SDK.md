# 一、引入maven依赖

```java
<dependency>
      <groupId>net.java.dev.jna</groupId>
      <artifactId>jna</artifactId>
      <version>4.5.2</version>
</dependency>
```



# 二、创建一个interface来映射DLL中的函数，之后我们可以通过interface的实例来访问DLL中的函数

#### Linux/Unix/macOS动态库

调用.SO动态库：

```java
/**
 * 动态库接口
 */
public interface TestJava extends Library {

    // 动态库绝对路径
    TestJava INSTANCE = (TestJava) Native.loadLibrary("signtx", TestJava.class);

    // 动态库签名方法
    GoString.ByValue Signtx(GoString.ByValue sk, GoString.ByValue targetAddr, long value, long gasLimit, long gasPrice,
               int type, long nonce, GoString.ByValue dataHex);

}

```

# 三、新建一个GoString类来对应C中的GoString结构体，也就是Go程序中的string



```java

/**
 * go的String字符串
 */
public class GoString extends Structure {

    public String str;
    public long length;

    public GoString() {
    }

    public GoString(String str) {
        this.str = str;
        this.length = str.length();
    }

    @Override
    protected List<String> getFieldOrder() {
        List<String> fields = new ArrayList<>();
        fields.add("str");
        fields.add("length");
        return fields;
    }

    public static class ByValue extends GoString implements Structure.ByValue {
        public ByValue() {
        }

        public ByValue(String str) {
            super(str);
        }
    }

    public static class ByReference extends GoString implements Structure.ByReference {
        public ByReference() {
        }

        public ByReference(String str) {
            super(str);
        }
    }
}
```

# 四、调用DEMO

```java

//testdemo
public static void main(String[] args) {
        System.out.println(TestJava.INSTANCE.Signtx(new GoString.ByValue("0x3d4e00f3ca3477c3260a1a94af9ce70947522372d097937ccae830b09748ffa4"),
                new GoString.ByValue("0xa6c1106469d59abaca375504f232738ef6cc7750f1e330cb1708cb275e8965e1"),
                10L, 1000L, 10000L, 0, 0L, new GoString.ByValue("6i6D00MxoOEAbI7EliHgo8F/+bbMuYvDZpczv1u5moc=")).str);
    }
```

函数调用参数说明：

1、sk：new GoString.ByValue("0x3d4e00f3ca3477c3260a1a94af9ce70947522372d097937ccae830b09748ffa4")

​	=》表示私钥，java中string类型，转化成c中string类型的写法

2、targetaddr：new GoString.ByValue("0xa6c1106469d59abaca375504f232738ef6cc7750f1e330cb1708cb275e8965e1")

​	=》目标地址，java中string类型，转化成c中string类型的写法

3、value：10L

​	=》转账金额，java中的long类型，对应c中int64

4、gaslimit：1000L

​	=》gas限制，同上第3条

5、gasprice：1000L

​	=》gas价格，同上第3条

6、type：0

​	=》交易类型，int类型，和c相同

7、nonce：0L

​	=》交易nonce表示交易的序号，同上第3条

8、dataHex：new GoString.ByValue("6i6D00MxoOEAbI7EliHgo8F/+bbMuYvDZpczv1u5moc=")

​	=》交易中的备注信息（绑定、转绑、解绑的内容参照rpc文档），java中string类型，转化成c中string类型的写法
