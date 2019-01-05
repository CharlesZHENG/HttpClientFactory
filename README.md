# HttpClientFactory For netcore And framework
### NUGET Install-Package HttpClientFactory

supported netcore2.0 and framework4.5+

## Why need HttpClientFactory?

As you know, HttpClient has a [trap](https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/) for using!

1. if not Singleton,
each instance of HttpClient will open a new socket connection 
and on  high traffic sites you can exhaust the available pool and receive a System.Net.Sockets.SocketException
2. if Singleton,HttpClient doesn't respect DNS changes!


## So how to use HttpClient correctly？

```
//useage 1： same Host use same HttpClient
PerHostHttpClientFactory perHostHttpClientFactory = new PerHostHttpClientFactory();// can be static
HttpClient = perHostHttpClientFactory.GetHttpClient("http://www.baidu.com").HttpClient;

//useage 2： per url use per HttpClient
PerUrlHttpClientFactory perUrlHttpClientFactory = new PerUrlHttpClientFactory();
HttpClient = perUrlHttpClientFactory.GetHttpClient("http://www.baidu.com").HttpClient;

```

## Easy to implement
```
	public class XXXHttpClientFactory : HttpClientFactoryBase
    {
        protected override string GetCacheKey(string key)
        {
            return key;
        }
				
				
		protected override HttpClient CreateHttpClient(HttpMessageHandler handler)
        {
            return new HttpClient(handler)
            {
                Timeout = TimeSpan.FromSeconds(20),
            };
        }

        protected override HttpMessageHandler CreateMessageHandler()
        {
            var handler = new HttpClientHandler();
            handler.Proxy = new WebProxy("xxx");
            return handler;
        }
    }
```

## Remark
1. Default Timeout in HttpClient is TimeSpan.FromSeconds(200)
2. Default [ConnectionLeaseExpired](http://byterot.blogspot.com/2016/07/singleton-httpclient-dns.html) is TimeSpan.FromMinutes(1)