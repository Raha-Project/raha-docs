# نرم‌افزار Raha-Xray

این نرم‌افزار یک فراهم کننده خدمات api است که ویژگی‌های‌‌ لازم برای استفاده‌های متفاوت از [xray-core](https://github.com/XTLS/Xray-core) را به شکل ساده فراهم کرده است.

# روش کار
برای ایجاد یک کاربر که توانایی اتصال به xray-core را داشته باشد مراحل زیر باید طی شود:
1. افزودن یک config
2. افزودن inbound با تنظیم config.id برای استفاده از config افزوده شده
3. افزودن یک clinet با تنظیم client_inbound برای استفاده از inbound افزوده شده.

## امکانات ویژه
1. سرویس‌های‌‌ متنوع برای کار با xray-core ایجاد کنید
2. با ایجاد کاربران، آن‌ها را به چندین سرویس همزمان متصل کنید
3. ترافیک کاربران به شکل خودکار مدیریت می‌شود و می‌توان مدل‌های‌‌ مختلف محدودیت ترافیک/زمان را اعمال کرد
4. به صورت یک ویژگی قابل حذف/اضافه ترافیک inbound, outbound, client را به شکل اطلاعات مناسب برای طراحی نمودار دریافت کرد
5. گزارش‌های‌‌ کامل جهت بررسی سرور را دریافت کرد
6. برای استفاده در سرور‌های‌‌ کوچک و با استفاده کم می‌توان از دیتابیس SQLite و برای استفاده حرفه‌ای می‌توان از MySQL استفاده کرد.


# روش نصب
مراجعه کنید به [روش نصب](https://github.com/Raha-Project/Raha/blob/main/README-FA.md#%D8%B1%D9%88%D8%B4-%D9%87%D8%A7%DB%8C-%D9%86%D8%B5%D8%A8)

# تنظیمات
تنظیمات اولیه این نرم‌افزار شامل دو فایل است که در صورت عدم وجود، به صورت داده‌های‌‌ پیش فرض ایجاد خواهند شد.
1. فایل `raha-xray.json` : تنظیمات نرم‌افزار در این بخش وارد می‌شود
2. فایل `xrayDefault.json` : تنظیمات پایه `xray-core` که نرم‌افزار برای اعمال تغییرات استفاده می‌کند.

## نکات مهم
* این فایل‌ها در پوشه اصلی نرم‌افزار ایجاد خواهند شد
(/usr/local/raha-xray/)
* در زمان نصب و یا بروزرسانی نرم‌افزار برای تغییرات در فایل `raha-xray.conf` از شما سوال پرسیده می‌شود. همچنین می‌توانید از دستور `raha-xray` در محیط متنی و وارد کردن شماره ۴ این فایل را تنیظم کنید.
* این فایل‌ها به شکل دستی هم قابل تغییر هستند. ولی پیشنهاد می‌شود از مدل‌های‌‌ اتوماتیک استفاده کنید
* این تنظیمات از طریق api نیز برای تغییرات در دسترس شما هستند

# کار با api
برای استفاده از این API می‌توانید از ابزار‌های‌‌ مخصوص و یا نرم‌افزار خود استفاده کنید.
این نرم‌افزار از طریق سرویس وب با پورت و آدرس مشخص شده در تنظیمات، قابل دسترسی خواهد بود.
* بهتر است با ارائه SSL در تنظیمات از امنیت اتصال خود مطمئن شوید.

## امنیت و احراز هویت
برای ارسال درخواست‌های‌‌ خود به این api میبایست یک توکن از طریق دستور `raha-xray` در محیط متنی لینوکس و گزینه‌های‌‌ ۵-۸ اقدام کنید.
> در زمان نصب همیشه یک توکن پیش فرض ایجاد خواهد شد که می‌توانید از آن استفاده کنید.

در تمامی درخواست‌های‌‌ شما میبایست این توکن در سربرگ وب (HTTP Header) با نام `X-Token` موجود باشد.

## شرح متد ها
در این بخش ابتدا مسیر‌های‌‌ اصلی و هدف آنها توضیح داده می‌شود:

Base path : /api
> مسیر پایه در فایل تنظیمات `raha-xra.conf` قابل تغییر است.

| مسیر | هدف | xray-core API |
| :------------ | --------------------------------------------------- | :----------------: |
| `/configs`    | مدل‌های‌‌ مختلف سرویس برای استفاده در ورودی‌ها | :heavy_check_mark: |
| `/inbounds`   | ورودی‌ها                                           | :heavy_check_mark: |
| `/clients`    | کاربران                                            | :heavy_check_mark: |
| `/outbounds`  | خروجی‌ها                                           | :heavy_check_mark: |
| `/rules`      | قوانین مسیریابی                                   |                    |
| `/server`     | اطلاعات سرور                                        |                    |
| `/settings`   | تنظیمات                                             |                    |

* اعمال تغییرات در مسیرهایی که تغییرات خود را از طریق api به xray-core اعمال می‌کنند، بدون ریستارت شدن xray-core  و در نتیجه بدون قطع شدن همه کاربران خواهد بود.

### راهنمای متدهای مسیر configs

<details>
  <summary>برای نمایش شرح کلیک کنید</summary>

مدل تعریف شده
```go
type Config struct {
	Id             uint     `json:"id" form:"id" gorm:"primaryKey;autoIncrement"`
	Protocol       Protocol `json:"protocol" form:"protocol"`
	Settings       string   `json:"settings" form:"settings"`
	StreamSettings string   `json:"streamSettings" form:"streamSettings"`
	Sniffing       string   `json:"sniffing" form:"sniffing"`
	ClientSettings string   `json:"clientSettings" form:"clientSettings"`
}
```
**API methods:**
Base: /api/configs

| Method | Path                            | Action                                    | Request Body |
| :----: | ------------------------------- | ----------------------------------------- | ------------ |
| `GET`  | `"/"`                           | Get all configs                           | -            |
| `GET`  | `"/get/:id"`                    | Get a config with config.id               | -            |
| `POST` | `"/save"`                       | Add/Edit a config                         | [JSON](#%D8%B4%D8%B1%D8%AD-apiconfigssave)     |
| `POST` | `"/del/:id"`                    | Delete a config                           | -            |

##### نمونه json برای ارسال در /save:
```json
{
    "id": 1,
    "protocol": "vless",
    "settings": "{\"decryption\":\"none\",\"fallbacks\": []}",
    "streamSettings": "{\"network\": \"tcp\",\"security\": \"none\"}",
    "sniffing": "{\"destOverride\": [\"http\",\"tls\",\"quic\"],\"enabled\": true}",
    "clientSettings": ""
}
```
##### شرح /api/configs/save
| پارمتر | نوع | الزامی | توضیح |
| :---------------- | :----: | :--: | ------------------------------------- |
| `id`              | uint   | No   | در صورت عدم وجود، رکورد جدیدی ایجاد و در صورت وجود، رکورد ذکر شده ویرایش می‌شود |
| `protocol`        | string | Yes  | Inbound protocol                      |
| `settings`        | string | Yes  | تنظیمات پروتکل، بدون بخش کاربران      |
| `streamSettings`  | string | Yes  | تنظیمات [stream](https://xtls.github.io/en/config/transport.html#streamsettingsobject) |
| `clientSettings`  | string | No   | تنظیماتی که برای لینک کاربران نیاز دارید |

</details>

### راهنمای متدهای مسیر inbounds
<details>
  <summary>برای نمایش شرح کلیک کنید</summary>

مدل تعریف شده
```go
type Inbound struct {
	Id     uint   `json:"id" form:"id" gorm:"primaryKey;autoIncrement"`
	Name   string `json:"name" form:"name"`
	Enable bool   `json:"enable" form:"enable" gorm:"default:true"`

	// config part
	Listen   string `json:"listen" form:"listen"`
	Port     uint   `json:"port" form:"port"`
	ConfigId uint   `gorm:"not null" json:"configId" form:"configId"`
	Config   Config `gorm:"foreignKey:ConfigId;references:Id" json:"config"`
	Tag      string `gorm:"unique" json:"tag" form:"tag"`

	// clients part
	ClientInbounds []ClientInbound `gorm:"foreignKey:InboundId;references:Id" json:"clients"`
}
```
**API methods:**
Base: /api/inbounds

| Method | Path                            | Action                                    | Request Body |
| :----: | ------------------------------- | ----------------------------------------- | ------------ |
| `GET`  | `"/"`                           | Get all inbounds                          | -            |
| `GET`  | `"/get/:id"`                    | Get an inbound with inbound.id            | -            |
| `POST` | `"/save"`                       | Add/Edit an inbound                       | [JSON](#%D8%B4%D8%B1%D8%AD-apiinboundssave)     |
| `POST` | `"/del/:id"`                    | Delete an inbound                         | -            |
| `GET`  | `"/traffics/:tag"`              | Get an inbound's traffics ( if enabled )  | -            |

##### نمونه json برای ارسال در /save:
```json
{
    "id": 2,
    "name": "inbound-2",
    "enable": true,
    "listen": "",
    "port": 443,
    "configId": 1,
    "tag": "in-2"
}
```
##### شرح /api/inbounds/save
| پارمتر | نوع | الزامی | توضیح |
| :---------------- | :----: | :--: | ------------------------------------- |
| `id`              | uint   | No   | در صورت عدم وجود، رکورد جدیدی ایجاد و در صورت وجود، رکورد ذکر شده ویرایش می‌شود |
| `name`            | string | No   | اسم ورودی                             |
| `enable`          | bool   | Yes  | فعال/ غیرفعال بودن                    |
| `listen`          | string | No   | آدرس IP که ورودی به آن گوش می‌دهد      |
| `port`            | uint   | Yes  | پورتی که ورودی به آن گوش می‌دهد        |
| `configId`        | uint   | Yes  | شماره ID که configs با آن معرفی شده   |
| `tag`             | string | Yes  | تگ اینباند ( باید یکتا باشد )         |

* در زمان دریافت inbound کاربران مربوطه نیز لیست خواهند شد. [مدل client_inbound](#%D9%85%D8%AF%D9%84-client_inbounds). همچنین اطلاعات Config نیز قابل مشاهده است.

</details>

### راهنمای متدهای مسیر clients
<details>
  <summary>برای نمایش شرح کلیک کنید</summary>

مدل تعریف شده
```go
type Client struct {
	Id     uint   `json:"id" form:"id" gorm:"primaryKey;autoIncrement"`
	Name   string `json:"name" form:"name" gorm:"unique"`
	Enable bool   `json:"enable" form:"enable" gorm:"default:true"`
	Quota  uint64 `json:"quota" form:"quota" gorm:"default:0"`
	Expiry uint64 `json:"expiry" form:"expiry" gorm:"default:0"`
	Reset  uint   `json:"reset" from:"reset" gorm:"default:0"`
	Once   uint   `json:"once" from:"once" gorm:"default:0"`
	Up     uint64 `json:"up" form:"up" gorm:"default:0"`
	Down   uint64 `json:"down" form:"down" gorm:"default:0"`
	Remark string `json:"remark" form:"remark"`

	// inbounds part
	ClientInbounds []ClientInbound `gorm:"foreignKey:ClientId;references:Id" json:"inbounds"`
}
```
**API methods:**
Base: /api/clients

| Method | Path                            | Action                                    | Request Body |
| :----: | ------------------------------- | ----------------------------------------- | ------------ |
| `GET`  | `"/"`                           | Get all clients                           | -            |
| `GET`  | `"/get/:id"`                    | Get a client with client.id               | -            |
| `POST` | `"/add"`                        | Add client(s)                             | [JSON](#%D8%B4%D8%B1%D8%AD-apiclientsadd) |
| `POST` | `"/update"`                     | Edit a client                             | [JSON](#%D8%B4%D8%B1%D8%AD-apiclientsupdate) |
| `POST` | `"/inbounds/:id"`               | Edit inbounds of a client with client.id  | [JSON](#%D8%B4%D8%B1%D8%AD-apiclientsinbounds) |
| `POST` | `"/del/:id"`                    | Delete a client                           | -            |
| `POST` | `"/onlines"`                    | Get the list of last online users         | -            |
| `GET`  | `"/traffics/:tag"`              | Get traffics ( if enabled )               | -            |

##### نمونه json برای ارسال در /add:
```json
[
    {
        "name": "client1",
        "enable": true,
        "quota": 0,
        "expiry": 0,
        "reset": 0,
        "once": 0,
        "up": 0,
        "down": 0,
        "remark": "exampleRemark1",
        "inbounds": [
            {
                "inboundId": 1,
                "config": "{\n  \"id\": \"62f65b16-b6d3-48af-9d13-8c200b002505\"\n}"
            },
            {
                "inboundId": 2,
                "config": "{\n  \"password\": \"fc8f3f8651bc\"\n}"
            }
        ]
    },
    {
        "name": "client2",
        "enable": true,
        "quota": 102400,
        "expiry": 0,
        "reset": 0,
        "once": 0,
        "up": 0,
        "down": 0,
        "remark": "Remark2",
        "inbounds": [
            {
                "inboundId": 2,
                "config": "{\n  \"password\": \"8c200b002505\"\n}"
            }
        ]
    }
]
```
##### شرح /api/clients/add
| پارمتر | نوع | الزامی | توضیح |
| :---------------- | :----: | :--: | ------------------------------------- |
| `name`            | string | Yes  | اسم کاربر (باید یکتا باشد)            |
| `enable`          | bool   | Yes  | فعال/ غیرفعال بودن                    |
| `quota`           | uint64 | No   | مقدار بایت حجم مجاز کاربر در دوره زمانی |
| `expiry`          | uint64 | No   | مقدار زمان به میلی ثانیه برای انقضای کاربر (unix epoch) |
| `reset`           | uint   | No   | روزهای شارژ در هر دوره زمانی          |
| `once`            | uint   | No   | روزهای شارژ اولین دوره پس از اولین اتصال |
| `up`              | uint64 | No   | مقدار آپلود کاربر به بایت             |
| `down`            | uint64 | No   | مقدار دانلود کاربر به بایت            |
| `remark`          | string | No   | نام مستعار در لینک‌ها                 |
| `inbounds`        | client_inbounds | No   | ورودی‌های‌‌ قابل استفاده کاریر |

##### نمونه json برای ارسال در /update:

برای بروزرسانی کاربر نیازی به ارسال همه فیلدها ندارید (نسخه 0.0.2 و بالاتر)

* نمونه ارسال همه فیلدها
```json
{
    "id": 1,
    "name": "Test1",
    "enable": true,
    "quota": 0,
    "expiry": 0,
    "reset": 0,
    "once": 0,
    "up": 0,
    "down": 0,
    "remark": "theFirstTest"
}
```
* نمونه برای ریست ترافیک کاربر
```json
{
    "id": 1,
    "up": 0,
    "down": 0
}
```

* نمونه برای غیرفعال کردن کاربر
```json
{
    "id": 1,
    "enable": false
}
```

##### شرح /api/clients/update
| پارمتر | نوع | الزامی | توضیح |
| :---------------- | :----: | :--: | ------------------------------------- |
| `id`              | uint   | Yes  |شناسه یکتای کاربر(ID) مورد نظر برای ویرایش|
| `name`            | string | No   | اسم کاربر (باید یکتا باشد)            |
| `enable`          | bool   | No  | فعال/ غیرفعال بودن                    |
| `quota`           | uint64 | No   | مقدار بایت حجم مجاز کاربر در دوره زمانی |
| `expiry`          | uint64 | No   | مقدار unix epoch به میلی ثانیه برای انقضای کاربر |
| `reset`           | uint   | No   | روزهای شارژ در هر دوره زمانی          |
| `once`            | uint   | No   | روزهای شارژ اولین دوره پس از اولین اتصال |
| `up`              | uint64 | No   | مقدار آپلود کاربر به بایت             |
| `down`            | uint64 | No   | مقدار دانلود کاربر به بایت            |
| `remark`          | string | No   | نام مستعار در لینک‌ها                 |

##### نمونه json برای ارسال در /inbounds:
```json
[
        {
          "id": 38,
          "inboundId": 1,
          "config": "{\"id\": \"fbcad46d-c9ab-40a3-a3cc-5d1ccf9269b7\"\n}"
        }
]
```

##### شرح /api/clients/inbounds
| پارمتر | نوع | الزامی | توضیح |
| :---------------- | :----: | :--: | ------------------------------------- |
| `id`              | uint   | No   | در صورت عدم وجود، رکورد جدیدی ایجاد و در صورت وجود، رکورد ذکر شده ویرایش می‌شود |
| `inboundId`       | uint   | Yes  | شماره ورودی (inbound_id)              |
| `config`          | string | Yes  | تنظیمات کاربر برای این ورودی          |

</details>

### مدل client_inbounds
<details>
  <summary>برای نمایش شرح کلیک کنید</summary>

```go
type ClientInbound struct {
	Id        uint   `json:"id" form:"id" gorm:"primaryKey;autoIncrement"`
	InboundId uint   `json:"inboundId" form:"inboundId"`
	ClientId  uint   `json:"clientId" form:"clientId"`
	Config    string `json:"config" form:"config"`
}
```
##### نمونه json دریافتی در inbounds و clients:
```json
[
        {
          "id": 38,
          "inboundId": 1,
          "clientId": 1,
          "config": "{\"id\": \"fbcad46d-c9ab-40a3-a3cc-5d1ccf9269b7\"\n}"
        }
]
```
</details>

### راهنمای متدهای مسیر outbounds

<details>
  <summary>برای نمایش شرح کلیک کنید</summary>

مدل تعریف شده
```go
type Outbound struct {
	Id             uint   `json:"id" form:"id" gorm:"primaryKey;autoIncrement"`
	SendThrough    string `json:"sendThrough" form:"sendThrough"`
	Protocol       string `json:"protocol" form:"protocol"`
	Settings       string `json:"settings" form:"settings"`
	Tag            string `gorm:"unique" json:"tag" form:"tag"`
	StreamSettings string `json:"streamSettings" form:"streamSettings"`
	ProxySettings  string `json:"proxySettings" form:"proxySettings"`
	Mux            string `json:"mux" form:"mux"`
}
```
**API methods:**
Base: /api/outbounds

| Method | Path                            | Action                                    | Request Body |
| :----: | ------------------------------- | ----------------------------------------- | ------------ |
| `GET`  | `"/"`                           | Get all outbounds                         | -            |
| `GET`  | `"/get/:id"`                    | Get an outbound with inbound.id           | -            |
| `POST` | `"/save"`                       | Add/Edit an outbound                      | [JSON](#%D8%B4%D8%B1%D8%AD-apioutboundssave)     |
| `POST` | `"/del/:id"`                    | Delete an outbound                        | -            |
| `GET`  | `"/traffics/:tag"`              | Get an outbound's traffics ( if enabled ) | -            |

##### نمونه json برای ارسال در /save:
```json
{
    "id": 1,
    "sendThrough": "",
    "protocol": "freedom",
    "settings": "{\"domainStrategy\": \"UseIPv6\"}",
    "tag": "direct-IPv6",
    "streamSettings": "",
    "proxySettings": "",
    "mux": ""
}
```
##### شرح /api/outbounds/save
| پارمتر | نوع | الزامی | توضیح |
| :---------------- | :----: | :--: | ------------------------------------- |
| `id`              | uint   | No   | در صورت عدم وجود، رکورد جدیدی ایجاد و در صورت وجود، رکورد ذکر شده ویرایش می‌شود |
| `sendThrough`     | string | No   | آدرس IP سرور که می‌خواهید ترافیک از این آدرس خارج شود |
| `protocol`        | string | Yes  | پروتکل خروجی                          |
| `settings`        | string | No   | تنظیمات                               |
| `tag`             | string | Yes  | تگ خروجی ( باید یکتا باشد )           |
| `streamSettings`  | string | No   | تنظیمات استریم                        |
| `proxySettings`   | string | No   | ارسال خروجی به یک خروجی دیگر با تگ    |
| `mux`             | string | No   | تنظیمات مولتیپلکس                     |

[توضیحات بیشتر](https://xtls.github.io/en/config/outbound.html)

</details>

### راهنمای متدهای مسیر rules
<details>
  <summary>برای نمایش شرح کلیک کنید</summary>

مدل تعریف شده
```go
type Rule struct {
	Id            uint   `json:"id" form:"id" gorm:"primaryKey;autoIncrement"`
	DomainMatcher string `json:"domainMatcher" form:"domainMatcher"`
	Type          string `json:"type" form:"type"`
	Domain        string `json:"domain" form:"domain"`
	Ip            string `json:"ip" form:"ip"`
	Port          string `json:"port" form:"port"`
	SourcePort    string `json:"sourcePort" form:"sourcePort"`
	Network       string `json:"network" form:"network"`
	Source        string `json:"source" form:"source"`
	User          string `json:"user" form:"user"`
	InboundTag    string `json:"inboundTag" form:"inboundTag"`
	Protocol      string `json:"protocol" form:"protocol"`
	Attrs         string `json:"attrs" form:"attrs"`
	OutboundTag   string `json:"outboundTag" form:"outboundTag"`
	BalancerTag   string `json:"balancerTag" form:"balancerTag"`
}
```
**API methods:**
Base: /api/rules

| Method | Path                            | Action                                    | Request Body |
| :----: | ------------------------------- | ----------------------------------------- | ------------ |
| `GET`  | `"/"`                           | Get all rules                             | -            |
| `GET`  | `"/get/:id"`                    | Get a rule with rule.id                   | -            |
| `POST` | `"/save"`                       | Add/Edit a rule                           | [JSON](#%D8%B4%D8%B1%D8%AD-apirulessave) |
| `POST` | `"/del/:id"`                    | Delete a rule                             | -            |

##### نمونه json برای ارسال در /save:
```json
{
    "id": 1,
    "domainMatcher": "hybrid",
    "type": "field",
    "domain": "[\"baidu.com\", \"qq.com\", \"geosite:cn\"]",
    "ip": "[\"::/0\"]",
    "port": "53,443,1000-2000",
    "sourcePort": "53,443,1000-2000",
    "network": "tcp",
    "source": "[\"10.0.0.1\"]",
    "user": "[\"love@xray.com\"]",
    "inboundTag": "[\"tag-vmess\"]",
    "protocol": "[\"http\", \"tls\", \"bittorrent\"]",
    "attrs": "{ \":method\": \"GET\" }",
    "outboundTag": "direct",
    "balancerTag": "balancer"
}
```
##### شرح /api/rules/save
| پارمتر | نوع | الزامی | توضیح |
| :---------------- | :----: | :--: | ------------------------------------- |
| `id`              | uint   | No   | در صورت عدم وجود، رکورد جدیدی ایجاد و در صورت وجود، رکورد ذکر شده ویرایش می‌شود |
| `domainMatcher`   | string | No   | الگوریتم تطبیق دامنه                  |
| `domain`          | string | No   | دامنه‌ها                              |
| `ip`              | string | No   | آدرس‌های‌‌ IP مقصد                      |
| `port`            | string | No   | پورت‌های‌‌ مقصد                         |
| `sourcePort`      | string | No   | پورت مبدا                             |
| `network`         | string | No   | شبکه استفاده شده (tcp/udp)            |
| `source`          | string | No   | آدرس‌های‌‌ IP مبدا                      |
| `user`            | string | No   | کاربر‌ها                              |
| `inboundTag`      | string | No   | تگ‌های‌‌ ورودی                          |
| `protocol`        | string | No   | پرتکل‌های‌‌ ارتباط                      |
| `attrs`           | string | No   | ویژگی‌های‌‌ سربرگ درخواست               |
| `outboundTag`     | string | Yes  | تگ خروجی                              |
| `balancerTag`     | string | No   | تگ LoadBalancer                       |

[توضیحات بیشتر](https://xtls.github.io/en/config/routing.html#ruleobject)

</details>

### راهنمای متدهای مسیر server
<details>
  <summary>برای نمایش شرح کلیک کنید</summary>

این متد برای دریافت و ارسال اطلاعات سرور قابل استفاده است. 

**API methods:**
Base: /api/server

| Method | Path                            | Action                                    | Request Body |
| :----: | ------------------------------- | ----------------------------------------- | ------------ |
| `POST` | `"/status"`                     | Get all statistics of server              | -            |
| `POST` | `"/getXrayVersion"`             | Get xray-core version                     | -            |
| `POST` | `"/setXrayVersion/:version"`    | Change the xray-core version              | -            |
| `POST` | `"/stopXrayService"`            | Stop xray-core service                    | -            |
| `POST` | `"/restartXrayService"`         | Restart xray-core service                 | -            |
| `POST` | `"/getConfigJson"`              | Download the configuration of xray-core   | -            |
| `POST` | `"/logs/:app/:count"`           | Get logs of services                      | -            |
| `POST` | `"/getNewX25519Cert"`           | Get reality keys                          | -            |

##### نمونه json دریافتی از /status:
```json
{
    "cpu": 2.676659528908924,
    "cpuCount": 4,
    "mem": {
        "current": 691990528,
        "total": 4123820032
    },
    "swap": {
        "current": 0,
        "total": 1073737728
    },
    "disk": {
        "current": 30789402624,
        "total": 62671097856
    },
    "xray": {
        "state": "running",
        "version": "1.8.4"
    },
    "uptime": 42755,
    "loads": [
        0.08,
        0.03,
        0
    ],
    "tcpCount": 7,
    "udpCount": 3,
    "netIO": {
        "up": 0,
        "down": 0
    },
    "netTraffic": {
        "sent": 21401,
        "recv": 51696
    },
    "appStats": {
        "threads": 10,
        "mem": 15045896
    },
    "xrayStats": {
        "NumGoroutine": 29,
        "NumGC": 572,
        "Alloc": 4047584,
        "TotalAlloc": 998091656,
        "Sys": 36017416,
        "Mallocs": 2650983,
        "Frees": 2637414,
        "LiveObjects": 13569,
        "PauseTotalNs": 1274427636,
        "Uptime": 42426
    },
    "hostInfo": {
        "hostname": "1681e5650ba4",
        "ipv4": "172.18.0.5/16 ",
        "ipv6": ""
    }
}
```

</details>

### راهنمای متدهای مسیر settings
<details>
  <summary>برای نمایش شرح کلیک کنید</summary>

این متد برای دریافت و ارسال تنظیمات نرم‌افزار قابل استفاده است. 

**API methods:**
Base: /api/settings

| Method | Path                            | Action                                    | Request Body |
| :----: | ------------------------------- | ----------------------------------------- | ------------ |
| `POST` | `"/getXrayDefault"`             | Get xray-core base configuration          | -            |
| `POST` | `"/setXrayDefault"`             | Change xray-core base configuration       | JSON         |
| `POST` | `"/getSettings"`                | Get App Configuration                     | -            |
| `POST` | `"/setSettings"`                | Update App Configuration                  | [JSON](#%D8%B4%D8%B1%D8%AD-apisettingssetsettings) |
| `POST` | `"/restartApp"`                 | Restart Raha-Xray                         | -            |

##### نمونه json برای ارسال در /setXrayDefault:
```json
{
  "log": {
    "loglevel": "warning",
    "access": "/dev/null"
  },
  "api": {
    "tag": "api",
    "services": ["HandlerService", "LoggerService", "StatsService"]
  },
  "inbounds": [
    {
      "tag": "api",
      "listen": "127.0.0.1",
      "port": 62789,
      "protocol": "dokodemo-door",
      "settings": {
        "address": "127.0.0.1"
      }
    }
  ],
  "outbounds": [
    {
      "tag": "direct",
      "protocol": "freedom",
      "settings": {}
    },
    {
      "tag": "blocked",
      "protocol": "blackhole",
      "settings": {}
    }
  ],
  "policy": {
    "levels": {
      "0": {
        "statsUserDownlink": true,
        "statsUserUplink": true
      }
    },
    "system": {
      "statsInboundDownlink": true,
      "statsInboundUplink": true,
      "statsOutboundUplink": true,
      "statsOutboundDownlink": true
    }
  },
  "routing": {
    "domainStrategy": "AsIs",
    "rules": [
      {
        "type": "field",
        "inboundTag": ["api"],
        "outboundTag": "api"
      },
      {
        "type": "field",
        "outboundTag": "blocked",
        "ip": ["geoip:private"]
      },
      {
        "type": "field",
        "outboundTag": "blocked",
        "protocol": ["bittorrent"]
      }
    ]
  },
  "stats": {}
}
```

##### نمونه json برای ارسال در /setSettings:
```json
{
    "listen": "",
    "domain": "",
    "port": 8080,
    "certFile": "",
    "keyFile": "",
    "basePath": "/api",
    "timeLocation": "Asia/Tehran",
    "dbType": "mysql",
    "dbAddr": "root:rahaXray@tcp(localhost:3306)",
    "trafficDays": 30
}
```
##### شرح /api/settings/setSettings
| پارمتر | نوع | الزامی | توضیح |
| :---------------- | :----: | :--: | ------------------------------------- |
| `listen`          | string | Yes  | آدرس IP برای سرویس api                |
| `domain`          | string | Yes  | دامنه api                             |
| `port`            | string | Yes  | پورت api                              |
| `certFile`        | string | Yes  | آدرس فایل گواهی دیجیتال               |
| `keyFile`         | string | Yes  | آدرس فایل کلید گواهی دیجیتال          |
| `basePath`        | string | Yes  | مسیر پیشفرض (default: /api)           |
| `timeLocation`    | string | Yes  | منطقه زمانی                           |
| `dbType`          | string | Yes  | نوع دیتابیس (SQLite/MySQL)            |
| `dbAddr`          | string | Yes  | آدرس دیتابیس                          |
| `trafficDays`     | string | Yes  | روزهای ذخیره اطلاعات مصرف              |

</details>
