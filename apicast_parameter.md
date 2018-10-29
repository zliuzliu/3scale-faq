## APIcast 参数

APIcast v2有许多参数可配置为环境变量，可修改网关的行为。 以下提供了这些参数的说明。

请注意，在使用OpenShift部署APIcast v2时，部分参数是可以配置为OpenShift的模板参数的。后者可以直接在模板中查阅。


环境变量
APICAST_BACKEND_CACHE_HANDLER
值: strict | resilient
默认值: strict
不推荐使用: 改为使用缓存策略

定义后端不可用时授权缓存的行为方式。Strict将在后端不可用时删除缓存的应用程序。Resilient只有在获得后端授权后才会这样做。

APICAST_CONFIGURATION_CACHE
值: 数字
默认值: 0

指定配置将存储在缓存中的时间长度（以秒为单位）。可以采用以下值：

0：禁用缓存。不存储配置。与APICAST_CONFIGURATION_LOADER=boot模式不兼容。当APICAST_CONFIGURATION_LOADER=lazy时，APIcast将在每个请求时重新加载配置。
正数（> 0）：指定配置重新加载之间的间隔（以秒为单位）。例如，当使用API​​CAST_CONFIGURATION_CACHE = 300和APICAST_CONFIGURATION_CACHE = boot启动APIcast时，它将在启动时加载配置，并将每5分钟（300秒）重新加载一次。
负数（<0）：禁用重新加载。一旦存储，缓存条目将永远不会从缓存中删除，并且永远不会重新加载配置。

APICAST_CONFIGURATION_LOADER
值: boot | lazy
默认值: lazy

定义如何加载配置。在boot模式下，APIcast将在网关启动时向API管理器请求配置。在lazy模式下，APIcast将按需在每个传入的请求时加载配置（当APICAST_CONFIGURATION_CACHE设置为0时可以确保每个请求都完全刷新配置）。

APICAST_CUSTOM_CONFIG
不推荐使用: 改为使用策略。

定义覆盖现有APIcast逻辑的自定义逻辑的Lua模块的名称。

APICAST_ENVIRONMENT
默认:
值: string[:]
示例: production:cloud-hosted

APIcast会加载以冒号（:)分隔的环境（或路径）。它可以在CLI上代替-e或--- environment参数，例如作为默认环境存储在容器映像中。 CLI上传递的任何值都将覆盖此变量。

APICAST_LOG_FILE
默认值: stderr

定义存储OpenResty错误日志的文件。它由bin/apicast中的error_log指令使用。有关更多信息，请参阅NGINX文档。文件路径可以是绝对路径，也可以相对路径（默认为apicast）

APICAST_LOG_LEVEL
值: debug | info | notice | warn | error | crit | alert | emerg
默认值: warn

指定OpenResty日志的日志级别。

APICAST_ACCESS_LOG_FILE
默认值: stdout

定义将存储访问日志的文件。

APICAST_OIDC_LOG_LEVEL
值: debug | info | notice | warn | error | crit | alert | emerg
默认值: err

允许设置与OpenID Connect集成相关的日志的日志级别

APICAST_MANAGEMENT_API
值:

disabled: 完全禁用，只侦听端口
status: 仅为运行状况检查启用/status/endpoints
debug: 完整的API已打开
Management API功能强大，可以控制APIcast的配置。您应该仅为调试目的启用调试级别。

APICAST_MODULE
默认值: apicast
不推荐使用：改为使用策略。

Custom modules can override the functionality of the default apicast.lua module. 
指定实现API网关逻辑的主Lua模块的名称。自定义模块可以覆盖默认apicast.lua模块的功能。查看如何使用模块的示例。

APICAST_OAUTH_TOKENS_TTL
值: 数字
默认值: 604800

配置为使用OAuth进行身份验证时，此参数指定创建的令牌的TTL（以秒为单位）。

APICAST_PATH_ROUTING
值:

true或1表示true
false，0或为空表示false
When this parameter is set to true, the gateway will use path-based routing in addition to the default host-based routing. The API request will be routed to the first service that has a matching mapping rule, from the list of services for which the value of the Host header of the request matches the Public Base URL.
当此参数设置为true时，除了默认的基于主机的路由之外，网关还将使用基于路径的路由。 API请求将从请求的Host头的值与Public Base URL匹配的服务列表路由到具有匹配映射规则的第一个服务。

APICAST_POLICY_LOAD_PATH
默认值: APICAST_DIR/policies
值:: string[:]
示例: ~/apicast/policies:$PWD/policies

Double colon (:) separated list of paths where APIcast should look for policies. It can be used to first load policies from a development directory or to load examples.
双冒号（:)分隔的路径列表，其中APIcast应查找策略。它可用于首先从开发目录加载策略或加载示例。

APICAST_PROXY_HTTPS_CERTIFICATE_KEY
默认值:
值: string
示例: /home/apicast/my_certificate.key

客户端SSL证书密钥的路径。

APICAST_PROXY_HTTPS_CERTIFICATE
默认值:
值: string
示例: /home/apicast/my_certificate.crt

The path to the client 
