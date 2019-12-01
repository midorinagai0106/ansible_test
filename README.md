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


## トラブルシューティング
rootじゃないと実行できないよ、というエラーが出る
`become: true `を設定、実行時に `--ask-become-pass`を指定
