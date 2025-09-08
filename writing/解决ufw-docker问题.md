## 切换 iptables 至 legacy 模式

Ubuntu 22.04 默认用的是 `iptables-nft`，而 `ufw-docker` 是基于 legacy 的。

```
sudo update-alternatives --config iptables
sudo update-alternatives --config ip6tables
sudo update-alternatives --config arptables
sudo update-alternatives --config ebtables
```

每一步都选 `...-legacy` 版本。
 验证：

```
iptables --version
```

输出应包含 `(legacy)`。

## 修改`/etc/ufw/after.rules`

在`\# don't delete the 'COMMIT'`上方添加如下内容

```
# BEGIN UFW AND DOCKER
:ufw-user-forward - [0:0]   # 确保链存在
:DOCKER-USER - [0:0]        # 确保 DOCKER-USER 链存在（某些版本 Docker 不会预创建）

# 让 DOCKER-USER 链流量走到 ufw-user-forward 链
-A DOCKER-USER -j ufw-user-forward

# 其他未被 ufw 允许的流量直接返回（相当于丢弃）
-A DOCKER-USER -j RETURN
# END UFW AND DOCKER
```

以下是我的完整`/etc/ufw/after.rules`内容

```
#
# rules.input-after
#
# Rules that should be run after the ufw command line added rules. Custom
# rules should be added to one of these chains:
#   ufw-after-input
#   ufw-after-output
#   ufw-after-forward
#

# Don't delete these required lines, otherwise there will be errors
*filter
:ufw-after-input - [0:0]
:ufw-after-output - [0:0]
:ufw-after-forward - [0:0]

# don't log noisy services by default
-A ufw-after-input -p udp --dport 137 -j ufw-skip-to-policy-input
-A ufw-after-input -p udp --dport 138 -j ufw-skip-to-policy-input
-A ufw-after-input -p tcp --dport 139 -j ufw-skip-to-policy-input
-A ufw-after-input -p tcp --dport 445 -j ufw-skip-to-policy-input
-A ufw-after-input -p udp --dport 67 -j ufw-skip-to-policy-input
-A ufw-after-input -p udp --dport 68 -j ufw-skip-to-policy-input

# don't log noisy broadcast
-A ufw-after-input -m addrtype --dst-type BROADCAST -j ufw-skip-to-policy-input


# BEGIN UFW AND DOCKER
:ufw-user-forward - [0:0]   # 确保链存在
:DOCKER-USER - [0:0]        # 确保 DOCKER-USER 链存在（某些版本 Docker 不会预创建）

# 让 DOCKER-USER 链流量走到 ufw-user-forward 链
-A DOCKER-USER -j ufw-user-forward

# 其他未被 ufw 允许的流量直接返回（相当于丢弃）
-A DOCKER-USER -j RETURN
# END UFW AND DOCKER


# don't delete the 'COMMIT' line or these rules won't be processed
COMMIT
```

## 重启ufw

```
ufw disable
ufw enable
```



## 验证 iptables

检查规则：

```
iptables -L DOCKER-USER -n --line-numbers
```

正常将会输出：
```
root@ser631771490187:~# iptables -L DOCKER-USER -n --line-numbers
Chain DOCKER-USER (1 references)
num  target     prot opt source               destination         
1    ufw-user-forward  all  --  0.0.0.0/0            0.0.0.0/0           
2    RETURN     all  --  0.0.0.0/0            0.0.0.0/
```

