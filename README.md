# gesso

研究室で用いるサーバーのセットアップを自動化する ansible playbook です．

## 動作確認済み OS

ubuntu 20.04

## About gesso

以下のことができます．

### 新規サーバーのセットアップに必要な作業

- 最低限必要なプログラムのインストールと設定（pyenv など）
- sudo 権限を持つユーザの作成
- 一般ユーザの作成
- ssh 設定（ルートログインの禁止，パスワード認証の無効化，ポート変更）
- 最低限の ufw の設定

### 既存のサーバーに対する作業

- 一般ユーザの作成

## How to use

### 新規サーバーのセットアップに必要な作業

はじめに，各種値を設定します．
`playbook/root/inventory` を開き，以下の値を設定してください．

#### 対象とするサーバーの設定

"[server]" という文字列の下に記載されている IP アドレスを設定を行うマシンのものに書き換えてください．

```inventory
[server]
192.168.56.10
```

次に対象のサーバーにログインするための情報を設定してください．

```inventory
ansible_port={sshのポート（デフォルト: 22）}
ansible_user={ログインユーザー名}
ansible_ssh_pass={ログインパスワード}
```

次に，ssh のポート番号を変更してください．

```inventory
new_ssh_port={変更後のsshポート}
```

#### 追加する sudo 権限を持つユーザの設定

sudo 権限を持つユーザを作成できます．

```inventory
admin_username={作成するユーザ名}
admin_password={新しいパスワード}
admin_ssh_pubkey={公開鍵のファイル名（`~/.ssh` 以下にあることを想定しています．）}
```

[TODO] 複数ユーザの作成

#### 追加する一般ユーザの設定

一般ユーザを作成する場合も同様に設定してください．

```inventory
admin_username={作成するユーザ名}
admin_password={新しいパスワード}
admin_ssh_pubkey={公開鍵のファイル名（`~/.ssh` 以下にあることを想定しています．）}
```

作成する必要がない場合は`playbook/root/playbook.yml` を開き，以下の部分をすべてコメントアウトしてください．

```playbook.yml
- include_tasks: tasks/20_create_normal_user.yml
    vars:
    username: "{{ normal_username }}"
    password: "{{ normal_password | password_hash('sha512') }}"
    ssh_pubkey: "{{ normal_ssh_pubkey }}"
```

[TODO] 複数ユーザの作成

上記の設定が完了したら，以下のコマンドを実行してください．
実行が完了すると対象のサーバーは再起動されます．

```sh
cd playbook/root
ansible-playbook -i playbook/root/inventry playbook/root/playbook.yml
```
