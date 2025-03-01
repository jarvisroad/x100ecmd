<!-- <p align="center">
<a href="./README_en.md">English</a>
</p> -->

# ALINCO DJ-X100 CommandLine Tool

- Unofficial command line tool for Alinco [DJ-X100](https://www.alinco.co.jp/product/electron/detail/djx100.html).

## :beginner: How to use

- For macos, if [Homebrew](https://brew.sh/index_ja) is already installed, you can install it with `brew install bellx2/tap/x100cmd`. Updates can be done with `brew upgrade x100cmd`.
- Pre-built binaries for Windows/macos can be downloaded from [Releases](https://github.com/bellx2/x100cmd/releases/). Place it in any location and run it.
- Connect the [DJ-X100](https://www.alinco.co.jp/product/electron/detail/djx100.html) with a USB cable.

Use the `read` command to read and display the data of the specified channel. The port is automatically recognized.

```sh
x100cmd read 10
```

Use the `write` command to write data to the specified channel.

The data will not be reflected until the system is restarted. If you add the `-r` option, the system will be restarted after writing.

```sh
x100cmd write 10 -f 433.00 -m FM -n "430 main" -s "20k" -r
```

Other command control is also possible.

```sh
x100cmd exec restart
```

## :rocket: command

Command list:

- [`check`] - Check serial port and connection
- [`ch`] - Channel command (optional)

- [`read`] - Read channel data
- [`write`] - Write channel data
- [`clear`] - Clear channel data
- [`export`] - Output channel data to file
- [`import`] - Read channel data to file

- [`bank`] - Bank command (initial firmware only)

- [`read`] - Read bank name
- [`write`] - Write bank name

- [`exec`] - Execute control command

| Global flag | Default | Description |
| ---------------- | ---------- | -------------------------------------------------- |
| `-p`, `--port` | `auto` | Serial port name <br/>Automatic search if `auto` |
| `--debug` | false | Debug display |

### `x100cmd check`

Check the connection status.

```sh
x100cmd check

** scan ports **
/dev/cu.wlan-debug
/dev/cu.Bluetooth-Incoming-Port
/dev/cu.usbmodem00000000000001 [3614:D001] DJ-X100!

** check connection **
PortName: auto
DJ-X100 PortName: /dev/cu.usbmodem00000000000001

** send command **
OK

** current freq **
433.000000
```

### `x100cmd read <channel_no>`<br/>`x100cmd ch read <channel_no>`

Read channel data.

```sh
x100cmd read 10

{"freq":433.000000, "mode":"FM", "step":"1k", "name":"430 main", "offset":"OFF", "shift_freq":"0.000000", "att":"OFF", "sq":"OFF", "tone":"670", "dcs":"017", "bank":"ABCTYZ", "empty": false}
```

### `x100cmd write <channel>`<br/>`x100cmd ch write <channel_no>`

Writes channel data. The data for the specified channel will not be reflected until the device is restarted. Data other than the specified data (such as chords) will be retained for the specified channel. If you add the `-r` option, the device will be restarted after writing.

```sh
x100cmd write 10 -f 433.00 -m FM -n "430 main" -s "20k" -r
```

※ If not specified, the current channel setting will be retained
| Flag | Default | Description |
| ----------------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-f`, `--freq` | ​​| Frequency (e.g. 433.0) |
| `-m`, `--mode` | | Mode: FM, NFM, AM, NAM, T98 , T102_B54, DMR, T61_typ1, T61_typ2, T61_typ3, T61_typ4,T61_typx,ICDU,dPMR,DSTAR, C4FM, AIS, ACARS, POCSAG, 12KIF_W, 12KIF_N <br />※Unsupported modes are not displayed? |
| `-s`, `--step` | | Frequency step: 1k, 5k, 6k25, 8k33, 10k, 12k5,15k, 20k, 25k, 30k, 50k, 100k, 125k,200k |
| `-n`, `--name` | | Name (UTF-8) Max: 30byte, NONE for blank |
| `--offset` | | Offset: ON, OFF |
| `--shift_freq` | ​​| Shift frequency |
| `--att` | | Attenuator: OFF, 10db, 20db |
| `--sq` | | Squelch: OFF,CTCSS,DCS,R_CTCSS,R_DCS,JR,MSK |
| `--tone` | | CTSS tone: 670,693...2503,2541 |
| `--dcs` | | DCS code: 017-754 |
| `--bank` | | Bank: A-Z ex. Multiple banks can be specified, e.g. `ABCZ`. Erase with `NONE`|
| `--skip` | | Memory skip: ON, OFF|
| `--lat` | | Latitude ex.35.681382 *Erase with latitude and longitude = 0|
| `--lon` | | Longitude ex.139.766084 *Erase with latitude and longitude = 0|
| `--ext` | | Extended information (0x50-0x7F) 96 characters in half-width |
| `-y`, `--yes` | false | Do not confirm writing |
| `-r`, `--restart` | false | Restart after execution |

### `x100cmd clear <channel_no>`<br/>`x100cmd ch clear <channel_no>`

Erase channel data with initial data.

```sh
x100cmd clear 10
OK
```

| Flag | Default | Description |
| ----------------- | ------ | ---------------- |
| `-y`, `--yes` | false | Do not confirm deletion |
| `-r`, `--restart` | false | Restart after execution |

### `x100cmd export <csv_filename>`<br/>`x100cmd ch export <csv_filename>`

Exports channel data (1-999).

The format is comma-separated CSV (UTF-8 with BOM).

```sh
x100cmd export channels.csv
```

| Flag | Default | Description |
| ------------- | ------ | ------------------------------------------ |
| `-y`, `--yes` | false | Do not display overwrite warning |
| `-a`, `--all` | false | Output empty channel data (frequency = 0) as well |
| `--ext` | false | Output extended information (0x50-0x7F) 96 characters in half-width |

#### File format

```:csv
Channel,Freq,Mode,Step,Name,offset,shift_freq,att,sq,tone,dcs,bank,lat,lon,skip
001,433.000000,FM,10k,430 main,OFF,0.000000,OFF,OFF,670,017,A,0.000000,0.000000,OFF
002,145.000000,FM,10k,144 main,OFF,0.000000,OFF,OFF,670,017,Z,0.000000,0.000000,OFF
....
```

- `--ext`オプション付きデータ

```:csv
Channel,Freq,Mode,Step,Name,offset,shift_freq,att,sq,tone,dcs,bank,lat,lon,skip,ext
001,433.000000,FM,10k,430メイン,OFF,0.000000,OFF,OFF,670,017,A,0.000000,0.000000,OFF,0000e4000000e400000000000000000000000180018001800180010000800100008001000080000080008000807b1700
002,145.000000,FM,10k,144メイン,OFF,0.000000,OFF,OFF,670,017,Z,0.000000,0.000000,OFF,0000e4000000e400000000000000000000000180018001800180010000800100008001000080000080008000807b1700
....
```

| フラグ              | 初期値 | 説明                             |
| ------------------- | ------ | -------------------------------- |
| `-o`, `--overwrite` | true   | 空白の場合に初期値で上書き       |
| `-v`, `--verbose`   | false  | 書き込み中データの詳細を表示する |
| `-r`, `--restart`   | false  | 書き込み後に再起動               |

### `x100cmd bank read <A-Z>`

**(初期ファームウェア v1.00 のみ)**

バンク名を読み込みます。バンクは A-Z で指定します。省略した場合はすべてのバンクを出力します

```sh
x100cmd bank read A
"A","羽田空港"

x100cmd bank read ABC
"A","羽田空港"
"B","成田空港"
"C","横田基地"
```

### `x100cmd bank write <A-Z> <bank_name>`

**(ファームウェア v1.00 のみ)**

バンク名を書き込みます。バンクは A-Z で指定します。再起動するまで反映されません。`-r`オプションを付けると書き込み後に再起動を行います。名称に`NONE`を指定すると名称を消去します（表示は`バンク-A~Z`となります）。

```sh
x100cmd bank write A "羽田空港" -r
```

| フラグ            | 初期値 | 説明               |
| ----------------- | ------ | ------------------ |
| `-y`, `--yes`     | false  | 上書き確認をしない |
| `-r`, `--restart` | false  | 実行後再起動       |

### `x100cmd exec <command>`

コントロールコマンドを送信します。

```sh
x100cmd exec restart # 再起動
```

| コマンド                | 説明                     |
| ----------------------- | ------------------------ |
| version                 | バージョン情報の取得     |
| restart                 | 再起動                   |
| read \<address>         | メモリー読み込み         |
| write \<address> <data> | メモリー書き込み 265Byte |

**(ファームウェア v1.00 のみ)**

| コマンド     | 説明                   |
| ------------ | ---------------------- |
| freq \<freq> | 現在の周波数取得/設定  |
| gps          | 本体の GPS 情報の取得  |
| sql \<level> | SQL 設定 (00-32)       |
| vol \<level> | ボリューム設定 (00-32) |

## 制限事項など

- 非公式ツールであり動作保証はありません。
- コマンド引数やレスポンスは予告なく変更する場合があります。
- 本体のデータを書き換えるため、データが破損する可能性があります。自己責任でご利用ください。
- 開発は MacOS で行っています。それ以外のプラットフォームの積極的な動作確認は行なっていません。
- コマンドライン文字列は UTF-8 です。
- [Windows] DJ-X100 を接続しても認識しない場合があります。その場合は`x100cmd check`コマンドでシリアルポート状況を確認するか、[メーカ提供の DJ-X100 ソフトウエア
  ](https://www.alinco.co.jp/product/electron/soft/softdl02/index.html)でまず接続を確認してください。認識できない場合は動作しません。
- 対応していない周波数やモードを書き込んだ場合の動作は不明です

## :memo: ライセンス

[MIT License](./LICENSE)

## 謝辞

<https://github.com/musen23872/djx100-commandline-tools>

- メモリーデータ構造の一部は`djx100-unofficial-memory-data.hexpat`を参考にさせていただきました。
