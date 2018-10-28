# 3scale-FAQ

(更新于2018年10月27日，当前最新版本为3scale v2.3）

- [**1. 红帽3scale对应的上游开源项目**](#upstream-project)
- [**2. 如何安装在物理机或虚拟机上安装API网关（APIcast）**](#install-apicast-rhel7)
- [**3. 3scale API网关的部署模式**](#deploy-mode)

1. 红帽3scale对应的上游开源项目

  https://github.com/3scale

2. 如何安装在物理机或虚拟机上安装API网关（APIcast）

以下为在RHEL v7.5上安装APIcast v3.2.1的步骤

* 切换至root用户

`sudo -i`

* 安装依赖包

`yum install -y yum-utils gcc git`

* 启用OpenResty repo (查看  https://openresty.org/en/linux-packages.html 的 RHEL 部分)

`yum-config-manager --add-repo https://openresty.org/package/rhel/openresty.repo`

* 启用 EPEL 包 (LuaRocks需要)

`yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm`

* 更新包

`yum upgrade -y`

* 安装 OpenResty (查看 https://openresty.org/en/linux-packages.html 的 RHEL 部分)

`yum install -y openresty-openssl`
`yum install -y \`
`     openresty \`
`     openresty-resty`

* Install LuaRocks

`yum install -y luarocks`

* 配置 Lua 环境

`mkdir -p /usr/share/lua/5.1/luarocks/`
`cat << EOF ``/usr/share/lua/5.1/luarocks/site_config.lua`
`local site_config = { }`

`local openresty = [[/usr/local/openresty]]`
local luajit = openresty .. [[/luajit]]

site_config.LUAROCKS_SYSCONFIG=openresty .. [[/config-5.1.lua]]
site_config.LUA_INTERPRETER=[[resty]]
site_config.LUA_DIR_SET=false
site_config.LUAROCKS_PREFIX= [[/usr]]
site_config.LUA_INCDIR= luajit .. [[/include/luajit-2.1]]
site_config.LUA_LIBDIR= luajit .. [[/lib/5.1]]
site_config.LUA_BINDIR= openresty .. [[/bin]]
site_config.LUAROCKS_SYSCONFDIR=[[/etc/luarocks]]
site_config.LUAROCKS_ROCKS_TREE=[[/usr/local/openresty/luajit]]
site_config.LUAROCKS_ROCKS_SUBDIR=[[/luarocks/rocks]]
site_config.LUAROCKS_UNAME_S=[[Linux]]
site_config.LUAROCKS_UNAME_M=[[x86_64]]
site_config.LUAROCKS_DOWNLOADER=[[curl]]
site_config.LUAROCKS_MD5CHECKER=[[md5sum]]

site_config.LUAROCKS_EXTERNAL_DEPS_SUBDIRS={ bin="bin", lib={ "lib", [[lib64]] }, include="include" }
site_config.LUAROCKS_RUNTIME_EXTERNAL_DEPS_SUBDIRS={ bin="bin", lib={ "lib", [[lib64]] }, include="include" }

return site_config
EOF
Return to non-root user

exit
Download the v3.2.1 release (Red Hat 3scale API Management v2.3) and extract it

curl -L -o apicast-3.2.1.tar.gz https://github.com/3scale/apicast/archive/v3.2.1.tar.gz
tar xzvf apicast-3.2.1.tar.gz
cd apicast-3.2.1
Install the dependencies

make dependencies
Start APIcast

THREESCALE_PORTAL_ENDPOINT=https://{ACCESS_TOKEN}@{DOMAIN}-admin.3scale.net \
THREESCALE_DEPLOYMENT_ENV=staging \
APICAST_CONFIGURATION_LOADER=lazy \
APICAST_CONFIGURATION_CACHE=0 \
bin/apicast -vvv
Diagnostic Steps
On different stages of the installation process the following errors may be seen:

During make dependencies step:

ERROR: lua_modules/share/lua/5.1/rover/install.lua:44: Build error: Failed compiling object src/lfs.o
Make sure you install "Development Tools" package

When starting APIcast (/home/ec2-user/apicast-3.2.1 is the root path where APIcast is located):

ERROR: ...user/apicast-3.2.1/lua_modules/share/lua/5.1/pl/path.lua:28: pl.path requires LuaFileSystem
stack traceback:
...user/apicast-3.2.1/lua_modules/share/lua/5.1/pl/path.lua:28: in main chunk
[C]: in function 'require'
...ser/apicast-3.2.1/gateway/src/apicast/cli/filesystem.lua:6: in main chunk
[C]: in function 'require'
...-user/apicast-3.2.1/gateway/src/apicast/cli/template.lua:11: in main chunk
[C]: in function 'require'
.../apicast-3.2.1/gateway/src/apicast/cli/command/start.lua:15: in main chunk
[C]: in function 'require'
/home/ec2-user/apicast-3.2.1/gateway/src/apicast/cli.lua:18: in function 'load_commands'
/home/ec2-user/apicast-3.2.1/gateway/src/apicast/cli.lua:23: in main chunk
[C]: in function 'require'
/tmp/JjRZz6pdQp:60: in function 'file_gen'
init_worker_by_lua:48: in function <init_worker_by_lua:46>
[C]: in function 'xpcall'
init_worker_by_lua:55: in function <init_worker_by_lua:53>
From within the /home/ec2-user/apicast-3.2.1 directory try the following:

rm -rf lua_modules
make dependencies

3. 3scale API网关的部署模式
（1）OpenShift
（2）docker
（3）native
