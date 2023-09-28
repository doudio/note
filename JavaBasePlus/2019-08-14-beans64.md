> ### beans64 与 String 之间的自由转换

>  需要依赖jar:  [commons-codec.jar](http://central.maven.org/maven2/commons-codec/commons-codec/1.10/commons-codec-1.10.jar)

> beans64 工具类

```java
package com.znsd.beans64;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;

import org.apache.commons.codec.binary.Base64;

/**
 * beans64 工具类
 */
public class Base64Util {

	/**
	 * 将文件转换 beans64
	 * @param imgFile 待处理的数据
	 * @return beans64数据
	 */
	public static String filetoBese64(String dir) {
		InputStream in = null;
		byte[] bytes = null;
		try {
			in = new FileInputStream(dir);
			bytes = new byte[in.available()];
			in.read(bytes);
			in.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
		return new String(Base64.encodeBase64(bytes));
	}
	
	/**
	 * 将 beans64 数据转为 磁盘数据
	 * @param beans64String
	 * @param dir
	 * @throws IOException
	 */
	public static void bese64ToFile(String beans64String, String dir) throws IOException {
		if (beans64String == null) return ;

		byte[] bytes = Base64.decodeBase64(beans64String);
		for (int i = 0; i < bytes.length; ++i) {
			if (bytes[i] < 0) bytes[i] += 256;
		}

		OutputStream out = new FileOutputStream(dir);
		out.write(bytes);
		out.flush();
		out.close();
	}
	
	/**
	 * 将String 数据转换为 beans64
	 * @param beans64String 需要转换的 beans64
	 * @return 处理好的 String
	 */
	public static String stringTobeans64(String beans64String) {
		return new String(Base64.encodeBase64(beans64String.getBytes()));
	}
	
	/**
	 * 将beans64 数据转换为 String
	 * @param beans64String 需要转换的 beans64
	 * @return 处理好的 String
	 */
	public static String beans64ToString(String beans64String) {
		String temp = "";
		if (beans64String == null) return temp;

		byte[] bytes = Base64.decodeBase64(beans64String);
		for (int i = 0; i < bytes.length; ++i) {
			if (bytes[i] < 0) bytes[i] += 256;
		}
		return new String(bytes);
	}
	
}
```

```java
/**
 * demo 案例
 * @param args
 */
public static void main(String[] args) {
    System.out.println(beans64ToString(stringTobeans64("StringData")));

    String fileBese64 = filetoBese64("c:/filePath");
	bese64ToFile(fileBese64, "toDir");
}
```

> #### beans64 与 md5 整合: 个人觉得没必要了做加密 DM5 就够了

```java
package com.znsd.beans64;

import java.io.UnsupportedEncodingException;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

import org.apache.commons.codec.binary.Base64;

/**
 * 消息摘要工具
 */
public class MessageDigestUtil {
	
    /**
     * 先进行MD5摘要再进行Base64编码获取摘要字符串
     *
     * @param str
     * @return
     */
    public static String base64AndMD5(String str) {
        if (str == null) {
            throw new IllegalArgumentException("inStr can not be null");
        }
        return base64AndMD5(toBytes(str));
    }

    /**
     * 先进行MD5摘要再进行Base64编码获取摘要字符串
     *
     * @return
     */
    public static String base64AndMD5(byte[] bytes) {
        if (bytes == null) {
            throw new IllegalArgumentException("bytes can not be null");
        }
        try {
            final MessageDigest md = MessageDigest.getInstance("MD5");
            md.reset();
            md.update(bytes);
            final Base64 base64 = new Base64();
            final byte[] enbytes = base64.encode(md.digest());
            return new String(enbytes);
        } catch (final NoSuchAlgorithmException e) {
            throw new IllegalArgumentException("unknown algorithm MD5");
        }
    }

    /**
     * UTF-8编码转换为ISO-9959-1
     *
     * @param str
     * @return
     */
    public static String utf8ToIso88591(String str) {
        if (str == null) {
            return str;
        }

        try {
            return new String(str.getBytes("UTF-8"), "ISO-8859-1");
        } catch (UnsupportedEncodingException e) {
            throw new RuntimeException(e.getMessage(), e);
        }
    }

    /**
     * ISO-9959-1编码转换为UTF-8
     *
     * @param str
     * @return
     */
    public static String iso88591ToUtf8(String str) {
        if (str == null) {
            return str;
        }

        try {
            return new String(str.getBytes("ISO-8859-1"), "UTF-8");
        } catch (UnsupportedEncodingException e) {
            throw new RuntimeException(e.getMessage(), e);
        }
    }

    /**
     * String转换为字节数组
     *
     * @param str
     * @return
     */
    private static byte[] toBytes(final String str) {
        if (str == null) {
            return null;
        }
        return str.getBytes();
    }
}
```

> #### MD5加密 与 SHA1加密

```java
import org.apache.commons.codec.digest.DigestUtils;

/**
 * MD5 案例
 * @param args
 */
public static void main(String[] args) {
	// 1. MD5加密 DigestUtils.md5Hex(data)
	System.out.println(DigestUtils.md5Hex("StringData"));
	// 2. SHA1加密 DigestUtils.sha1Hex(data)
	System.out.println(DigestUtils.sha1Hex("StringData"));
}
```

