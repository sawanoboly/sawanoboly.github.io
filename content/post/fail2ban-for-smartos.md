+++
date = "2014-02-06T00:00:00+09:00"
draft = false
slug = "fail2ban-for-smartos"
tags = ["smartos"]
title = "Install fail2ban to the SmartOS"

+++

[fail2ban](http://www.fail2ban.org/wiki/index.php/Main_Page)がSolaris(OpenSolaris)に対応していたので、Joyent SmartOSに導入する。

- [0.8.12/README.Solaris](https://github.com/fail2ban/fail2ban/blob/0.8.12/README.Solaris)
上記Solarisの内容ではちょっと困る。

## Patch for the SmartOS

パスの問題程度だったので、最新安定版の0.8.12をベースにパッチを作成した。

[https://gist.github.com/sawanoboly/8838359](https://gist.github.com/sawanoboly/8838359)

<script src="https://gist.github.com/sawanoboly/8838359.js?file=0.8.12_smartos.patch"></script>

## Install to the SmartOS

インストールはこのように。

GitリポジトリでもアーカイブでもOKだが、パッチファイルはコピペするとインデントが怪しくなるのでgistのdownloadリンクを使うのがいい。

<script src="https://gist.github.com/sawanoboly/8838359.js?file=README.smartos.md"></script>

## Ban

Banする方式はTCP WrapperとIpfilterが使える。

### TCP WrapperでBan

hosts.denyに設定を出し入れするやりかた。

READMEにもあるけど、TCP Wrapperは有効にしておく。

inetadm -pで確認。

```
# inetadm -p

-- snip --

tcp_wrappers=TRUE

-- snip --
inetadm -Mで設定変更。

inetadm -M tcp_wrappers=true
```

SSH(TCP/22)をBanするサンプル。

```
## cat /etc/fail2ban/jail.local 

[ssh-tcpwrapper]

enabled = true
filter = sshd
action = hostsdeny[daemon_list=sshd]
logpath = /var/log/authlog
```

SmartOSのバージョンによっては /var/log/auth.logだったり、syslogの設定がそもそも無かったりするので調整しよう。

このとおりJail listが登録される。

```
# fail2ban-client status
Status
|- Number of jail:      1
`- Jail list:           ssh-tcpwrapper
```

### IPFilterでBan

ipfコマンドでルールの管理をするやりかた。

ちなみにリストの最後にquickで出し入れするやり方になるので、既存のルールと干渉します。

fail2banと一緒に使うため、blockとpass対象ではquickを使わないようにします。

```
## Example of filter rules with using fail2ban.
pass out quick from any to any keep state
block in all                                                                                ## without quick
pass in quick from 127.0.0.0/8 to any keep state
pass in quick proto icmp from any to any keep state
pass in proto tcp from any to any port = 22 keep state   ## without quick
pass in quick proto tcp from any to any port = 80 keep state
pass in quick proto tcp from any to any port = 443 keep state
```

SSH(TCP/22)をBanするサンプル。

```
## cat /etc/fail2ban/jail.local 

[ssh-tcpwrapper]

enabled = true
filter = sshd
action = ipfilter
logpath = /var/log/authlog
```

このとおりJail listが登録される。

```
# fail2ban-client status        
Status
|- Number of jail:      1
`- Jail list:           ssh-ipfilter
```

### Result


TCP WrapperでBanしたらこんな感じ。

```
#  tail -f /var/log/fail2ban.log
2014-02-06 04:01:51,728 fail2ban.server [85350]: INFO    Changed logging target to /var/log/fail2ban.log for Fail2ban v0.8.12
2014-02-06 04:01:51,740 fail2ban.jail   [85350]: INFO    Creating new jail 'ssh-tcpwrapper'
2014-02-06 04:01:51,746 fail2ban.jail   [85350]: INFO    Jail 'ssh-tcpwrapper' uses poller
2014-02-06 04:01:51,821 fail2ban.jail   [85350]: INFO    Initiated 'polling' backend
2014-02-06 04:01:51,832 fail2ban.filter [85350]: INFO    Added logfile = /var/log/authlog
2014-02-06 04:01:51,836 fail2ban.filter [85350]: INFO    Set maxRetry = 3
2014-02-06 04:01:51,840 fail2ban.filter [85350]: INFO    Set findtime = 600
2014-02-06 04:01:51,841 fail2ban.actions[85350]: INFO    Set banTime = 600
2014-02-06 04:01:51,933 fail2ban.jail   [85350]: INFO    Jail 'ssh-tcpwrapper' started

-- ban log

2014-02-06 04:03:32,957 fail2ban.actions[85350]: WARNING [ssh-tcpwrapper] Ban xxx.xxx.xxx.xxx
2014-02-06 04:03:34,978 fail2ban.actions[85350]: INFO    [ssh-tcpwrapper] xxx.xxx.xxx.xxx already banned
```

パッチは本家にIssueでお知らせしたので、そのうち含まれたらええなあと。

[fail2ban/issues#607](https://github.com/fail2ban/fail2ban/issues/607)
