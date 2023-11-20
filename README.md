# PerBlogApi 接口文档 

陈公桥 


## 功能描述

1. 获取电影数据
2. 刷新登录
3. 发送验证码
4. 校验验证码
5. 注册(修改密码)
6. 获取用户信息 , 歌单，收藏，mv, dj 数量

## 运行

```shell
$ node app.js
```

服务器启动默认端口为 3000, 若不想使用 3000 端口 , 可使用以下命令 : Mac/Linux

```shell
$ PORT=4000 node app.js
```

windows 下使用 git-bash 或者 cmder 等终端执行以下命令 :

```shell
$ set PORT=4000 && node app.js
```

服务器启动默认 host 为 localhost,如果需要更改, 可使用以下命令 : Mac/Linux

```shell
$ HOST=127.0.0.1 node app.js
```

windows 下使用 git-bash 或者 cmder 等终端执行以下命令 :

```shell
$ set HOST=127.0.0.1 && node app.js
```




## 接口文档

### 调用前须知

!> 本项目不提供线上 demo，请不要轻易信任使用他人提供的公开服务，以免发生安全问题,泄露自己的账号和密码

!> 为使用方便,降低门槛, 文档示例接口直接使用了 GET 请求,本项目同时支持 GET/POST 请按实际需求使用 (POST 请求 url 必须添加时间戳,使每次请求 url 不一样,不然请求会被缓存)

!> 由于接口做了缓存处理 ( 缓存 2 分钟,不缓存数据极容易引起网易服务器高频 ip 错误 , 可在 app.js 设置 , 可能会导致登录后获取不到 cookie), **相同的 url** 会在两分钟内只向网易服务器发一次请求 , 如果遇到不需要缓
存结果的接口 , 可在请求 url 后面加一个时间戳参数使 url 不同 , 例子 :
`/simi/playlist?id=347230&timestamp=1503019930000` (之所以加入缓存机制是因为项目早期没有缓存机制，很多 issues 都是报 IP 高频，请按自己需求改造缓存中间件(app.js)，源码不复杂)

!> 不要频繁调登录接口,不然可能会被风控,登录状态还存在就不要重复调登录接口

!> 如果是跨域请求 , 请在所有请求带上 `xhrFields: { withCredentials: true }` (axios 为 `withCredentials: true`, Fetch API 为 `fetch(url, { credentials: 'include' })`), 或直接手动传入 cookie (参见 `登录`), 否则
可能会因为没带上 cookie 导致 301, 具体例子可看 `public/test.html`, 访问`http://localhost:3000/test.html`(默认端口的话) 例子使用 jQuery 和 axios

!> 301 错误基本都是没登录就调用了需要登录的接口,如果登录了还是提示 301, 基本都是缓存把数据缓存起来了,解决方法是加时间戳或者等待 2 分钟或者重启服务重新登录后再调用接口,可自行改造缓存方法

!> 部分接口如登录接口不能调用太频繁 , 否则可能会触发 503 错误或者 ip 高频错误 ,若需频繁调用 , 需要准备 IP 代理池 (更新:已加入缓存机制,但仍需注意).

!> 本项目仅供学习使用,请尊重版权，请勿利用此项目从事商业行为或进行破坏版权行为

!> 文档可能会有缓存 , 如果文档版本和 github 上的版本不一致,请清除缓存再查看

!> 由于网易限制,此项目在国外服务器或部分国内云服务上使用会受到限制,如 `460 cheating异常`,如需解决 , 可使用`realIP`参数,传进国内IP解决,如:`?realIP=116.25.146.177`
即可解决

!> 图片加上 `?param=宽y高` 可控制图片尺寸，如 `http://p4.music.126.net/JzNK4a5PjjPIXAgVlqEc5Q==/109951164154280311.jpg?param=200y200`, `http://p4.music.126.net/JzNK4a5PjjPIXAgVlqEc5Q==/109951164154280311.jpg?param=50y50`

!> 分页接口返回字段里有`more`,more 为 true 则为有下一页


### 获取电影数据

说明 : 已观看的电影数据

**必选参数 :**

```
type:电影类型

ifmvoie:是否是电影

sort:列表字段排序

order:排序方式
```

**接口地址 :** `/api/movielist`

**调用例子 :** `/api/movielist?type=1210&ifmovie=-1&sort=-1&order=0`

