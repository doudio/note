> ## Robot

Robot 类用于生成本机系统输入事件，用于测试自动化，自动运行演示和需要鼠标和键盘控制的其他应用程序。 Robot的主要目的是为了方便Java平台实现的自动化测试。 

> ## 经过封装后的 Robot api 可以现实操作电脑的基本需求

```java
public static void main(String[] args) {
    String outString = "Hello %s";
    for (int i = 0; i < 100; i++) {
        RobotUtils.output(String.format(outString, i));
        RobotUtils.enter();
    }
}
```

> Robot Demo

```java
/**
 * Robot的工具类
 */
@Slf4j
public class RobotUtils {

    /**
     * awt 机器人
     */
    private static Robot robot;

    /**
     * 基础延迟毫秒
     */
    private static int BaseTime = 100;

    /**
     * 随机数
     */
    private static Random random = new Random();

    static {
        try {
            robot = new Robot();
        } catch (AWTException e) {
            log.error("robot init exception: {}", e);
            throw new RuntimeException(e);
        }
    }

    public static Robot getRobot() {
        return robot;
    }

    /**
     * 按下某个键
     *
     * @param keyEvent
     */
    public static void key(KeyEvent keyEvent) {
        key(keyEvent.getExtendedKeyCode());
    }

    public static void key(int keyEvent) {
        robot.keyPress(keyEvent);
        robot.delay(getRandom());
        robot.keyRelease(keyEvent);
    }

    /**
     * 获取一个随机值
     */
    public static int getRandom() {
        return random.nextInt(10) + BaseTime;
    }

    /**
     * 获取鼠标当前位置
     */
    public static Point getMousePosition() {
        return MouseInfo.getPointerInfo().getLocation();
    }

    /**
     * 将鼠标移动到 x与y 轴
     *
     * @param x
     * @param y
     */
    public static void mouseMove(int x, int y) {
        robot.mouseMove(x, y);
    }

    /**
     * 部分情况下 mouseMove 无法做到精准位移不过通过这个方式多位移一下就好了
     */
    public static void mouseMove(int x, int y, int maxTimes) {
        for (int count = 0;
             (MouseInfo.getPointerInfo().getLocation().getX() != x ||
                     MouseInfo.getPointerInfo().getLocation().getY() != y) &&
                     count < maxTimes; count++) {
            robot.mouseMove(x, y);
        }
    }

    /**
     * 单击鼠标
     */
    public static void click() {
        robot.mousePress(KeyEvent.BUTTON1_MASK);
        robot.delay(getRandom());
        robot.mouseRelease(KeyEvent.BUTTON1_MASK);
    }

    /**
     * 右击鼠标
     */
    public static void rightClick() {
        robot.mousePress(KeyEvent.BUTTON3_MASK);
        robot.delay(getRandom());
        robot.mouseRelease(KeyEvent.BUTTON3_MASK);
    }

    /**
     * 按下回车键
     */
    public static void enter() {
        robot.keyPress(KeyEvent.VK_ENTER);
        robot.delay(getRandom());
        robot.keyRelease(KeyEvent.VK_ENTER);
    }

    /**
     * 输出一串字符
     *
     * @param text
     */
    public static void output(String text) {
        if (null != text && !text.isEmpty()) {
            char[] chars = text.toCharArray();
            for (char c : chars) {
                try {
                    RobotUtils.key(KeyEvent.getExtendedKeyCodeForChar(c));
                } catch (Exception e) {
                    log.error("getExtendedKeyCodeForChar Incompatible:: {}", c);
                }
            }
        }
    }

    /**
     * 复制
     */
    public static void copy() {
        robot.keyPress(KeyEvent.VK_CONTROL);
        robot.keyPress(KeyEvent.VK_C);
        robot.delay(getRandom());
        robot.keyRelease(KeyEvent.VK_C);
        robot.keyRelease(KeyEvent.VK_CONTROL);
    }

    /**
     * 粘贴
     */
    public static void paste() {
        robot.keyPress(KeyEvent.VK_CONTROL);
        robot.keyPress(KeyEvent.VK_V);
        robot.delay(getRandom());
        robot.keyRelease(KeyEvent.VK_V);
        robot.keyRelease(KeyEvent.VK_CONTROL);
    }

    /**
     * 全选
     */
    public static void electionAll() {
        robot.keyPress(KeyEvent.VK_CONTROL);
        robot.keyPress(KeyEvent.VK_A);
        robot.delay(getRandom());
        robot.keyRelease(KeyEvent.VK_A);
        robot.keyRelease(KeyEvent.VK_CONTROL);
    }

}
```