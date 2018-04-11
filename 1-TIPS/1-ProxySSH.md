# 多段SSHの便利な設定

`~/.ssh/config`の最下行に以下を追加

```bash:~/.ssh/config
Host *+*
  ProxyCommand ssh -W "$(sed -E 's/.*\+//'<<<"%h")":%p "$(sed -E 's/\+[^\+]*//'<<<"%h")"
```

これで、例えば、サーバalphaを踏み台にサーバbetaへSSH接続したいときは、

{% term %}
ssh alpha+beta
{% endterm %}

で接続できる。

もっと多段にするときは`+`をつなげていけばいい。