`type` 取值:

```
1200:默认值 全部 
1210:动画
1220:剧情
1230:动作
1240:爱情
1250:喜剧
1260:奇幻
1270:科幻
```

`ifmovie`取值:

```
-1:默认值 全部 
0:属于剧集
1:属于电影

```

`sort`取值:

```
-1:默认值 不经过排序 并随机输出
0:观看时间 
1:上映时间
2:评分

```

`order`取值:

```
0:升序
1:降序
```

返回示例

```
{
    "code": 200,
    "data": [
        {
            "id": 6,
            "movieid": 35337634,
            "name": "地狱乐 地獄楽",
            "imgurl": "https://img2.doubanio.com/view/photo/s_ratio_poster/public/p2885293812.jpg",
            "grade": "7.8",
            "website": "https://www.jigokuraku.com",
            "showdate": "2023-04-01(日本)",
            "movietime": "",
            "type": "动作 动画 奇幻 ",
            "ifmovie": false,
            "content": "故事以江户时代后期为舞台，最强死刑犯忍者「画眉丸」为了能再次与最爱的妻子见面，与处刑人「山田浅右卫门佐切」一同前往被称为极乐净土的小岛，找寻「不老不死的仙药」以换取无罪释放，并在岛上与神秘的怪物以及其他死刑犯互相残杀。",
            "watchdate": "2023-04-04",
            "url": "https://movie.douban.com/subject/35337634/"
        },
        {
            "id": 4,
            "movieid": 26709258,
            "name": "罗小黑战记",
            "imgurl": "https://img9.doubanio.com/view/photo/s_ratio_poster/public/p2568288336.jpg",
            "grade": "8.0",
            "website": "https://luoxiaohei-movie.com",
            "showdate": "2019-09-07(中国大陆)",
            "movietime": "101分钟",
            "type": "动作 动画 奇幻 ",
            "ifmovie": true,
            "content": "在熙攘的人类世界里，很多妖精隐匿其中，他们与人类相安无事地生活着。猫妖罗小黑因为家园被破坏，开始了它的流浪之旅。这场旅途中惺惺相惜的妖精同类与和谐包容的人类伙伴相继出现，让小黑陷入了两难抉择，究竟何处才是真正的归属？",
            "watchdate": "2023-03-29",
            "url": "https://movie.douban.com/subject/26709258/"
        }
    ]
}
```

当sort 使用默认值 order无效 

### 搜索电影数据

说明 : 通过id值检索数据

**必选参数 :**

```
id: 电影id

```

**接口地址 :** `/api/moviewhether`

**调用例子 :** `/api/moviewhether?id=35337634`

返回示例

```
{
    "code": 200,
    "whether": 1,
    "data": {
        "id": 10,
        "movieid": 35368245,
        "name": "夏日幽灵 サマーゴースト",
        "imgurl": "https://img2.doubanio.com/view/photo/s_ratio_poster/public/p2633564542.jpg",
        "grade": "8.1",
        "website": "http://summerghost.jp",
        "showdate": "2021-11-12(日本)",
        "movietime": "40分钟",
        "content": "“知道夏日幻魂吗？”通过网络相识的高中生，友也、葵、凉。据称，都市传说中被称作“夏日幻魂”的是一位年轻女性的幽灵，只要燃放烟花她就会现身。无法迈向心仪人生的“友也”寻找不到容身之处的\"葵\"美好未来不翼而飞的\"凉\"他们分别有着必须见到夏日幻魂的理由。在生与死交织的夏夜，三人各自的思虑会迈向何方呢——",
        "watchdate": "2023-04-25",
        "type": "动画 短片 ",
        "ifmovie": 1,
        "url": "https://movie.douban.com/subject/35368245/"
    }
}
```

### 获取图书数据

说明 : 已观看的图书数据

**必选参数 :**

```
type:类型

sort:排序

order:排序方式

```

**接口地址 :** `/api//booklist`

**调用例子 :** `/api/booklist?type=1200&sort=-1&order=0`

`type`取值:

```
1200:默认值 全部
1210:中国
1220:日本
1230:美国
1240:韩国
1250:英国
1260:法国
```

`sort`取值:

```
-1:默认值 不排序 会随机输出
0:观看时间
1:上架时间
2:评分
3:页数
4:价格
```

