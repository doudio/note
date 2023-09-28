> 在application.yml文件中设置multipart location ，并重启项目

```yaml
spring:
  http:
    multipart:
      location: /data/upload_tmp

---

server
  tomcat:
     basedir: /tmp/tomcat

```

> WebUtil, 基于 >> https://www.callicoder.com/spring-boot-file-upload-download-rest-api-example/

```java
import org.springframework.core.io.FileUrlResource;
import org.springframework.core.io.Resource;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;

import javax.servlet.http.HttpServletRequest;
import java.io.IOException;

/**
 * @description: 文件下载工具类
 * @author: doudio
 * @create: 2021-06-04
 **/
public class WebUtil {

    /**
     * 根据不同文件获取不同的 contentType
     * 无法确定文件类型 时使用 application/octet-stream
     *
     * @param resource org.springframework.core.io.FileUrlResource
     * @param request request
     * @return contentType
     */
    public static String getContentType(Resource resource, HttpServletRequest request) {
        try {
            return request.getServletContext().getMimeType(resource.getFile().getAbsolutePath());
        } catch (IOException ex) {
            return "application/octet-stream";
        }
    }

    /**
     * 加载文件作为资源
     * @param filePath 文件全路径
     * @return
     */
    public static Resource loadFileAsResource(String filePath) {
        try {
            Resource resource = new FileUrlResource(filePath);
            if(resource.exists()) {
                return resource;
            } else {
                throw new RuntimeException("File not found");
            }
        } catch (Exception ex) {
            throw new RuntimeException("Load file as Resource", ex);
        }
    }

    /**
     * 一键下载 , 文件名从文件路径中获取
     * URLDecoder.decode("fileName","utf-8");
     *  @GetMapping("/downloadFile")
     *  public ResponseEntity<Resource> downloadFile(String path, HttpServletRequest request) {
     *      return WebUtil.oneClickDownload(path, request);
     *  }
     * @param filePath 下载的路径
     * @param request request
     * @return mvc 方法中直接返回 ResponseEntity<Resource>
     */
    public static ResponseEntity<Resource> oneClickDownload(String filePath, HttpServletRequest request) {
        // Load file as Resource
        Resource resource = loadFileAsResource(filePath);
        // 从 Resource 中获取文件名
        String fileName = null;
        try {
            fileName = resource.getFile().getName();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        return ResponseEntity.ok()
                .contentType(MediaType.parseMediaType(WebUtil.getContentType(resource, request)))
                .header(HttpHeaders.CONTENT_DISPOSITION, String.format("attachment; filename=\"%s\"", fileName))
                .body(resource);
    }

    /**
     * 一键下载自己指定FileName
     * @param filePath 下载的路径
     * @param fileName 自己定义文件名
     * @param request request
     * @return
     */
    public static ResponseEntity<Resource> oneClickDownload(String filePath, String fileName, HttpServletRequest request) {
        // Load file as Resource
        Resource resource = loadFileAsResource(filePath);
        // 从 Resource 中获取文件名
        return ResponseEntity.ok()
                .contentType(MediaType.parseMediaType(WebUtil.getContentType(resource, request)))
                .header(HttpHeaders.CONTENT_DISPOSITION, String.format("attachment; filename=\"%s\"", fileName))
                .body(resource);
    }

}
```