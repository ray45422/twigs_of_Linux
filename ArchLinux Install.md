#ArchLinux Install  
ArchLinuxでXfce4を使用する環境を構築したりする  
インターネットには有線で接続できる前提  
#インストール準備  
##インストールメディアの起動  
ArchLinuxのサイトからインストールイメージをダウンロードし、ブート可能にする  
USBメモリにイメージを焼くならddを使えば楽  
BIOSとかの設定からインストールイメージを焼いたメディアを選択して起動  
すでにArchが起動しているならarch-install-scriptをインストールすればそこからインストールできるらしい(https://wiki.archlinuxjp.org/index.php/%E6%97%A2%E5%AD%98%E3%81%AE_Linux_%E3%81%8B%E3%82%89%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)  
##キーボード配列の変更  
US配列を使っているならいいが僕はJIS配列キーボードを使っているので  
>\# loadkeys jp106  
  
##ネットワーク  
>\# ip link  
  
でIPアドレスが割り当てられるか確認する  
もし割り当てられてなければ  
>\#systemctl restart dhcpcd.service  
  
ping飛ばしてインターネットに繋がるか確認  
##パーティショニング  
fdiskとか使ってパーティショニングする  
man使え  
UEFIとか知らねえ  
##フォーマット  
mkfs.ext4とかでフォーマット  
man使え  
#ArchLinuxインストール  
##パーティションをマウントする  
パーティションをルートから順に/mnt下の適切な場所にマウントする  
>\# mount /dev/< partition > /mnt  
  
##時間を合わせる  
>\# ntpd -qg ntp.nict.jp  
  
##ミラーを選択  
/etc/pacman.d/mirrorlistを編集する  
物理的に遠いミラーはだいたい遅いので以下を頭らへんに追記する  
>Server = http://ftp.jaist.ac.jp/pub/Linux/ArchLinux/$repo/os/$arch  
Server = http://ftp.tsukuba.wide.ad.jp/Linux/archlinux/$repo/os/$arch  
  
##ベースシステムのインストール  
ついでなので一緒にいろいろインストールしてしまおう  
>\# pacstrap /mnt base base-devel grml-zsh-config grub openssh  
  
##fstabの生成  
>\# genfstab -pU /mnt >> /mnt/etc/fstab  
  
これだけ  
##chroot  
インストールしたシステムに移動する  
>\# arch-chroot /mnt /bin/zsh  
  
##rootのデフォルトシェルを変更  
rootで使うことが無いと思われるが一応変更  
>\# which zsh  
  
でzshのパスを確認  
>\# chsh  
  
入力を求められたらzshのパスを入力  
###ホストネームの設定  
>\# echo hostname > /etc/hostname  
  
###ロケールの生成と設定  
/etc/locale.gen  
を編集し  
>en_US.UTF-8 UTF-8  
ja_JP.UTF-8 UTF-8  
  
のコメントアウトを外す  
  
>\# locale-gen  
\# echo LANG=en_US.UTF-8 > /etc/locale.conf  
\# export LANG=en_US.UTF-8  
\# echo "KEYMAP=jp106" > /etc/vconsole.conf  
  
これで大体大丈夫なはず  
  
##Initial ramdisk環境の作成  
必要かわからないがやっても問題なさそうなのでやっておく  
>\# mkinitcpid -p linux  
  
##パスワードを設定  
rootのパスワードを設定する  
できるだけ長く複雑に  
>\# passwd  
  
##ユーザーの追加など  
\# useradd -m -s /bin/zsh user  
ユーザーの追加  
>\# passwd user  
>  
パスワードの設定  
>\# gpasswd -a user wheel  
>  
ユーザーをwheelグループに追加  
>\# EDITOR=nano visudo  
>  
nanoでvisudo  
>wheel =(ALL) ALL  
  
のところのコメントアウトを外す  
なければ追記  
間違ってればエラーが出るのでメッセージを頼りに編集しなおす  
##GRUBのインストール  
マルチブートとかするときは  
>\# pacman -S os-prober  
\# grub-install --recheck /dev/< partition >  
\# grub-mkconfig -o /boot/grub/grub.cfg  
  
##再起動  
>\# exit  
\# umount -R /mnt  
\# reboot  
  
これで再起動できればとりあえずインストール成功です  
  
#デスクトップ環境の構築  
##Xの導入  
\# pacman -S xorg-server xorg-server-utils xorg-xinit xorg-xclock xterm  
  
##ビデオドライバのインストール  
>$ lspci | grep VGA  
  
で何を入れるか確認  
Intelなら  
>$ sudo pacman -S xf86-video-intel  
  
#DMのインストール  
$ sudo pacman -S slim archlinux-themes-slim slim-themes  
$ EDITOR=nano sudo -e /etc/slim.conf  
>login_cmd exec /bin/zsh -l ~/.xinitrc %session  
daemon yes  
current_theme archlinux-simplyblac  
  
このように編集とコメントアウト  
最後に  
>$ sudo systemctl enable slim.service  
  
これで起動時に自動でslimが表示されます  
##WMのインストール  
>$ sudo pacman -S xfce4 xfce4-goodies gamin  
  
同じリポジトリ内のxinitrcを参考に  
  
##packerのインストール  
先にpackerをインストールしてAURを使いやすくする  
$ wget https://aur.archlinux.org/packages/pa/packer/packer.tar.gz  
>$ tar -zxvf packer.tar.gz  
$ cd packer  
$ makepkg -s  
$ sudo pacman -U packer-20140817-1-any.pkg.tar.xz  
これでインストールできる  
  
##mozcのインストール  
>$ sudo pacman -S mozc ibus-mozc  
>$ ibus-setup  
  
を実行するとデーモンが立ち上がってないだのとウィンドウが出てきて鬱陶しいが書かれているように環境変数を設定するとよさげ  
ibusの設定でmozcを選択する  
xfceの設定>セッションと起動>自動開始アプリケーションにibusの起動を追加  
コマンドは  
>/usr/bin/ibus-daemon --xim --replace  
  
こんな感じ  
キーボードの配列が変わっていると思うのでxfceの設定から変更する  
  
#環境変数とか  
CUI、GUI共通の環境変数を~/.myenvってファイルにまとめた  
.xinitrcや.zshrcの後ろの方に追記しておくと編集が楽かもしれん  
  
#個人的にいろいろしたこと  
##ttyの文字化け対策  
ttyで文字化けする対策として.zshrcにこんなのを追記したりした  
>case $TERM in  
        linux)  
                LANG=C  
                ;;  
        *)  
        LANG=ja_JP.UTF8  
        ;;  
esac  
export LANG  
  
これでttyを使ってる時は英語にしてくれるので文字化けしない  
  
##仮想端末  
個人的に端末はguakeがお気に入り(背景透過や背景画像が設定できたりF12ですぐ出せる)  
pacmanでインストールできる  
  
##スクリーンショット  
スクリーンショットのために設定のキーボード>アプリケーションショートカットキーからプリントスクリーンキーに  
>xfce4-screenshooter -f -s /home/username/Pictures  
  
などとしておくと少し便利かもしれない  
確認画面が出ないやつのほうがいいかもとは思う  
  
##画面ロック  
xscreen-saverをインストールすればロックが使える  
  
##ネットワークマネージャー  
network-manager-appletを入れていると上のタイルからネットワークの設定ができて結構便利  
xfce4-notifydとgnome-keyringが必要っぽい  
  
#これで終わり  
なにかあったら追記する  
  
  
  
  
  
  
  
  
  