`order`取值:

```
0:升序 默认值
1:降序
```

返回示例

```
{
    "code": 200,
    "data": [
        {
            "id": 5,
            "bookid": 24745899,
            "name": "左岸",
            "imgurl": "https://img1.doubanio.com/view/subject/s/public/s26839099.jpg",
            "grade": "7.8",
            "author": "(日)江国香织",
            "publishing": "重庆出版社",
            "showdate": "2013-9-1",
            "pagecount": 580,
            "content": "要去更远的地方啊，茉莉。不能老待在同一个地方，不是不行，而是因为谁都无法停留。",
            "watchdate": "2023-08-03T16:00:00.000Z",
            "pricing": "39.80元",
            "url": "https://book.douban.com/subject/24745899/"
        },
        {
            "id": 6,
            "bookid": 24745897,
            "name": "右岸",
            "imgurl": "https://img2.doubanio.com/view/subject/s/public/s26839103.jpg",
            "grade": "7.6",
            "author": "(日)辻仁成",
            "publishing": "重庆出版社",
            "showdate": "2013-9-1",
            "pagecount": 520,
            "content": "“人的一生也是要拼命才好玩，任何事情都必须像个笨蛋一样认真看待，否则就毫无意义。”\n        “人为什么会出生在这个世上，说穿了不就是为了拼命吗？” ",
            "watchdate": "2023-08-12T16:00:00.000Z",
            "pricing": "39.00",
            "url": ""
        },
    ]
}

```

### 搜索图书数据

说明 : 通过id值检索数据

**必选参数 :**

```
id: 图书id

```

**接口地址 :** `/api/bookwhether`

**调用例子 :** `/api/moviewhether?id=30245830`

返回示例

```
{
    "code": 200,
    "whether": 1,
    "data": {
        "id": 1,
        "bookid": 30245830,
        "name": "从前我死去的家",
        "imgurl": "https://img2.doubanio.com/view/subject/s/public/s30014642.jpg",
        "grade": "7.8",
        "author": "[日]东野圭吾",
        "publishing": "北京十月文艺出版社",
        "showdate": "2018-8",
        "pagecount": 256,
        "content": "大约有45%的施虐母亲自己也有过被虐待的经历，即便没有遭到虐待，大部分人的童年都经历过父亲消失、母亲生病不在家等等各种形式精神上的寂寞空虚。也就是说，她们都缺少爱。 ",
        "watchdate": "2023-07-21",
        "pricing": "49.50元",
        "url": "https://book.douban.com/subject/30245830/"
    }
}
```

### 获取相册封面

说明 : 相册单封面文字

**无参数**

**接口地址 :** `/api/photoshow`

**调用例子 :** `/api/photoshow`

返回示例

```
{
    "code": 200,
    "data": [
        {
            "id": 11,
            "photoid": "1024152",
            "pageid": "15201",
            "title": "坐牢",
            "tag": "珠海市档案馆",
            "date": "2023-05-15",
            "type": null
        },
        {
            "id": 1,
            "photoid": "1212538",
            "pageid": "37901",
            "title": "梵高",
            "tag": "抖音",
            "date": "2023-02-24",
            "type": null
        },
    ]
}
```


### 获取相册合集

说明 : 通过page检索相册集合

**必选参数 :**  

`page`: 相册集合唯一标识

**可选参数 :**


**接口地址 :** `/api/photo`

**调用例子 :** `/api/photo?page=15201`

返回示例

```
{
    "code": 200,
    "data": {
        "id": 7,
        "photoid": "5412253",
        "pageid": "33027",
        "title": "国庆",
        "tag": "珠海市岱山路",
        "date": "2022-10-03",
        "type": null,
        "list": [
            "5412253",
            "3352004",
            "6214781",
            "5463211",
            "9312457",
            "6543287",
            "4512351",
            "8747516"
        ]
    }
}
```

### 获取豆瓣电影数据

说明 : 通过id值获取豆瓣电影相关信息

**必选参数 :**

`id`: 豆瓣id 值

**接口地址 :** `/api/doubanmovie?`

**调用例子 :** `/api/doubanmovie?id=35593344`

返回示例

