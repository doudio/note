MongoDB 入门

> 利用Docker安装容器

```shell
# 拉取容器
$ docker pull mongo:latest

# -p 27017:27017 ：映射容器服务的 27017 端口到宿主机的 27017 端口。
## 外部可以直接通过 宿主机 ip:27017 访问到 mongo 的服务。
# --auth：需要密码才能访问容器服务。
$ docker run -itd --name mongo -p 27017:27017 mongo --auth

$ docker exec -it mongo mongo admin
# 创建一个名为 admin，密码为 123456 的用户。
>  db.createUser({ user:'admin',pwd:'123456',roles:[ { role:'userAdminAnyDatabase', db: 'admin'},"readWriteAnyDatabase"]});
# 尝试使用上面创建的用户信息进行连接。
> db.auth('admin', '123456')
```

* 完全取自: https://www.runoob.com/docker/docker-install-mongodb.html

> SpringBoot 连接 MongoDB 实现 CRUD 

> 导入

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

> 配置 Mongo

```properties
#单台服务配置
spring.data.mongodb.uri=mongodb://admin:123456@localhost:27017/admin
#集群服务配置
#spring.data.mongodb.uri=mongodb://user:pwd@ip1:port1,ip2:port2/database

#spring.data.mongodb.host=localhost
#spring.data.mongodb.database=admin
#spring.data.mongodb.port=27017
#spring.data.mongodb.username=admin
#spring.data.mongodb.password=123456
```

> domain

```java
@Data
public class Stu {
    @Id
    private String id;
    private String name;
}
```

> Repository

```java
import org.springframework.data.mongodb.repository.MongoRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface StuRepository extends MongoRepository<Stu, String> {
}
```

> Controlller

```java
@RestController
public class MongodbController {

    @Autowired
    private StuRepository stuRepository;

    @GetMapping("/")
    public List<Stu> list() {
        return stuRepository.findAll();
    }

    @GetMapping("/{id}")
    public Optional<Stu> listById(@PathVariable("id") String id) {
        return stuRepository.findById(id);
    }

    @PostMapping("/")
    public Stu save(Stu stu) {
        return stuRepository.save(stu);
    }

    @DeleteMapping("/{id}")
    public String delById(@PathVariable("id") String id) {
        stuRepository.deleteById(id);
        return "success";
    }

}
```