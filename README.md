# HFLAP.X

A simple high memory FLAC player for Human68k/X680x0

---

## About This

PhantomX 68030モード + ハイメモリ 専用のFLAC音楽プレーヤです。Mercury-UNITまたは内蔵ADPCMが利用可能です。

44.1kHz/48.0kHz 2ch 16bit/24bit のFLACデータのみ再生可能です。96kHz以上や3ch以上のデータには対応していません。

曲名、アルバム名、アーティスト名のタグ情報表示に対応しています。アルバムアートの表示も可能です。(ただしベースラインJPEGのみ)

---

## 動作環境 (実機)

* X68000実機
* PhantomX 人柱版 (version 1.04b または 1.20)
* Raspberry Pi 4B (なるべく後期型)
* MPU 68030 モード (68000モードでは動作しません)
* MPU ライトバックモード (ライトスルーモードでは処理が追いつきません)
* ハイメモリ256MB以上
* Mercury-UNIT V2.0/V3.0/V3.1/V3.5/V4.0 または KeplerX
* [TS16DRVp.X ハイメモリドライバ](https://hauoli.x68kbbs.com/)
* [PCM8PP.X 0.83d](http://retropc.net/x68000/software/hardware/mercury/pcm8pp/)
* Mercury-UNITが無い場合は [PCM8A.X 1.02.1 (TcbnErik氏のハイメモリ対応版)](https://github.com/kg68k/pcm8a/releases/tag/v1.02.1)

FLACデータファイルは PhantomX 人柱版の WindrvXM 機能を利用してSDカードに直接保存することを強くお勧めします。

68040モードでも動作しますが、68040に対応したFLOATの組み込みが必要です。FLOAT2.Xを使うか、68040対応改造版のFLOAT4を利用してください。SHARP純正FLOAT3.X/FLOAT4.Xは68040に対応していません。なお、MP3のデコードはすべて整数演算によるものです。FLOATはXCのライブラリが一部使います。

---

## 動作環境 (エミュレータ)

XEiJ でのみ確認しています。

* XEiJ X68030 モード
* 060turbo互換ハイメモリ 256MB以上
* TS16DRVp.X ハイメモリドライバ
* MPUクロック 250MHz 以上
* ウェイトサイクルあり可
* PCM8A.X 1.02.1
* データはHFSから読むこと

---

## 使い方

TS16DRVp.X および PCM8PP.X をあらかじめ常駐させておきます。Mercury-UNITが無い場合は PCM8PP.X の代わりに PCM8A.X を使用してください。

        usage: hflap [options] <input-file.mp3>
        options:
             -l[n] ... loop count (none:endless, default:1)
             -v<n> ... volume (1-15, default:6)
             -t<n> ... album art display brightness (1-100, default:off)
             -b<n> ... buffer size [x 64KB] (3-32,default:4)
             -n    ... no progress bar
             -s    ... use main memory for file reading (SCSI disk)
             -h    ... show help message

音割れを防ぐために、PCM8PP/PCM8Aの音量設定は少し小さめとなっています。

アルバムアートワークを表示させるには `-t80` など、表示輝度パーセント値を指定して `-t` オプションを使ってください。
ベースラインJPEGのみ対応しています。プログレッシブJPEGやPNGには対応していません。

WindrvXM/VDISKドライブ以外のSCSIドライブ等からMP3を読み込む場合は `-s` オプションをつけてください。
TS16FILE.X は WindrvXM + ハイメモリと相性が悪いので使わないようにしてください。

再生中はバッファにデータが展開されるたびに `>` が表示されます。最後まで展開が終わると `|` が表示されます。もし再生データの方が進みが速くバッファを消費してしまった場合は `*` が表示されます。バッファアンダーランが発生した場合はメッセージが表示され、再生が一時停止します。`-n` オプションをつけるとプログレス表示を行いません。

---

## FLACデータ入手方法

moraなどのダウンロード販売が利用できます。

ffmpegなどのソフトで既存のデータから変換することも可能です。

---

## ライセンス

本ソフトはFLACデータのデコードに libfoxenflac ライブラリ(GPLライセンス)を使用しているためGPLとなります。

ソースコードは https://github.com/tantanGH/hflap-src/ に置いてあります。

---

## History

* 0.2.0 (2024/03/16) ... 初版
