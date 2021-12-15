# JWT

JWT是一串字符串，由三部分组成：头信息Header+载荷Payload+签名信息Signature

- 头信息Header：描述JWT基本信息，

  - typ表示采用JWT令牌，
  - alg（algorithm）表示采用什么算法进行签名，常见算法有HmacSHA256(HS256)、HmacSHA384(HS384)等。

  ```json
  {
  	"typ": "JWT",
      "alg": "HS256"
  }
  ```

- 载荷Payload：描述具体的传输内容，包括一些标准属性，

  - iss：该JWT的签发者。
  - exp：过期时间戳
  - iat：签发时间戳
  - jti：JWTID等等
  - 也可以添加其他需要传递的信息

  ```json
  {
      "iss": "kkk",
      "iat": 1639210774,
      "exp": 1639210800,
      "sub": "test.com"
  }
  ```

- 签名Signature：对头信息和载荷进行签名，保证传输过程中信息不被篡改。比如：将头信息和载荷分别进行base64加密得到字符串a和b，将字符串a和b以点相连并签名得到字符串c，将字符串a、b、c以点相连得到最终token。

# JWT验证

流程：

1. 用户提交用户名、密码到服务器后台
2. 后台验证通过，服务器端生成Token字符串，返回客户端
3. 客户端保存Token，下一次请求资源时，附带上Token信息
4. 服务端验证Token是否由服务器签发的（一般在拦截器中验证），若Token验证通过，则返回需要的资源。

# Java JWT

## 导入依赖

```xml
<!-- JWT -->
        <dependency>
            <groupId>com.auth0</groupId>
            <artifactId>java-jwt</artifactId>
            <version>3.18.2</version>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
            <version>0.9.1</version>
        </dependency>
```

## 编写工具类

```java

/**
 * @author MacroYuan
 * @className JwtUtils
 * @Date 2021/12/11
 * @Version 1.0
 **/
public class JwtUtils {
    public static String getToken(String id, String sec) {
        return JWT.creat().withAudience(id)
            .sign(Algorithm.HMAC256(sec))
    }
}
```

### 验证token

定义拦截器：

```java
package com.example.demo.interceptor;

import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTVerifier;
import com.auth0.jwt.algorithms.Algorithm;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * @author MacroYuan
 * @className AuthInterceptor
 * @Date 2021/12/11
 * @Version 1.0
 **/
public class AuthInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest httpServletRequest,
                             HttpServletResponse httpServletResponse,
                             Object object) throws Exception {
        String token = httpServletRequest.getHeader("token"); //从header获取token
        token = httpServletRequest.getParameter("token"); //从参数获取token
        String userId = JWT.decode(token).getAudience().get(0);
        // 验证token
        JWTVerifier jwtVerifier =
                JWT.require(Algorithm.HMAC256(userId)).build();
        jwtVerifier.verify(token);
        return true;
    }
}


```

拦截器配置：

```java
package com.example.demo.interceptor;

import org.springframework.context.annotation.Bean;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * @author MacroYuan
 * @className InterceptorConfig
 * @Date 2021/12/11
 * @Version 1.0
 **/
@Configuration
public class InterceptorConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(authInterceptor())
                .addPathPatterns("/**");
    }
    
    @Bean
    public AuthInterceptor authInterceptor() {
        return new AuthInterceptor();
    }
}

```

