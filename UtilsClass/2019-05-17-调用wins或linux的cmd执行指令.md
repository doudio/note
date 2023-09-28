> ## 调用wins或linux的cmd执行指令.md

> #### wins Util

```java
public static List<String> executeLinuxCmdOne(String commands) {
    List<String> resultList = new ArrayList<String>();
    String[] cmd = {"cmd.exe", "/c", commands};
    try {
        Process process = new ProcessBuilder().command(cmd).start();
        BufferedReader in = new BufferedReader(new InputStreamReader(process.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            resultList.add(line);
        }
        process.destroy();
        in.close();
    } catch (IOException ioe) {
        ioe.printStackTrace();
    }
    return resultList;
}
```

> #### Linux Util

```java
public static List<String> executeLinuxCmdOne(String commands) {
    List<String> resultList = new ArrayList<String>();
    String[] cmd = {"/bin/sh", "-c", commands};
    try {
        Process process = new ProcessBuilder().command(cmd).start();
        BufferedReader in = new BufferedReader(new InputStreamReader(process.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            resultList.add(line);
        }
        process.destroy();
        in.close();
    } catch (IOException ioe) {
        ioe.printStackTrace();
    }
    return resultList;
}
```

