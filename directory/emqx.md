## 需要配置文件
- 不带证书
![image](https://user-images.githubusercontent.com/15257651/164962587-ee76fb13-02d6-4c8b-898d-6e9c1fcb0f3d.png)
- 带证书
![image](https://user-images.githubusercontent.com/15257651/164965167-8e83779b-38b8-4821-8a46-e5269e1993e7.png)

## 配置文件内容

- emqx_auth_mnesia.conf

```
## Password hash.
##
## Value: plain | md5 | sha | sha256 | sha512
auth.mnesia.password_hash = sha256

##--------------------------------------------------------------------
## ClientId Authentication
##--------------------------------------------------------------------

## Examples
##auth.client.1.clientid = xxx
##auth.client.1.password = public
##auth.client.2.clientid = dev:devid
##auth.client.2.password = passwd2
##auth.client.3.clientid = app:appid
##auth.client.3.password = passwd3
##auth.client.4.clientid = client~!@#$%^&*()_+
##auth.client.4.password = passwd~!@#$%^&*()_+

##--------------------------------------------------------------------
## Username Authentication
##--------------------------------------------------------------------

## Examples:
auth.user.1.username = xxxx
auth.user.1.password = oooo
##auth.user.2.username = feng@emqtt.io
##auth.user.2.password = public
##auth.user.3.username = name~!@#$%^&*()_+
##auth.user.3.password = pwsswd~!@#$%^&*()_+

```
- acl.conf

```
%%--------------------------------------------------------------------
%% [ACL](https://docs.emqx.io/broker/v3/en/config.html)
%%
%% -type(who() :: all | binary() |
%%                {ipaddr, esockd_access:cidr()} |
%%                {client, binary()} |
%%                {user, binary()}).
%%
%% -type(access() :: subscribe | publish | pubsub).
%%
%% -type(topic() :: binary()).
%%
%% -type(rule() :: {allow, all} |
%%                 {allow, who(), access(), list(topic())} |
%%                 {deny, all} |
%%                 {deny, who(), access(), list(topic())}).
%%--------------------------------------------------------------------
{allow, all, subscribe, ["$SYS/brokers/+/clients/#"]}.

{allow, {user, "dashboard"}, subscribe, ["$SYS/#"]}.

{allow, {ipaddr, "127.0.0.1"}, pubsub, ["$SYS/#", "#"]}.

{deny, all, subscribe, ["$SYS/#", {eq, "#"}]}.

{allow, all}.
```
- loaded_plugins

```
{emqx_management,true}.
{emqx_dashboard,true}.
{emqx_modules,false}.
{emqx_recon,true}.
{emqx_retainer,true}.
{emqx_telemetry,true}.
{emqx_rule_engine,true}.
{emqx_bridge_mqtt,false}.
{emqx_auth_mnesia,true}.
```
- emqx.conf

```
## EMQ X Configuration 4.3

cluster.name = emqxcl
cluster.proto_dist = inet_tcp
cluster.discovery = manual
cluster.autoheal = on
cluster.autoclean = 5m

node.name = emqx@127.0.0.1

node.cookie = emqxsecretcookie

node.data_dir = data

node.global_gc_interval = 15m

node.crash_dump = log/crash.dump

node.dist_listen_min = 6369
node.dist_listen_max = 6369

node.backtrace_depth = 16

rpc.mode = async

rpc.async_batch_size = 256

rpc.port_discovery = stateless

rpc.connect_timeout = 5s

rpc.send_timeout = 5s

rpc.authentication_timeout = 5s

rpc.call_receive_timeout = 15s

rpc.socket_keepalive_idle = 900s

rpc.socket_keepalive_interval = 75s

rpc.socket_keepalive_count = 9

rpc.socket_sndbuf = 1MB

rpc.socket_recbuf = 1MB

rpc.socket_buffer = 1MB
log.to = file

log.level = warning

log.dir = log

log.file = emqx.log

log.rotation = on

log.rotation.size = 10MB

log.rotation.count = 5
allow_anonymous = false

acl_nomatch = allow

acl_file = etc/acl.conf

enable_acl_cache = on

acl_cache_max_size = 32
acl_cache_ttl = 1m

acl_deny_action = ignore

flapping_detect_policy = 30, 1m, 5m
mqtt.max_packet_size = 1MB

mqtt.max_clientid_len = 65535

mqtt.max_topic_levels = 0

mqtt.max_qos_allowed = 2

mqtt.max_topic_alias = 65535

mqtt.retain_available = true
mqtt.wildcard_subscription = true

mqtt.shared_subscription = true

mqtt.ignore_loop_deliver = false

mqtt.strict_mode = false

zone.external.idle_timeout = 15s

zone.external.enable_acl = on

zone.external.enable_ban = on

zone.external.enable_stats = on

zone.external.acl_deny_action = ignore

zone.external.force_gc_policy = 16000|16MB

zone.external.keepalive_backoff = 0.75

zone.external.max_subscriptions = 0

zone.external.upgrade_qos = off

zone.external.max_inflight = 32

zone.external.retry_interval = 30s

zone.external.max_awaiting_rel = 100

zone.external.await_rel_timeout = 300s

zone.external.session_expiry_interval = 2h

zone.external.max_mqueue_len = 1000

zone.external.mqueue_default_priority = highest

zone.external.mqueue_store_qos0 = true

zone.external.enable_flapping_detect = off

zone.external.use_username_as_clientid = false

zone.external.ignore_loop_deliver = false

zone.external.strict_mode = false

zone.internal.allow_anonymous = true

zone.internal.enable_stats = on

zone.internal.enable_acl = off

zone.internal.acl_deny_action = ignore

zone.internal.max_subscriptions = 0

zone.internal.max_inflight = 128

zone.internal.max_awaiting_rel = 1000

zone.internal.max_mqueue_len = 10000

zone.internal.mqueue_store_qos0 = true

zone.internal.enable_flapping_detect = off

zone.internal.ignore_loop_deliver = false

zone.internal.strict_mode = false

zone.internal.bypass_auth_plugins = true

listener.tcp.external = 0.0.0.0:1883

listener.tcp.external.acceptors = 8

listener.tcp.external.max_connections = 1024000

listener.tcp.external.max_conn_rate = 1000

listener.tcp.external.active_n = 100

listener.tcp.external.zone = external

listener.tcp.external.access.1 = allow all

listener.tcp.external.backlog = 1024

listener.tcp.external.send_timeout = 15s

listener.tcp.external.send_timeout_close = on

listener.tcp.external.nodelay = true

listener.tcp.external.reuseaddr = true

listener.tcp.internal = 127.0.0.1:11883

listener.tcp.internal.acceptors = 4

listener.tcp.internal.max_connections = 1024000

listener.tcp.internal.max_conn_rate = 1000

listener.tcp.internal.active_n = 1000

listener.tcp.internal.zone = internal

listener.tcp.internal.backlog = 512

listener.tcp.internal.send_timeout = 5s

listener.tcp.internal.send_timeout_close = on

listener.tcp.internal.recbuf = 64KB

listener.tcp.internal.sndbuf = 64KB

listener.tcp.internal.nodelay = false

listener.tcp.internal.reuseaddr = true

listener.ssl.external = 8883

listener.ssl.external.acceptors = 16

listener.ssl.external.max_connections = 102400

listener.ssl.external.max_conn_rate = 500

listener.ssl.external.active_n = 100

listener.ssl.external.zone = external

listener.ssl.external.access.1 = allow all

listener.ssl.external.handshake_timeout = 15s

listener.ssl.external.keyfile = etc/certs/key.pem

listener.ssl.external.certfile = etc/certs/cert.pem

listener.ssl.external.ciphers = TLS_AES_256_GCM_SHA384,TLS_AES_128_GCM_SHA256,TLS_CHACHA20_POLY1305_SHA256,TLS_AES_128_CCM_SHA256,TLS_AES_128_CCM_8_SHA256,ECDHE-ECDSA-AES256-GCM-SHA384,ECDHE-RSA-AES256-GCM-SHA384,ECDHE-ECDSA-AES256-SHA384,ECDHE-RSA-AES256-SHA384,ECDHE-ECDSA-DES-CBC3-SHA,ECDH-ECDSA-AES256-GCM-SHA384,ECDH-RSA-AES256-GCM-SHA384,ECDH-ECDSA-AES256-SHA384,ECDH-RSA-AES256-SHA384,DHE-DSS-AES256-GCM-SHA384,DHE-DSS-AES256-SHA256,AES256-GCM-SHA384,AES256-SHA256,ECDHE-ECDSA-AES128-GCM-SHA256,ECDHE-RSA-AES128-GCM-SHA256,ECDHE-ECDSA-AES128-SHA256,ECDHE-RSA-AES128-SHA256,ECDH-ECDSA-AES128-GCM-SHA256,ECDH-RSA-AES128-GCM-SHA256,ECDH-ECDSA-AES128-SHA256,ECDH-RSA-AES128-SHA256,DHE-DSS-AES128-GCM-SHA256,DHE-DSS-AES128-SHA256,AES128-GCM-SHA256,AES128-SHA256,ECDHE-ECDSA-AES256-SHA,ECDHE-RSA-AES256-SHA,DHE-DSS-AES256-SHA,ECDH-ECDSA-AES256-SHA,ECDH-RSA-AES256-SHA,AES256-SHA,ECDHE-ECDSA-AES128-SHA,ECDHE-RSA-AES128-SHA,DHE-DSS-AES128-SHA,ECDH-ECDSA-AES128-SHA,ECDH-RSA-AES128-SHA,AES128-SHA


listener.ssl.external.reuseaddr = true

listener.ws.external = 8083

listener.ws.external.mqtt_path = /mqtt

listener.ws.external.acceptors = 4

listener.ws.external.max_connections = 102400

listener.ws.external.max_conn_rate = 1000

listener.ws.external.active_n = 100

listener.ws.external.zone = external

listener.ws.external.access.1 = allow all

listener.ws.external.backlog = 1024

listener.ws.external.send_timeout = 15s

listener.ws.external.send_timeout_close = on

listener.ws.external.nodelay = true

listener.ws.external.mqtt_piggyback = multiple

listener.ws.external.check_origin_enable = false

listener.ws.external.allow_origin_absence = true

listener.ws.external.check_origins = http://localhost:18083, http://127.0.0.1:18083

listener.wss.external = 8084

listener.wss.external.mqtt_path = /mqtt

listener.wss.external.acceptors = 4

listener.wss.external.max_connections = 102400

listener.wss.external.max_conn_rate = 1000

listener.wss.external.active_n = 100

listener.wss.external.zone = external

listener.wss.external.access.1 = allow all

listener.wss.external.keyfile = etc/certs/key.pem

listener.wss.external.certfile = etc/certs/cert.pem

listener.wss.external.ciphers = TLS_AES_256_GCM_SHA384,TLS_AES_128_GCM_SHA256,TLS_CHACHA20_POLY1305_SHA256,TLS_AES_128_CCM_SHA256,TLS_AES_128_CCM_8_SHA256,ECDHE-ECDSA-AES256-GCM-SHA384,ECDHE-RSA-AES256-GCM-SHA384,ECDHE-ECDSA-AES256-SHA384,ECDHE-RSA-AES256-SHA384,ECDHE-ECDSA-DES-CBC3-SHA,ECDH-ECDSA-AES256-GCM-SHA384,ECDH-RSA-AES256-GCM-SHA384,ECDH-ECDSA-AES256-SHA384,ECDH-RSA-AES256-SHA384,DHE-DSS-AES256-GCM-SHA384,DHE-DSS-AES256-SHA256,AES256-GCM-SHA384,AES256-SHA256,ECDHE-ECDSA-AES128-GCM-SHA256,ECDHE-RSA-AES128-GCM-SHA256,ECDHE-ECDSA-AES128-SHA256,ECDHE-RSA-AES128-SHA256,ECDH-ECDSA-AES128-GCM-SHA256,ECDH-RSA-AES128-GCM-SHA256,ECDH-ECDSA-AES128-SHA256,ECDH-RSA-AES128-SHA256,DHE-DSS-AES128-GCM-SHA256,DHE-DSS-AES128-SHA256,AES128-GCM-SHA256,AES128-SHA256,ECDHE-ECDSA-AES256-SHA,ECDHE-RSA-AES256-SHA,DHE-DSS-AES256-SHA,ECDH-ECDSA-AES256-SHA,ECDH-RSA-AES256-SHA,AES256-SHA,ECDHE-ECDSA-AES128-SHA,ECDHE-RSA-AES128-SHA,DHE-DSS-AES128-SHA,ECDH-ECDSA-AES128-SHA,ECDH-RSA-AES128-SHA,AES128-SHA

listener.wss.external.backlog = 1024

listener.wss.external.send_timeout = 15s

listener.wss.external.send_timeout_close = on

listener.wss.external.mqtt_piggyback = multiple
listener.wss.external.check_origin_enable = false
listener.wss.external.allow_origin_absence = true
listener.wss.external.check_origins = https://localhost:8084, https://127.0.0.1:8084

modules.loaded_file = data/loaded_modules

module.presence.qos = 1

plugins.etc_dir = etc/plugins/

plugins.loaded_file = data/loaded_plugins

plugins.expand_plugins_dir = etc/plugins/

broker.sys_interval = 1m

broker.sys_heartbeat = 30s

broker.session_locking_strategy = quorum

broker.shared_subscription_strategy = random

broker.shared_dispatch_ack_enabled = false

broker.route_batch_clean = off

sysmon.long_gc = 0
sysmon.long_schedule = 240ms

sysmon.large_heap = 8MB

sysmon.busy_port = false

sysmon.busy_dist_port = true

os_mon.cpu_check_interval = 60s

os_mon.cpu_high_watermark = 80%

os_mon.cpu_low_watermark = 60%

os_mon.mem_check_interval = 60s

os_mon.sysmem_high_watermark = 70%

os_mon.procmem_high_watermark = 5%

vm_mon.check_interval = 30s

vm_mon.process_high_watermark = 80%

vm_mon.process_low_watermark = 60%

alarm.actions = log,publish
alarm.size_limit = 1000

alarm.validity_period = 24h

## CONFIG_SECTION_END=sys_mon ==================================================
```