```
{
    "code": 200,
    "data": {
        "url": "https://movie.douban.com/subject/35593344",
        "movieid": "35593344",
        "name": "奥本海默 Oppenheimer",
        "imgurl": "https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2897210097.jpg",
        "grade": "8.8",
        "website": "http://www.oppenheimermovie.com",
        "showdate": "2023-08-30(中国大陆)2023-07-21(美国)",
        "movietime": "180分钟",
        "content": "当我们为权力金钱焦虑、兴奋与愤怒时，却根本想象不到“他们”在谈论着怎样更重要的事情。随着战争阴云笼罩世界上空，各国紧锣密鼓抓紧军事竞赛。为了抢占先机，美国陆军中将莱斯利·格罗夫斯（马特·达蒙MattDamon饰）找到量子力学与核物理学领域的扛鼎人物罗伯特·奥本海默（基里安·墨菲CillianMurphy饰），力荐其担任曼哈顿计划的首席科学家以及洛斯阿拉莫斯国家实验室的总负责人。经过两年争分夺秒的研发，硕大的蘑菇云终于在荒原的上空腾起，也宣告着绞肉机一般的二战即将落下帷幕。奥本海默有如将火种带到人间的普罗米修斯，可是对人性的参悟和对未来的担忧迫使他走向与政府相悖的道路。更可悲的是，凡人钟情的物欲也将一世天才裹挟至炼狱之中，永世燃烧……",
        "ifmovie": 1,
        "type": "剧情 传记 历史 ",
        "watchdate": "2023-09-08",
        "alsolikes": [
            {
                "title": "极速车王",
                "url": "https://movie.douban.com/subject/6538866/?from=subject-page",
                "imgurl": "https://img2.doubanio.com/view/photo/s_ratio_poster/public/p2896589511.jpg"
            },
            {
                "title": "拿破仑",
                "url": "https://movie.douban.com/subject/35224919/?from=subject-page",
                "imgurl": "https://img2.doubanio.com/view/photo/s_ratio_poster/public/p2895412552.jpg"
            },
            {
                "title": "芝加哥七君子审判",
                "url": "https://movie.douban.com/subject/2609258/?from=subject-page",
                "imgurl": "https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2620161520.jpg"
            },
            {
                "title": "末代皇帝",
                "url": "https://movie.douban.com/subject/1293172/?from=subject-page",
                "imgurl": "https://img2.doubanio.com/view/photo/s_ratio_poster/public/p452089833.jpg"
            },
            {
                "title": "至暗时刻",
                "url": "https://movie.douban.com/subject/26761416/?from=subject-page",
                "imgurl": "https://img2.doubanio.com/view/photo/s_ratio_poster/public/p2504277551.jpg"
            },
            {
                "title": "教宗的承继",
                "url": "https://movie.douban.com/subject/27132510/?from=subject-page",
                "imgurl": "https://img2.doubanio.com/view/photo/s_ratio_poster/public/p2567299222.jpg"
            },
            {
                "title": "社交网络",
                "url": "https://movie.douban.com/subject/3205624/?from=subject-page",
                "imgurl": "https://img9.doubanio.com/view/photo/s_ratio_poster/public/p2553756806.jpg"
            },
            {
                "title": "华盛顿邮报",
                "url": "https://movie.douban.com/subject/26990609/?from=subject-page",
                "imgurl": "https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2512869017.jpg"
            }
        ]
    }
}
```

### 获取豆瓣电影图片

说明 : 通过豆瓣电影id值获取图片地址集合

**必选参数 :** `id` : 豆瓣电影id值

**接口地址 :** `/api/doubanmovieimg`

**调用例子 :** `/api/doubanmovieimg?id=35593344&start=2`

返回示例

```
{
    "code": 200,
    "data": {
        "current": 2,
        "count": " 687",
        "pagecount": 23,
        "list": [
            "https://img1.doubanio.com/view/photo/l/public/p2897212830.jpg",
            "https://img9.doubanio.com/view/photo/l/public/p2897212536.jpg",
            "https://img2.doubanio.com/view/photo/l/public/p2897212833.jpg",
            "https://img2.doubanio.com/view/photo/l/public/p2897213021.jpg",
            "https://img1.doubanio.com/view/photo/l/public/p2897214127.jpg",
            "https://img2.doubanio.com/view/photo/l/public/p2893532563.jpg"
        ]
    }
}

```

### 获取豆瓣图书数据

