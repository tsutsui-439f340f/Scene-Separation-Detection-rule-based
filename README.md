# Scene Separation Detection
## 概要


シーン分離検出はMADムービーの作成や、行動認識、動画キャプションタスクにおけるデータセット構築の際に役立つ処理になります。

このプログラムはルールベース手法によって動画内の各シーンの箇所を検出するプログラムになっています。

システムとしては、まず動画の各フレーム情報を畳み込みニューラルネットワークで構成されるVGG16で特徴量に変換します。\
そして現在のフレームの特徴量と1つ前のフレームの特長量の類似度を測定し、閾値判定で現在のフレームが1つ前のフレームのシーンの延長なのかを判別し、キーフレーム情報を取得します。

このプログラムの工夫としては、各シーンのキーフレームを取得後、細かく分割してしまっているシーンに関しては再度、特長量照合を行い、できるだけ大きな塊に分割するようにしています。
この後処理によって、分離しすぎてしまうミスを減らしています。

しかし、現状、変化が激しいシーン(輝度が瞬間的に変化するようなシーンなど)ではシーンの分割ミスが起きやすくなっており、改善する必要があります。
原因は以下のようにVGG16が画像レベルの処理にしか対応しておらず、動画の時系列的な意味を理解できないからだと思われます。
以下は複数のシーンで構成される動画の各フレームをVGG16で埋め込んでt-SNEで次元削減した図です。
連続的なシーンは比較的近くにマッピングされていますが、中には遠くにマッピングされたり、中央付近に関してはシーンの連続性を考慮できない分布となっていることが分かります。この分布が性能低下に大きく影響しています。
![ダウンロード (1)wetgeg](https://user-images.githubusercontent.com/55880071/185231788-4549ef4b-0800-4c8d-8222-dc94b8926b03.png)

## 動作確認環境
windows10\
Python3.8.8
```
主なライブラリ
torchvision==0.10.0
opencv-python==4.6.0.66
scipy==1.6.2
```
## 使い方
``` python scene_separation.py "動画ファイル.mp4"```

## サンプル
この動画はBlenderを用いて作成したもので権利は作者にあります。
この動画の分離結果は`output/`にあります。

https://user-images.githubusercontent.com/55880071/178159094-64ba36de-eb38-4ce2-8e8c-1695786f9dd6.mp4

## 性能評価
|  システム  |  ACC  | 誤検出数 | 未検出数|
| ---- | ---- | ---- | ---- |
| ルールベース処理(VGG16)  | 0.995 | 404 | 3 |
| ルールベース処理(ResNet50)  | 0.981 | 88 | 12 |
| ルールベース処理(VGG16)+後処理| 0.968 | 158 | 20 |
| ルールベース処理(ResNet50)+後処理| 0.970 | 47 | 19 |


## 備考
今後、このプログラムの改良として、より激しいシーンにも対応出来るニューラルシーン分離検出器を作成する予定です。


