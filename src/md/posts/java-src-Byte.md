<!--
author: wngn123
head: head.png
date: 2016-08-29
title: Java源码 Byte
tags: java
category: Java
status: publish
summary: 阅读Java的源码，Byte源码
-->

## Byte继承体系 ##
Byte的签名如下，继承了Number类并实现Comparable接口
```java
public final class Byte extends Number implements Comparable<Byte>
```
Comparable接口强行对实现它的每个类的对象进行整体排序。此排序被称为该类的自然排序，类的 compareTo 方法被称为它的自然比较方法。实现此接口的对象列表（和数组）可以通过Collections.sort（和Arrays.sort）进行自动排序。实现此接口的对象可以用作有序映射表中的键或有序集合中的元素，无需指定比较器。强烈推荐（虽然不是必需的）使自然排序与 equals 一致。所谓与equals一致是指对于类C的每一个e1和e2来说，当且仅当 (e1.compareTo((Object)e2) == 0) 与e1.equals((Object)e2) 具有相同的布尔值时，类C的自然排序才叫做与equals一致 
```java
public interface Comparable<T> {
    public int compareTo(T o);
}
```
Number为抽象类，提供了实数基本类型的互相转换方法：int,long,short,byte,float,double
Number的基本实现类有java.lang.Byte，java.lang.Double，java.lang.Float，java.lang.Integer，java.lang.Long，java.lang.Short等
```java
public abstract class Number implements java.io.Serializable {
    public abstract int intValue();
    public abstract long longValue();
    public abstract float floatValue();
    public abstract double doubleValue();
    public byte byteValue() {
        return (byte)intValue();
    }
    public short shortValue() {
        return (short)intValue();
    }
    private static final long serialVersionUID = -8742448824652078965L;
}

```
## Byte基本概念 ##
Byte类包装了byte基本类型,所有Byte的实际值是存储在一个byte类型的属性value上的，byte存储占用1字节8位内存，第一位是符号位，所以byte的最大值是2的7次幂减1，最小值是负2的7次幂，因此byte的最大值是：0111 1111 = 0x7f = 127,byte的最小值为负值，负数的表示为正值二进制的补码：
```
负数：正值的补码
补码：反码加一
源码：绝对值二进制
反码：二进制按位取反
```
负数最小值的正值为2的7次幂，正值的二进制为：1000 0000，二进制取反之后为：0111 1111,则正值的补码为：1000 0000，即byte的最小值为：0x80
## Byte构造函数 ##
Byte有2个构造函数，可以传递一个byte数字或者一个数字字符串，数字字符串会被转换成十进制的byte数字。

```java
    private final byte value;
    public static final byte MIN_VALUE = -128;
    public static final byte MAX_VALUE = 127;
    public Byte(byte value) {
        this.value = value;
    }
    public Byte(String s) throws NumberFormatException {
        this.value = parseByte(s, 10);
    }

```
## Byte的实例方法 ##
Byte实现了从Number类和Comparable接口以及Object继承的方法。Byte提供了可以直接转换成Short,Integer,Long,Float,Double等数值类型。
```java
	public byte byteValue() {
        return value;
    }
    public short shortValue() {
        return (short)value;
    }
    public int intValue() {
        return (int)value;
    }
    public long longValue() {
        return (long)value;
    }
    public float floatValue() {
        return (float)value;
    }
    public double doubleValue() {
        return (double)value;
    }
    public String toString() {
        return Integer.toString((int)value);
    }
    public int hashCode() {
        return (int)value;
    }
    public boolean equals(Object obj) {
        if (obj instanceof Byte) {
            return value == ((Byte)obj).byteValue();
        }
        return false;
    }
    public int compareTo(Byte anotherByte) {
        return compare(this.value, anotherByte.value);
    }
    public static int compare(byte x, byte y) {
        return x - y;
    }
```
## ByteCache ##
Byte类内部有一个私有的静态内部类ByteCache，该类缓存了全部的的byte值到缓存中，从而保证这些byte可以直接获取而不用构造。
```java
	private static class ByteCache {
        private ByteCache(){}
        static final Byte cache[] = new Byte[-(-128) + 127 + 1];
        static {
            for(int i = 0; i < cache.length; i++)
                cache[i] = new Byte((byte)(i - 128));
        }
    }
```
## byte转换成Byte ##
byte转换成Byte使用valueOf(byte b)方法，该方法会从ByteCache中获取该值得内存缓存，而不会创建新的Byte对象。如果使用构造函数则会创建新的对象。
```java
	public static Byte valueOf(byte b) {
        final int offset = 128;
        return ByteCache.cache[(int)b + offset];
    }
```
## String转换成Byte ##
String字符串转换成byte使用parseByte(String s, int radix)方法，第一个参数是要转换成byte类型的字符串，第二个参数是该字符串的进制数。
```
System.out.println(Byte.parseByte("11",10)); //11,10进制数11,转换成byte是11
System.out.println(Byte.parseByte("11",16)); //17,16进制数11,转换成byte是17
System.out.println(Byte.parseByte("11",8)); //9,8进制数11,转换成byte是9
System.out.println(Byte.parseByte("11",2)); //3,2进制数11,转换成byte是3
```
parseByte方法中会调用Integer中的parseInteger方法处理，如果结果在Byte的取值范围之内，这直接转换成byte，否则抛出异常。

parseByte(String s)方法默认10进制解析成byte

valueOf(String s, int radix)方法首先调用parseByte(String s, int radix)方法将字符串转换成byte类型数值，然后通过valueOf(byte b)方法将byte转换成Byte类型的对象。

valueOf(String s)方法默认10进制解析成Byte

```java
	public static Byte valueOf(String s) throws NumberFormatException {
        return valueOf(s, 10);
    }
	public static Byte valueOf(String s, int radix)
        throws NumberFormatException {
        return valueOf(parseByte(s, radix));
    }
 	public static byte parseByte(String s) throws NumberFormatException {
        return parseByte(s, 10);
    }
	public static byte parseByte(String s, int radix)
        throws NumberFormatException {
        int i = Integer.parseInt(s, radix);
        if (i < MIN_VALUE || i > MAX_VALUE)
            throw new NumberFormatException(
                "Value out of range. Value:\"" + s + "\" Radix:" + radix);
        return (byte)i;
    }

```