说明 : 通过id值获取豆瓣图书相关信息数据

**必选参数 :**

`id`:豆瓣id 值

**接口地址 :** `/api/doubanbook`

**调用例子 :** `/api/doubanbook?id=3016386`

返回示例

```
{
    "code": 200,
    "data": {
        "bookid": "30163860",
        "name": "绿毛水怪",
        "url": "https://book.douban.com/subject/30163860",
        "imgurl": "https://img1.doubanio.com/view/subject/s/public/s29774838.jpg",
        "author": "王小波",
        "publishing": "北京十月文艺出版社",
        "pricing": "39.00元",
        "content": "我说：“妖妖，你看那水银灯的灯光像什么？大团的蒲公英浮在街道的河流上，吞吐着柔软的针一样的光。”妖妖说：“好，那么我们在人行道上走呢？”我抬头看看路灯，它把昏黄的灯光隔着蒙蒙的雾气一直投向地面。我说：“我们好像在池塘的水底。从一个月亮走向另一个月亮。”",
        "pagecount": "181",
        "showdate": "2018-6",
        "grade": "9.1",
        "alsolikes": [
            {
                "title": "红拂夜奔",
                "url": "https://book.douban.com/subject/27077957/",
                "imgurl": "https://img9.doubanio.com/view/subject/s/public/s33953395.jpg"
            },
            {
                "title": "万寿寺",
                "url": "https://book.douban.com/subject/27143188/",
                "imgurl": "https://img1.doubanio.com/view/subject/s/public/s29609540.jpg"
            },
            {
                "title": "白银时代",
                "url": "https://book.douban.com/subject/27116320/",
                "imgurl": "https://img9.doubanio.com/view/subject/s/public/s29623604.jpg"
            },
            {
                "title": "黑铁时代",
                "url": "https://book.douban.com/subject/30296222/",
                "imgurl": "https://img1.doubanio.com/view/subject/s/public/s29842357.jpg"
            },
            {
                "title": "爱你就像爱生命",
                "url": "https://book.douban.com/subject/27111096/",
                "imgurl": "https://img1.doubanio.com/view/subject/s/public/s29520509.jpg"
            },
            {
                "title": "一只特立独行的猪",
                "url": "https://book.douban.com/subject/27013708/",
                "imgurl": "https://img2.doubanio.com/view/subject/s/public/s29419061.jpg"
            },
            {
                "title": "沉默的大多数",
                "url": "https://book.douban.com/subject/27013716/",
                "imgurl": "https://img1.doubanio.com/view/subject/s/public/s29419068.jpg"
            },
            {
                "title": "我的精神家园",
                "url": "https://book.douban.com/subject/27156834/",
                "imgurl": "https://img2.doubanio.com/view/subject/s/public/s29609541.jpg"
            }
        ]
    }
}
```


### 添加电影信息到数据库

说明 : 配合获取豆瓣电影信息接口将数据储存到数据库中

注意 : 使用post提交
**必选参数 :**

```
movieid:豆瓣电影id值

name:电影标题

imgurl:图片地址

grade:评分

website:官方网站地址

showdate:上映时间

movietime:电影时长

content:电影描述信息

type:电影类型

ifmovie:是否为电影或剧集

url:豆瓣对应的电影url

watchdate:观看日期

```

**接口地址 :** `/api/addmovie`

返回示例

```
{
    code:200,
    msg:success
}
```

### 添加图书信息到数据库

说明 : 配合获取豆瓣电影信息接口将数据储存到数据库中
注意 : 使用post提交

**必选参数 :** 

```
bookid:豆瓣图书id值

name:图书标题

imgurl:图片地址

grade:评分

author:作者

publishing:出版社

showdate:上架时间

pagecount:页数

content:原文摘录

pricing:价格

url:豆瓣对应的图书url

watchdate:观看日期 默认值今天

```

**接口地址 :** `/user/addbook`

**调用例子 :** `/user/addbook`

返回示例

```
{
    code:200,
    msg:success
}
```

## 关于此文档

此文档由 [docsify](https://github.com/QingWei-Li/docsify/) 生成 docsify 是一个动态生成文档网站的工具。不同于 GitBook、Hexo 的地方是它不会生成将 .md 转成 .html 文件，所有转换工作都是在运行时进行。
