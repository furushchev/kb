# SSHの接続クライアントをアドレスごとに制限する

```conf:/etc/ssh/sshd_config
# existing settings...

PasswordAuthentication no

Match Address 10.68.0.0/24
      PasswordAuthentication yes

Match Address 192.168.0.0/24
      PasswordAuthentication yes
```

以上のように設定すると、基本はパスワード認証無効で、接続元が特定のアドレスにマッチしたときのみパスワード認証を有効にできる
