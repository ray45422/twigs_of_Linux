#RigidChips on wine  
最近のwineはかなり性能が良さそうでRigidChipsがいい感じに動く  
https://wiki.archlinuxjp.org/index.php/Wine  
基本的にこれに従う感じ  
#wineのインストール  
multilibリポジトリにx86_64版があるのでそれをpacmanで入れる  
  
#winetricsのインストール  
winetricsはwineの設定がいろいろ簡単にできるスクリプトだ  
pacmanでインストールできる  
  
＃winetricsでwineの設定  
##フォントのインストール  
winetricsを起動し、select the default wineprefix>Install a fontからすべてのフォントをインストールする  
日本語を表示するときはロケールの設定をしよう  
  
##directXのインストール  
Install a Windows DLL or componentからd3dx9とdirectplay、dsoundをインストールする  
dinputを入れるとF3(Configure Input)で落ちてしまうので入れないようにするのが賢明  
mpg123が無いとNATSoundとかでエラーを吐かれたようなきがする  
  
#これで終わり  
ほとんどうろ覚えだけどこれくらいすれば動いたと思う  
お酒の力も使えるしネットワーク機能も使えるのでwine素晴らしすぎる  
けどRC窓にアクティブ移す関数は使えなかった気がする  
あとRCDもちょっとエラーの頻度が多い気がするけど結構動く  
喪寺は使ってないから知らん