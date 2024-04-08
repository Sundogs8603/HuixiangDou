# Web

web 版本前后端源码，效果同 https://openxlab.org.cn/apps/detail/tpoisonooo/huixiangdou-web

整个服务分 前后端一体 和 算法两部分，中间用 redis queue 生产者/消费者模式通信。

## 启动

1. 设置环境变量，启动 redis-server（修改 `/etc/redis/redis.conf` 中的 `requirepass` 配置密码）。必须

```bash
$ cat env.sh

export PYTHONUNBUFFERED=1
export REDIS_HOST=10.1.52.22
export REDIS_PASSWORD=${REDIS_PASSWORD}
export REDIS_PORT=6380
export JWT_SECRET=${JWT_SEC}
export SERVER_PORT=7860
export HUIXIANGDOU_LARK_ENCRYPT_KEY=thisiskey
export HUIXIANGDOU_LARK_VERIFY_TOKEN=sMzyjKi9vMlEhKCZOVtBMhhl8x23z0AG

# set your service endpoint(open to Internet callback from lark and wechat)
export HUIXIANGDOU_MESSAGE_ENDPOINT=http://10.1.52.36:18443
export COOKIE_SECURE=1
```
⚠️ 重要事项：  如果不用 https 安全链接，需要 `unset COOKIE_SECURE`（不是设成 0）。否则知识库登录会异常

> 怎么算是用 https ？ 就是**你买了域名且能 https 打头， 如果你用的是裸 ip 地址，那就是没有！** 
>
> 例如：
> 
> https://openxlab.com/api    是 https，需要 `export COOKIE_SECURE=1` 
> 
> https://10.1.2.22   不是，取消 cookie
> 
> http://101.204.1.5  不是
> 


2. 编译前端 & 运行后端服务

安装 Node.js `npm` (需要版本为 20.x , 安装时, 可根据用户权限需要自行添加 sudo + 命令) 

```bash
apt update
apt install nodejs npm
node -v # v20.12.0
```

如果 `node -v` 版本太老 （10.x），则需要升级 node 版本

```bash
npm install n -g
n stable
hash -r
node -v # v20.12.0
```

编译项目

```bash
cd front-end
npm install && npm run build
```

安装依赖、运行后端

```bash
cd ../../ # 从front-end返回到huixiangdou目录下, 该目录内含有web文件夹
python3 -m pip install -r web/requirements.txt
python3 -m web.main
```

4. 运行算法 pipeline

```bash
# 先开个终端窗口，启动 LLM hybrid proxy
python3 -m huixiangdou.service.llm_server_hybrid --config_path config.ini

# 再开个窗口，监听服务
python3 -m web.proxy.main
```

5. 测试
打开服务器 7860 端口，创建知识库测试效果