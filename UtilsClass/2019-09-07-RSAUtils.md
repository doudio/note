> ## RSAUtils

* java RSA 加密时如果明文过长就会导致加密报错
  * 需要进行分段加密不建议去修改 JRE
* 如果想反向加解密可通过切换
  * `PKCS8EncodedKeySpec` 私钥加密
  * `X509EncodedKeySpec` 公钥加密
* 用私钥对信息生成数字签名

> RSAUtils

```java
import javax.crypto.Cipher;
import java.io.ByteArrayOutputStream;
import java.security.*;
import java.security.interfaces.RSAPrivateKey;
import java.security.interfaces.RSAPublicKey;
import java.security.spec.PKCS8EncodedKeySpec;
import java.security.spec.X509EncodedKeySpec;
import java.util.Base64;
import java.util.HashMap;
import java.util.Map;

/**
 * RSA 分段加密与反向加密
 */
public class RSAUtils {

    /**
     * 加密算法RSA
     */
    public static final String KEY_ALGORITHM = "RSA";

    /**
     * 签名算法
     * java.security.Signature#signatureInfo
     * 
     * signatureInfo.put("sun.security.rsa.RSASignature$MD2withRSA", TRUE);
     * signatureInfo.put("sun.security.rsa.RSASignature$MD5withRSA", TRUE);
     * signatureInfo.put("sun.security.rsa.RSASignature$SHA1withRSA", TRUE);
     * signatureInfo.put("sun.security.rsa.RSASignature$SHA256withRSA", TRUE);
     * signatureInfo.put("sun.security.rsa.RSASignature$SHA384withRSA", TRUE);
     * signatureInfo.put("sun.security.rsa.RSASignature$SHA512withRSA", TRUE);
     */
    public static final String SIGNATURE_ALGORITHM = "MD5withRSA";

    /**
     * 获取公钥的key
     */
    private static final String PUBLIC_KEY = "RSA_PUBLIC_KEY";

    /**
     * 获取私钥的key
     */
    private static final String PRIVATE_KEY = "RSA_PRIVATE_KEY";

    /**
     * RSA KEY_SIZE 支持的最大加密大小
     */
    private static final int MAX_ENCRYPT_BLOCK = 117;

    /**
     * RSA KEY_SIZE 支持的最大解密大小
     */
    private static final int MAX_DECRYPT_BLOCK = 128;

    /**
     * KEY_SIZE/MAX_ENCRYPT_BLOCK/MAX_DECRYPT_BLOCK
     * 1024/117/128
     * 2048/245/256
     * MAX_DECRYPT_BLOCK = MAX_ENCRYPT_BLOCK + 11
     *
     * RSA 公钥私钥大小
     *  RSAUtils#MAX_ENCRYPT_BLOCK
     *  RSAUtils#MAX_DECRYPT_BLOCK
     * 这三个值是对应的
     */
    private static final int KEY_SIZE = 1024;

    /**
     * 生成密钥对(公钥和私钥)
     * @return
     */
    public static Map<String, Object> genKeyPair() throws NoSuchAlgorithmException {
        KeyPairGenerator keyPairGen = KeyPairGenerator.getInstance(KEY_ALGORITHM);
        keyPairGen.initialize(KEY_SIZE);
        KeyPair keyPair = keyPairGen.generateKeyPair();
        RSAPublicKey publicKey = (RSAPublicKey) keyPair.getPublic();
        RSAPrivateKey privateKey = (RSAPrivateKey) keyPair.getPrivate();
        Map<String, Object> keyMap = new HashMap<>(2);
        keyMap.put(PUBLIC_KEY, publicKey);
        keyMap.put(PRIVATE_KEY, privateKey);
        return keyMap;
    }

    /**
     * 获取私钥
     * @param keyMap 密钥对
     */
    public static String getPrivateKey(Map<String, Object> keyMap) {
        Key key = (Key) keyMap.get(PRIVATE_KEY);
        return base64Encode(key.getEncoded());
    }

    /**
     * 获取公钥
     * @param keyMap 密钥对
     */
    public static String getPublicKey(Map<String, Object> keyMap) {
        Key key = (Key) keyMap.get(PUBLIC_KEY);
        return base64Encode(key.getEncoded());
    }

    /**
     * 用私钥对信息生成数字签名
     *
     * @param data       已加密数据
     * @param privateKey 私钥(BASE64编码)
     */
    public static String sign(byte[] data, String privateKey) throws Exception {
        byte[] keyBytes = base64Decode(privateKey);
        PKCS8EncodedKeySpec pkcs8KeySpec = new PKCS8EncodedKeySpec(keyBytes);
        KeyFactory keyFactory = KeyFactory.getInstance(KEY_ALGORITHM);
        PrivateKey privateK = keyFactory.generatePrivate(pkcs8KeySpec);
        Signature signature = Signature.getInstance(SIGNATURE_ALGORITHM);
        signature.initSign(privateK);
        signature.update(data);
        return base64Encode(signature.sign());
    }

    /**
     * 校验数字签名
     *
     * @param data      已加密数据
     * @param publicKey 公钥(BASE64编码)
     * @param sign      数字签名
     */
    public static boolean verify(byte[] data, String publicKey, String sign) throws Exception {
        byte[] keyBytes = base64Decode(publicKey);
        X509EncodedKeySpec keySpec = new X509EncodedKeySpec(keyBytes);
        KeyFactory keyFactory = KeyFactory.getInstance(KEY_ALGORITHM);
        PublicKey publicK = keyFactory.generatePublic(keySpec);
        Signature signature = Signature.getInstance(SIGNATURE_ALGORITHM);
        signature.initVerify(publicK);
        signature.update(data);
        return signature.verify(base64Decode(sign));
    }

    /**
     * 私钥解密
     * @param encryptedData 已加密数据
     * @param privateKey    私钥(BASE64编码)
     */
    public static byte[] decryptByPrivateKey(byte[] encryptedData, String privateKey) throws Exception {
        byte[] keyBytes = base64Decode(privateKey);
        PKCS8EncodedKeySpec pkcs8KeySpec = new PKCS8EncodedKeySpec(keyBytes);
        KeyFactory keyFactory = KeyFactory.getInstance(KEY_ALGORITHM);
        Key privateK = keyFactory.generatePrivate(pkcs8KeySpec);
        Cipher cipher = Cipher.getInstance(keyFactory.getAlgorithm());
        cipher.init(Cipher.DECRYPT_MODE, privateK);
        return segmented(cipher, encryptedData, MAX_DECRYPT_BLOCK);
    }

    /**
     * 公钥解密
     * @param encryptedData 已加密数据
     * @param publicKey     公钥(BASE64编码)
     */
    public static byte[] decryptByPublicKey(byte[] encryptedData, String publicKey) throws Exception {
        byte[] keyBytes = base64Decode(publicKey);
        X509EncodedKeySpec x509KeySpec = new X509EncodedKeySpec(keyBytes);
        KeyFactory keyFactory = KeyFactory.getInstance(KEY_ALGORITHM);
        Key publicK = keyFactory.generatePublic(x509KeySpec);
        Cipher cipher = Cipher.getInstance(keyFactory.getAlgorithm());
        cipher.init(Cipher.DECRYPT_MODE, publicK);
        return segmented(cipher, encryptedData, MAX_DECRYPT_BLOCK);
    }

    /**
     * 公钥加密
     * @param data      源数据
     * @param publicKey 公钥(BASE64编码)
     * @return base64
     */
    public static byte[] encryptByPublicKey(byte[] data, String publicKey) throws Exception {
        byte[] keyBytes = base64Decode(publicKey);
        X509EncodedKeySpec x509KeySpec = new X509EncodedKeySpec(keyBytes);
        KeyFactory keyFactory = KeyFactory.getInstance(KEY_ALGORITHM);
        Key publicK = keyFactory.generatePublic(x509KeySpec);
        // 对数据加密
        Cipher cipher = Cipher.getInstance(keyFactory.getAlgorithm());
        cipher.init(Cipher.ENCRYPT_MODE, publicK);
        return segmented(cipher, data, MAX_ENCRYPT_BLOCK);
    }

    /**
     * 私钥加密
     * @param data       源数据
     * @param privateKey 私钥(BASE64编码)
     */
    public static byte[] encryptByPrivateKey(byte[] data, String privateKey) throws Exception {
        byte[] keyBytes = base64Decode(privateKey);
        PKCS8EncodedKeySpec pkcs8KeySpec = new PKCS8EncodedKeySpec(keyBytes);
        KeyFactory keyFactory = KeyFactory.getInstance(KEY_ALGORITHM);
        Key privateK = keyFactory.generatePrivate(pkcs8KeySpec);
        Cipher cipher = Cipher.getInstance(keyFactory.getAlgorithm());
        cipher.init(Cipher.ENCRYPT_MODE, privateK);
        return segmented(cipher, data, MAX_ENCRYPT_BLOCK);
    }

    /**
     * 分段处理加解密避免JDK的
     * Exception in thread "main" javax.crypto.IllegalBlockSizeException: Data must not be longer than 128 bytes
     * @param cipher 加解密的密码对象
     * @param data 加解密的数据
     * @param MAX_BLOCK 最大处理长度
     */
    private static byte[] segmented(Cipher cipher, byte[] data, final int MAX_BLOCK) throws Exception {
        int inputLen = data.length;
        byte[] cache;
        ByteArrayOutputStream out = new ByteArrayOutputStream();
        for (int i = 0, offSet = 0; inputLen - offSet > 0; i++, offSet = i * MAX_BLOCK) {
            if (inputLen - offSet > MAX_BLOCK) {
                cache = cipher.doFinal(data, offSet, MAX_BLOCK);
            } else {
                cache = cipher.doFinal(data, offSet, inputLen - offSet);
            }
            out.write(cache, 0, cache.length);
        }
        byte[] encryptedData = out.toByteArray();
        out.close();
        return encryptedData;
    }

    /**
     * 将公钥转化为xml格式
     * Java和C# RSA加密密钥互通以及密文字节信息大于117位或128位的终极解决办法
     *  https://blog.csdn.net/wenfengzhuo/article/details/7925433
     *
     * @param encodedPrivateKey 私钥
     * @author wenfengzhuo
     */
    private static String privateKeyInfoToXMLRSAPubKey(byte[] encodedPrivateKey) {
        try {
            StringBuffer buff = new StringBuffer(1024);

            PKCS8EncodedKeySpec pvkKeySpec = new PKCS8EncodedKeySpec(encodedPrivateKey);
            KeyFactory keyFactory = KeyFactory.getInstance("RSA");
            RSAPrivateCrtKey pvkKey = (RSAPrivateCrtKey) keyFactory.generatePrivate(pvkKeySpec);
            buff.append("<RSAKeyValue>");
            buff.append("<Modulus>" + base64Encode(removeMSZero(pvkKey.getModulus().toByteArray())) + "</Modulus>");
            buff.append("<Exponent>" + base64Encode(removeMSZero(pvkKey.getPublicExponent().toByteArray())) + "</Exponent>");
            buff.append("</RSAKeyValue>");
            return buff.toString();
        } catch (Exception e) {
            System.err.println(e);
            return null;
        }
    }

    /**
     * 实现java和C# key的byte[]值互通
     * @author wenfengzhuo
     */
    private static byte[] removeMSZero(byte[] data) {
        byte[] data1;
        int len = data.length;
        if (data[0] == 0) {
            data1 = new byte[data.length-1];
            System.arraycopy(data, 1, data1, 0, len-1);
        } else {
            data1 = data;
        }
        return data1;
    }

    /**
     * base64转码
     */
    public static String base64Encode(byte[] target) {
        return Base64.getEncoder().encodeToString(target);
    }

    /**
     * base64解码
     */
    public static byte[] base64Decode(String target) {
        return Base64.getDecoder().decode(target);
    }

    public static void main(String[] args) throws Exception {
        // 需要被加密的明文
        String target = "public static void main(String[] args) {System.out.println('RSA加密, 密码是: Hello RSA!');}";
        target += target + target;

        // 生成公钥私钥
        Map<String, Object> rsaKeyMap = genKeyPair();
        // 获取公钥
        String publicKey = getPublicKey(rsaKeyMap);
        // 获取私钥
        String privateKey = getPrivateKey(rsaKeyMap);
        // 用私钥对信息生成数字签名
        String sign = sign(target.getBytes(), privateKey);
        // 校验数字签名
        boolean verifyTrue = verify(target.getBytes(), publicKey, sign);
        boolean verifyFalse = verify(target.getBytes(), publicKey, sign.replace("A", "B"));

        System.out.println(String.format("publicKey:%s%n", publicKey));
        System.out.println(String.format("privateKey:%s%n", privateKey));
        System.out.println(String.format("sign:%s%n", sign));
        System.out.println(String.format("verifyTrue:%s%n", verifyTrue));
        System.out.println(String.format("verifyFalse:%s%n", verifyFalse));

        // 公钥加密 && 私钥解密
        byte[] bytesEncryptByPublicKey = encryptByPublicKey(target.getBytes(), publicKey);
        System.out.println(String.format("公钥加密:%s%n", base64Encode(bytesEncryptByPublicKey)));
        byte[] bytesDecryptByPrivateKey = decryptByPrivateKey(bytesEncryptByPublicKey, privateKey);
        System.out.println(String.format("私钥解密:%s%n", new String(bytesDecryptByPrivateKey)));

        // 私钥加密 && 公钥解密
        byte[] bytesEncryptByPrivateKey = encryptByPrivateKey(target.getBytes(), privateKey);
        System.out.println(String.format("私钥加密:%s%n", base64Encode(bytesEncryptByPrivateKey)));
        byte[] bytesDecryptByPublicKey = decryptByPublicKey(bytesEncryptByPrivateKey, publicKey);
        System.out.println(String.format("公钥解密:%s%n", new String(bytesDecryptByPublicKey)));
    }

}
```
