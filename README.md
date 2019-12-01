## vagrant上のサーバーに対して実行する方法
https://qiita.com/snaka/items/d5a8004afbfe665e1d3a

```
ansible-playbook site.yml -i staging  -u vagrant --private-key=.vagrant/machines/default/virtualbox/private_key  --ask-become-pass
```

## メモ
- ansibleのplaybook上にあるタスクは基本的に上から順番に実行される
- モジュールのドキュメントが読みたいときは`andible-doc モジュール名`
- literalinclude
https://www.sphinx-doc.org/ja/1.5/markup/code.html
- デバッグ方法 (https://github.com/midorinagai0106/ansible_test/commit/33bb29a6dd97a8b725042721694bab22c8768d04)
- あるTaskの実行を試したいときにつかう`--tags <tag_name>`オプションがある
- 再起動からの復帰が完了するまで待機する`wait_for`モジュール
- 機密情報を格納する`ansible-vault`
```
ansible-vault create secret.yml
ansible-playbook -i hosts site.yml --ask-vault-pass <Vaultパスワード>
```
https://docs.ansible.com/ansible/latest/user_guide/playbooks_vault.html
ここで作成したファイルは暗号化されるため編集するには復号化して再度上書きする必要がある。できたらrole毎に分けてあると良い。

## トラブルシューティング
rootじゃないと実行できないよ、というエラーが出る
`become: true `を設定、実行時に `--ask-become-pass`を指定

## 便利コマンド
- `--syntax-check`
- `--list-tasks`
- `--check --diff` dry rum とリモートにもたらされる変更のチェック
- `-v` 管理対象の状態やPlaybookの実行状況を詳細に
- `-vvv` ↑に加えてネットワークの接続情報も見れる
- `limit <role name>` 特定のRoleに属しているホストのみPlaybookを実行することができる

## 高速化

### 並列実行数の制御
デフォルトは5並列だが変更できる。
並行実行の単位はinventoryで指定したサーバーグループ
```
ANSIBLE_CONFIG
ansible.cfg(カレントディレクトリ)
.ansible.cfg(ホームディレクトリ)
/etc/ansible/ansible.cfg
実行時のコマンド引数
```

### SSH接続の高速化
OpenSSHは最初に接続した一つのセッションを複数セッション間で共有できる「ControlMaster」という機能を持っている

```
[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=3600s
pipelining = True
```

### Pipeilning
通常の処理：タスク用の実行ディレクトリに各タスク用の実行スクリプトを生成して管理対象へscpで転送してから再度SSH接続する
→スクリプトファイルの転送をせずに標準入力としてSSHでリモート実行するようになる
