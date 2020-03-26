3rdライブ最高でしたね！
さて、[デレステ](http://cinderella.idolmaster.jp/sl-stage/)の現在のイベントは、1回プレイすつのにスタミナが50必要です。
自分の環境では最大スタミナが67、スタミナを50使って参加すると17残ります。
次にスタミナが50になる時刻が一体いつなのか知りたい！ということで、
ちゃちゃっと書いたスクリプトが便利だったので貼っておきます。

<img width="304" alt="2.png" src="https://qiita-image-store.s3.amazonaws.com/0/6459/89f22013-8e4e-03cd-9a37-bdec11716b18.png">

```lang:stamina.sh
#!/bin/bash

readonly MIN_PER_STAMINA=5

function usage() {
cat <<_EOT_
Usage:
$0 \$1
スタミナが\$1まで回復する時刻
$0 \$1 \$2
スタミナの現在値を\$1として\$2まで回復する時刻
_EOT_
}

if [ $# -eq 0 ]; then
  usage
  exit 0
fi

calc_stamina=0
if [ $# -eq 1 ]; then
  calc_stamina=$1
fi
if [ $# -eq 2 ]; then
  calc_stamina=$(($2-$1))
fi
minutes=$(($calc_stamina*MIN_PER_STAMINA))
eval "date -v+${minutes}M '+%Y/%m/%d %H:%M'"
echo "( $minutes 分後)"
```
