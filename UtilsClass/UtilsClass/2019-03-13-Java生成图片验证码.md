> java 生成图片验证码

> ImageBuilder

```java
import lombok.Data;

import javax.imageio.ImageIO;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.IOException;
import java.io.Serializable;
import java.util.Random;

@Data
public class ImageBuilder {
    //图片验证码长度为4
    private int length = 4;
    //图片宽度
    private int width = 100;
    //图片高度
    private int height = 35;
    //字体高度
    private int fontHeight = height - 4;
    //代码字符
    private char[] codeChar = {'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J',
            'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W',
            'X', 'Y', 'Z', 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j',
            'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w',
            'x', 'y', 'z', '0', '1', '2', '3', '4', '5', '6', '7', '8', '9'};

    //字符之间的宽度
    private int x_space = 3;
    //字符与图片左右边框的间隔
    private int lr_space = 4;
    //一个字符的宽度
    private int x = (width - 2 * lr_space - x_space * (length - 1)) / length;
    //画直线数量
    private int lineSize = height / 4;
    //字符高度
    private int codeY = height - 4;
    //圆的半径
    private int r = 3;
    //噪点的个数
    private int np_num = 25;
    //图片
    private BufferedImage image;
    //验证码
    private String verifyCode;


    public ImageBuilder builder() {
        BufferedImage image = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);
        Graphics2D graphics = image.createGraphics();
        //背景颜色
        graphics.setColor(Color.white);
        graphics.fillRect(0, 0, width, height);
        Font font = new Font(null, Font.BOLD | Font.ITALIC, fontHeight);
        graphics.setFont(font);
        //边框
        graphics.setColor(Color.black);
        graphics.drawRect(0, 0, width - 1, height - 1);
        //画线
        for (int i = 0; i < lineSize; i++) {
            int ly = lineSize * (i + 1);
            graphics.setColor(randomColor());
            graphics.drawLine(1, ly, width - 2, ly);
        }
        Random random = new Random();
        //生成噪点
        for (int i = 0; i < np_num; i++) {
            graphics.setColor(randomColor());
            int x = random.nextInt(width);
            int y = random.nextInt(height);
            graphics.fillOval(x, y, r, r);
        }
        StringBuffer sb = new StringBuffer();
        //获取字符
        for (int i = 0; i < length; i++) {
            graphics.setColor(randomColor());
            String codeStr = String.valueOf(codeChar[new Random().nextInt(codeChar.length)]);
            sb.append(codeStr);
            graphics.drawString(codeStr, lr_space + i * x_space + i * x, codeY);
        }
        this.setVerifyCode(sb.toString());
        this.setImage(image);
        //清空缓存
        graphics.dispose();
        return this;
    }

    /**
     * 随机生成一个颜色
     *
     * @return
     */
    private Color randomColor() {
        // 创建一个随机数生成器类
        Random random = new Random();
        int red = random.nextInt(255);
        int green = random.nextInt(255);
        int blue = random.nextInt(255);
        return new Color(red, green, blue);
    }

    public static void main(String[] args) throws IOException {
        ImageBuilder iBuilder = new ImageBuilder().builder();
        File file = new File("E:/temp/img.png");
        ImageIO.write(iBuilder.getImage(), "png", file);
    }
}
```

> 图片二维码样式

![](img/ImageBuilder.png)

> ValidateCode

