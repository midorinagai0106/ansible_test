## vagrant上のサーバーに対して実行する方法
 ansible-playbook yum.yml -i hosts/hosts  -u vagrant --private-key=.vagrant/machines/default/virtualbox/private_key  --ask-become-pass

## トラブルシューティング
rootじゃないと実行できないよ、というエラーが出る
`become: true `を設定、実行時に `--ask-become-pass`を指定
