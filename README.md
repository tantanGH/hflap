# HFLAP.X

A simple high memory FLAC player for Human68k/X680x0

---

## About This

Human68k ハイメモリ環境専用のFLAC音楽プレーヤです。Mercury-UNITまたは内蔵ADPCMが利用可能です。

曲名、アルバム名、アーティスト名のタグ情報表示に対応しています。アルバムアートの表示も可能です。(ただしベースラインJPEGのみ)

実行ファイルは3つ同梱されています。

 - HFLAP.X ... 68000/68030/68040/68060 のすべてのMPUモードに対応
 - HFLAP030.X ... 68030/68040/68060 のMPUモードに対応(より高速動作します)
 - HFLAP060.X ... 68060専用(さらに高速に動作します)

固定小数点演算しか行っていないため、FPUの有無や、FLOATドライバの種類によるパフォーマンスの違いはありません。

再生可能なデータはハイメモリのサイズやMPUパワーにより異なりますが、060turbo実機で48kHz/24bitのFLACデータが再生可能です。

MP3の再生には[HMP3P.X](https://github.com/tantanGH/hmp3p/)をご利用ください。

MP3/FLAC両対応のセレクタ[MP3SEL.X](https://github.com/tantanGH/mp3sel/)との併用をお勧めします。

---

## 動作環境 (X68000実機 + PhantomX)

* X68000実機
* PhantomX 1.3以降 + ハイメモリ128MB以上 (できれば256MB以上)
* Raspberry Pi 4B
* MPU ライトバックモード (データによってはライトスルーモードでも大丈夫)
* Mercury-UNIT または PilederX または KeplerX
* [TS16DRVp.X ハイメモリドライバ](https://hauoli.x68kbbs.com/)
* [PCM8PP.X 0.83d](http://retropc.net/x68000/software/hardware/mercury/pcm8pp/)
* Mercury-UNITが無い場合は [PCM8A.X 1.02.1 (TcbnErik氏のハイメモリ対応版)](https://github.com/kg68k/pcm8a/releases/tag/v1.02.1)

FLACデータファイルは PhantomX の WindrvXM 機能を利用してSDカードに直接保存することをお勧めします。
SCSIディスクから読み込む場合は`-s`オプションをつけるか、TS16FILE.Xを使用してください。

---

## 動作環境 (X68030実機 + 060turbo)

* X68030実機
* 060turbo + ハイメモリ128MB
* MPU コピーバックモード
* Mercury-UNIT または PilederX または KeplerX
* [PCM8PP.X 0.83d](http://retropc.net/x68000/software/hardware/mercury/pcm8pp/)
* 内蔵ADPCMで再生する場合は [PCM8A.X 1.02.1 (TcbnErik氏のハイメモリ対応版)](https://github.com/kg68k/pcm8a/releases/tag/v1.02.1)

コピーバックモードを使用してください。060turbo.sysに`-cm1`オプションを指定する必要があります。

FLACデータファイルは直接ハイメモリに読み込むため、060turbo.sysに`-ss`オプションを指定してください。あるいはTS16FILE.Xを使うか、HFLAP.Xに `-s`オプションをつけて読み込みを行ってください。

もしバッファアンダーランが発生するようであれば、`-b8`など、バッファサイズを少し多めにとってみてください。

CONFIG.SYSにPROCESS=行の記述があると、Timer-D割り込みが使用されるため再生が間に合わなくなる場合があります。

---

## 動作環境 (エミュレータ)

XEiJ 68030/68060 でのみ確認しています。0.26.01.08以降でMercury-UNIT対応となりました。

* XEiJ 060turboモード MPUクロック50MHz以上 + 060turbo.sys
* XEiJ X68030モード MPUクロック250MHz以上 + TS16DRVp.X (or 060turbo.sys)
* [PCM8PP.X 0.83d](http://retropc.net/x68000/software/hardware/mercury/pcm8pp/)
* 内蔵ADPCMで再生する場合は [PCM8A.X 1.02.1 (TcbnErik氏のハイメモリ対応版)](https://github.com/kg68k/pcm8a/releases/tag/v1.02.1)

FLACデータファイルはHFSから読むことをお勧めします。

XEiJでPCM8PP.Xを使用する場合、常駐前に MKMU.R を実行する必要があります。

---

## 使い方

お使いの環境にあわせて適宜 HFLAP030.X または HFLAP060.X を HFLAP.X にリネームするなどしてください。

PCM8PP.X をあらかじめ常駐させておきます。内蔵ADPCMで再生する場合は PCM8A.X を使用してください。

        usage: hflap [options] <input-file.fla>
        options:
             -l[n] ... loop count (none:endless, default:1)
             -v<n> ... volume (1-15, default:6)
             -t<n> ... album art display brightness (1-100, default:off)
             -b<n> ... buffer size [x 64KB] (3-32,default:4)
             -f    ... full disk read mode (default:continuous disk read)
             -n    ... no progress bar
             -s    ... use main memory for file reading (SCSI disk)
             -h    ... show help message

音割れを防ぐために、PCM8PP/PCM8Aの音量設定は少し小さめとなっています。

アルバムアートワークを表示させるには `-t80` など、表示輝度パーセント値を指定して `-t` オプションを使ってください。
ベースラインJPEGのみ対応しています。プログレッシブJPEGやPNGには対応していません。

WindrvXM/VDISK/HFSドライブ以外のSCSIドライブ等からFLACデータを読み込む場合は `-s` オプションをつけるか TS16FILE.X を活用してください。060turboの場合は060turbo.sysに`-ss`オプションを指定してください。

再生中はバッファにデータが展開されるたびに `>` が表示されます。最後まで展開が終わると `|` が表示されます。もし再生データの方が進みが速くバッファを消費してしまった場合は `*` が表示されます。バッファアンダーランが発生した場合はメッセージが表示され、再生が一時停止します。`-n` オプションをつけるとプログレス表示を行いません。

デフォルトはディスク逐次読み込みとなります。`-f`を指定すると最初に一括でハイメモリに読み込み、デコードの処理負担が軽くなりますが、再生開始までの時間が長くなります。また、必要となるハイメモリがその分大きくなります。基本的には逐次読み込みでの再生を推奨します。

---

## FLACデータ入手方法

moraなどのダウンロード販売が利用できます。96kHzなどのデータは再生が厳しい場合がありますのでご注意ください。48kHz/24bitなどであれば特に問題にならないでしょう。

Win/Mac/Linuxなどで `flac`, `ffmpeg`などのソフトを使って既存のデータから変換することも可能です。

---

## ライセンス

本ソフトはFLACデータのデコードに libfoxenflac ライブラリ(GPLライセンス)を使用しているためGPLとなります。

ソースコードは https://github.com/tantanGH/hflap-src/ に置いてあります。

---

## History

* 0.8.0 (2026/03/31) ... ハイメモリの消費量を削減した
* 0.7.0 (2026/03/30) ... libfoxenflac-x68k ライブラリを使ってビルドするようにした
* 0.6.5 (2026/03/30) ... アルバムアートワークの長辺が512になるように自動フィッティングするようにした
* 0.6.1 (2026/03/30) ... スペースが含まれるファイル名をクオートして渡した場合に再生できなくなっていたのを修正
* 0.6.0 (2026/03/29) ... ビルド環境をelf2x68kに移行した
* 0.5.1 (2026/03/16) ... アルバムアートワークの1辺が1024以上の場合は1/2サイズで表示するようにした
* 0.5.0 (2026/03/16) ... 逐次ディスク読み込み再生に対応し、それをデフォルトとした
* 0.4.0 (2026/03/15) ... さらに高速化し、680x0汎用バイナリ/68030以上専用バイナリ/68060専用バイナリの3つを同梱
* 0.2.5 (2024/03/20) ... もう少し高速化した
* 0.2.4 (2024/03/19) ... 少し高速化した
* 0.2.3 (2024/03/17) ... 88.2kHz/96.0kHzのデータを拒否しないようにした(ただし再生は間に合いません)
* 0.2.2 (2024/03/16) ... ID3タグがついている場合は読み飛ばすようにした
* 0.2.1 (2024/03/16) ... 拡張子のチェックを廃止
* 0.2.0 (2024/03/16) ... 初版
