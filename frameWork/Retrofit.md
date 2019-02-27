#### Difference between interceptor and NetworkInterceptor in retrofit

>关于interceptors 可以查看文档[Interceptors](https://github.com/square/okhttp/wiki/Interceptors)

---
Application Interceptor   
- 不用关心redirects和reties的Response中间的过程
- 永远只会调用一次，不管Http Response是否是从缓存中直接取到的
- 可以监控原始的请求，不关心其它诸如If-None-Match的Header
- 允许不调用Chain.proceed()
- 允许重试多次调用Chain.proceed()。

NetworkInterceptor

- 可以操作redirects和reties的过程
- 不会调用缓存的Response
- 可以监控网络传输交互的数据
- 可以获取Connection携带的请求信息



#### GZip压缩
> okHttp3 支持gzip解压缩，需要在请求时加入header 
```java
Accept-Encoding: gzip
```
并且服务端返回时header需要携带
```java
Content-Encoding: gzip
```

> 如果需要向服务端post大量数据，官方采用拦截器的方式实现，[gzip官方源码](https://github.com/square/okhttp/blob/7135628c645892faf1a48a8cff464e0ed4ad88cb/samples/guide/src/main/java/okhttp3/recipes/RequestBodyCompression.java)

```java
import java.io.IOException;

import okhttp3.Interceptor;
import okhttp3.MediaType;
import okhttp3.Request;
import okhttp3.RequestBody;
import okhttp3.Response;
import okhttp3.OkHttpClient;
import okio.BufferedSink;
import okio.GzipSink;
import okio.Okio;

public class GzipRequestInterceptor implements Interceptor {
    @Override
    public Response intercept(Chain chain) throws IOException {
        Request originalRequest = chain.request();
        if (originalRequest.body() == null || originalRequest.header("Content-Encoding") != null) {
            return chain.proceed(originalRequest);
        }

        Request compressedRequest = originalRequest.newBuilder()
                .header("Content-Encoding", "gzip")
                .method(originalRequest.method(), gzip(originalRequest.body()))
                .build();
        return chain.proceed(compressedRequest);
    }

    private RequestBody gzip(final RequestBody body) {
        return new RequestBody() {
            @Override
            public MediaType contentType() {
                return body.contentType();
            }

            @Override
            public long contentLength() {
                return -1; // 无法提前知道压缩后的数据大小
            }

            @Override
            public void writeTo(BufferedSink sink) throws IOException {
                BufferedSink gzipSink = Okio.buffer(new GzipSink(sink));
                body.writeTo(gzipSink);
                gzipSink.close();
            }
        };
    }
};

//然后
OkHttpClient client = new OkHttpClient.Builder()
    .addNetworkInterceptor(new GzipRequestInterceptor())
    .build();
```