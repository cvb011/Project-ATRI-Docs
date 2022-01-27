# 配置本项目（Setting Proj）

## 配置预览
打开位于项目根目录的 `config.yml`，你会得到如下列表：
```yaml
BotSelfConfig:
  host: "127.0.0.1"
  port: 20000
  debug: false
  superusers: ["1234567890"]
  nickname: ["ATRI", "Atri", "atri", "亚托莉", "アトリ"]
  command_start: ["", "/"]
  command_sep: ["."]
  session_expire_timeout: 60
  proxy: ""  # 请参考文档

SauceNAO:
  key: ""

Setu:
  reverse_proxy: False # 请参考文档
  reverse_proxy_domain: ""
```

其中:

- BotSelfConfig 为本项目机器人主体设置
- SauceNAO 为搜图相关设置
- Setu 为色图相关设置

!!! info
    如果你看不懂本设置中的英文词汇，请善用翻译工具

## 开始配置

### BotSelfConfig

- host: 配置机器人监听的 `IP/主机名`
- port: 配置机器人监听的 `端口`
- debug: 配置机器人的调试模式，默认为 `False`
- superusers: 配置机器人的 `超级用户`，可以设置多个，用逗号分隔，即QQ号
- nickname: 机器人的 `昵称`，可以更改/设置多个，用逗号分隔
- command_start: 配置机器人的命令前缀，可以设置多个，用逗号分隔
- command_sep: 配置机器人的命令分隔符，可以设置多个，用逗号分隔
- session_expire_timeout: 配置机器人的会话过期时间，单位为秒
- proxy: 配置机器人的代理，默认为空。

!!! warning
    对于 `proxy`，国内用户还请自行安排木弟子并设置代理

    本项目初始化资源位于 `Jsdelivr`，由于某些用户将其用于不法用途，现已被国内封杀。故国内用户 `很可能` 无法完全、顺利地拉取所需必要资源。

!!! info
    如果你不是很能理解个别设置，我的建议是：不要动

### SauceNAO

- key: SauceNAO 搜图的 API 密钥

!!! info
    如果你是第一次接触此玩意:

    - 进入 [SauceNAO](https://saucenao.com/user.php)用户界面，登录/注册
    - 在个人信息预览左侧 `Search` 列表中找到 `api`，进入
    - 在 `api` 界面中找到 Key word: `api key`，其内容即为所需的 API 密钥


### Setu

- reverse_proxy: 配置是否开启涩图反向代理，默认为 `False`
- reverse_proxy_domain: 配置涩图反向代理的域名，默认为空。如上一个配置为 `True`，则此项必须配置

!!! info
    国内用户设置了代理的可以无需设置

    对于只想用涩图的国内用户，请继续阅读以下内容

涩图的来源是 `Pixiv`，其含有图片防盗措施，源址为 `i.pximg.net`，只要 `Referer` 是空值，或者不是来自 `Pixiv` 的请求，则会被拒绝（`403`）

再一个，涩图api返回的图片 `domain` 为 `pixiv.cat`，现已被列入 `GFW list`，国内用户无法访问

接下来将针对国内用户进行教学、部署属于自己的 `Pixiv` 图片反代:

??? example "0w0"
    - 如果你是 `nginx` 用户:
    ```
    proxy_cache_path /path/to/cache levels=1:2 keys_zone=pximg:10m max_size=10g inactive=7d use_temp_path=off;

    server {
        listen 443 ssl http2;

        ssl_certificate /path/to/ssl_certificate.crt;
        ssl_certificate_key /path/to/ssl_certificate.key;

        server_name i.pixiv.cat;
        access_log off;

        location / {
        proxy_cache pximg;
        proxy_pass https://i.pximg.net;
        proxy_cache_revalidate on;
        proxy_cache_use_stale error timeout updating http_500 http_502 http_503 http_504;
        proxy_cache_lock on;
        add_header X-Cache-Status $upstream_cache_status;
        proxy_set_header Host i.pximg.net;
        proxy_set_header Referer "https://www.pixiv.net/";

        proxy_cache_valid 200 7d;
        proxy_cache_valid 404 5m;
    }
    }
    ```

    - 如果你是 `CloudFlare` 用户，并且了解 `CloudFlare Worker`:
    ```javascript
    addEventListener("fetch", event => {
    let url = new URL(event.request.url);
    url.hostname = "i.pximg.net";

    let request = new Request(url, event.request);
    event.respondWith(
        fetch(request, {
        headers: {
            'Referer': 'https://www.pixiv.net/',
            'User-Agent': 'Cloudflare Workers'
        }
        })
    );
    });
    ```

    - 如果你啥也不是:
        - 设置为: `True`
        - domain填入: `i.pixiv.re`

## 结束

至此，机器人的配置已全部完成