```java
import javax.imageio.ImageIO;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;
import java.util.Date;
import java.util.Random;

public class ValidateCode {
    // 图片的宽度。
    private int width = 160;
    // 图片的高度。
    private int height = 40;
    // 验证码字符个数
    private int codeCount = 5;
    // 验证码干扰线数
    private int lineCount = 150;
    // 验证码
    private String code = null;
    // 验证码图片Buffer
    private BufferedImage buffImg = null;

    // 验证码范围,去掉0(数字)和O(拼音)容易混淆的(小写的1和L也可以去掉,大写不用了)
    private char[] codeSequence = {'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J',
            'K', 'L', 'M', 'N', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W',
            'X', 'Y', 'Z', '1', '2', '3', '4', '5', '6', '7', '8', '9'};

    /**
     * 默认构造函数,设置默认参数
     */
    public ValidateCode() {
        this.createCode();
    }

    /**
     * @param width  图片宽
     * @param height 图片高
     */
    public ValidateCode(int width, int height) {
        this.width = width;
        this.height = height;
        this.createCode();
    }

    /**
     * @param width     图片宽
     * @param height    图片高
     * @param codeCount 字符个数
     * @param lineCount 干扰线条数
     */
    public ValidateCode(int width, int height, int codeCount, int lineCount) {
        this.width = width;
        this.height = height;
        this.codeCount = codeCount;
        this.lineCount = lineCount;
        this.createCode();
    }

    public void createCode() {
        int x = 0, fontHeight = 0, codeY = 0;
        int red = 0, green = 0, blue = 0;

        x = width / (codeCount + 2);//每个字符的宽度(左右各空出一个字符)
        fontHeight = height - 2;//字体的高度
        codeY = height - 4;

        // 图像buffer
        buffImg = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);
        Graphics2D g = buffImg.createGraphics();
        // 生成随机数
        Random random = new Random();
        // 将图像填充为白色
        g.setColor(Color.WHITE);
        g.fillRect(0, 0, width, height);
        // 创建字体,可以修改为其它的
        Font font = new Font("Fixedsys", Font.PLAIN, fontHeight);
//        Font font = new Font("Times New Roman", Font.ROMAN_BASELINE, fontHeight);
        g.setFont(font);

        for (int i = 0; i < lineCount; i++) {
            // 设置随机开始和结束坐标
            int xs = random.nextInt(width);//x坐标开始
            int ys = random.nextInt(height);//y坐标开始
            int xe = xs + random.nextInt(width / 8);//x坐标结束
            int ye = ys + random.nextInt(height / 8);//y坐标结束

            // 产生随机的颜色值，让输出的每个干扰线的颜色值都将不同。
            red = random.nextInt(255);
            green = random.nextInt(255);
            blue = random.nextInt(255);
            g.setColor(new Color(red, green, blue));
            g.drawLine(xs, ys, xe, ye);
        }

        // randomCode记录随机产生的验证码
        StringBuffer randomCode = new StringBuffer();
        // 随机产生codeCount个字符的验证码。
        for (int i = 0; i < codeCount; i++) {
            String strRand = String.valueOf(codeSequence[random.nextInt(codeSequence.length)]);
            // 产生随机的颜色值，让输出的每个字符的颜色值都将不同。
            red = random.nextInt(255);
            green = random.nextInt(255);
            blue = random.nextInt(255);
            g.setColor(new Color(red, green, blue));
            g.drawString(strRand, (i + 1) * x, codeY);
            // 将产生的四个随机数组合在一起。
            randomCode.append(strRand);
        }
        // 将四位数字的验证码保存到Session中。
        code = randomCode.toString();
    }

    public void write(String path) throws IOException {
        OutputStream sos = new FileOutputStream(path);
        this.write(sos);
    }

    public void write(OutputStream sos) throws IOException {
        ImageIO.write(buffImg, "png", sos);
        sos.close();
    }

    public BufferedImage getBuffImg() {
        return buffImg;
    }

    public String getCode() {
        return code;
    }

    /**
     * 测试函数,默认生成到d盘
     *
     * @param args
     */
    public static void main(String[] args) {
        ValidateCode vCode = new ValidateCode(160, 40, 5, 150);
        try {
            String path = "E:/temp/" + new Date().getTime() + ".png";
            System.out.println(vCode.getCode() + " >" + path);
            vCode.write(path);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

> 图片二维码样式

![](img/ValidateCode.png)