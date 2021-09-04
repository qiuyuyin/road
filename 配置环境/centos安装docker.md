**centos7服务器安装docker**

直接参照这个文章来进行安装即可：

https://developer.aliyun.com/article/765545

**docker中配置redis**

docker run  --log-opt max-size=100m --log-opt max-file=2 -p 6379:6379 --name myredis -v /home/redis/myredis/redis.conf:/etc/redis/redis.conf -v /home/redis/myredis/data:/data -d redis redis-server /etc/redis/redis.conf  --appendonly yes  --requirepass 000415

https://blog.csdn.net/weixin_45821811/article/details/116211724

注意要标记密码



获取的验证码：

1：

{

  "code": 200,

  "message": "获取验证码成功",

  "data": "101058"

}

123：

{

```
{"code":200,"message":"获取验证码成功","data":"625701"}
```

}

420021211224727