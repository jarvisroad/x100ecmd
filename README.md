<!-- <p align="center">
<a href="./README_en.md">English</a>
</p> -->

# ALINCO DJ-X100E CommandLine Tool

- Unofficial command line tool for Alinco [DJ-X100E](https://alinco.com/Products/ham/ht/DJ-X100TE/)

## :beginner: How to use

- Pre-built binaries for Windows can be downloaded from [Releases](https://github.com/jarvisroad/x100ecmd/releases/). Place it in any folder and run it.
- Connect the [DJ-X100E](https://alinco.com/Products/ham/ht/DJ-X100TE/) with a USB cable.

Use the `read` command to read and display the data of the specified channel. The port is automatically recognized.

```sh
x100ecmd read 10
```

Use the `write` command to write data to the specified channel.

The data will not be reflected until the system is restarted. If you add the `-r` option, the system will be restarted after writing.

```sh
x100ecmd write 10 -f 433.00 -m FM -n "430 main" -s "20k" -r
```

Other command control is also possible.

```sh
x100ecmd exec restart
```

## :rocket: commands

Command list:

- [`check`] - Check serial port and connection
- [`ch`] - Channel command (optional)

- [`read`] - Read channel data
- [`write`] - Write channel data
- [`clear`] - Clear channel data
- [`export`] - Output channel data to file
- [`import`] - Read channel data to file
- [`exec`] - Execute control command

| Global flag | Default | Description |
| ---------------- | ---------- | -------------------------------------------------- |
| `-p`, `--port` | `auto` | Serial port name <br/>Automatic search if `auto` |
| `--debug` | false | Debug display |

### `x100ecmd check`

Check the connection status.

```sh
x100ecmd check

** scan ports **
COM3
COM4 [3614:D001] DJ-X100!
COM1

** check connection **
PortName: auto
DJ-X100 PortName: COM4

** send device check command **
DJ-X100E

** current version **
ver 3.90-008
```

### `x100ecmd read <channel_no>`<br/>`x100cmd ch read <channel_no>`

Read channel data.

```sh
x100ecmd read 10

{"freq":433.000000, "mode":"FM", "step":"1k", "name":"430 main", "offset":"OFF", "shift_freq":"0.000000", "att":"OFF", "sq":"OFF", "tone":"670", "dcs":"017", "bank":"ABCTYZ", "empty": false}
```

### `x100ecmd write <channel>`<br/>`x100ecmd ch write <channel_no>`

Writes channel data. The data for the specified channel will not be reflected until the device is restarted. Data other than the specified data (such as chords) will be retained for the specified channel. If you add the `-r` option, the device will be restarted after writing.

```sh
x100ecmd write 10 -f 433.00 -m FM -n "430 main" -s "20k" -r
```

※ If not specified, the current channel setting will be retained
| Flag | Default | Description |
| ----------------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-f`, `--freq` | ​​| Frequency (e.g. 433.0) |
| `-m`, `--mode` | | Mode: FM, NFM, AM, NAM, T98, T102_B54, DMR, T61_typ1, T61_typ2, T61_typ3, T61_typ4, dPMR, DSTAR, C4FM, AIS, ACARS, POCSAG, 12KIF_W, 12KIF_N<br />※Unsupported modes are not displayed? |
| `-s`, `--step` | | Frequency step: 1k, 3k125, 5k, 6k25, 8k33, 10k, 12k5, 15k, 20k, 25k, 30k, 50k, 100k, 125k, 200k |
| `-n`, `--name` | | Name (UTF-8) Max: 30 chars, NONE for blank |
| `--offset` | | Offset: ON, OFF |
| `--shift_freq` | ​​| Shift frequency |
| `--att` | | Attenuator: OFF, 10db, 20db |
| `--sq` | | Squelch: OFF, CTCSS, DCS, R_CTCSS, R_DCS, JR, MSK |
| `--tone` | | CTSS tone: 670,693...2503,2541 |
| `--dcs` | | DCS code: 017-754 |
| `--bank` | | Bank: A-Z ex. Multiple banks can be specified, e.g. `ABCZ`. Erase with `NONE`|
| `--skip` | | Memory skip: ON, OFF|
| `--lat` | | Latitude ex.35.681382 *Erase with latitude and longitude = 0|
| `--lon` | | Longitude ex.139.766084 *Erase with latitude and longitude = 0|
| `--ext` | | Extended information (0x50-0x7F) 96 characters in half-width |
| `-y`, `--yes` | false | Do not confirm writing |
| `-r`, `--restart` | false | Restart after execution |

### `x100ecmd clear <channel_no>`<br/>`x100ecmd ch clear <channel_no>`

Erase channel data with initial data.

```sh
x100ecmd clear 10
OK
```

| Flag | Default | Description |
| ----------------- | ------ | ---------------- |
| `-y`, `--yes` | false | Do not confirm deletion |
| `-r`, `--restart` | false | Restart after execution |

### `x100ecmd export <csv_filename>`<br/>`x100cmd ch export <csv_filename>`

Exports channel data (1-999).

The format is comma-separated CSV (UTF-8 with BOM).

```sh
x100ecmd export channels.csv
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
- Data with `--ext` option

```:csv
Channel,Freq,Mode,Step,Name,offset,shift_freq,att,sq,tone,dcs,bank,lat,lon,skip,ext
001,433.000000,FM,10k,430 main,OFF,0.000000,OFF,OFF,670,017,A,0.000000,0.000000,OFF,0000e40000000e40000000000000000000000180018001800180010000800100008001000080000080008000807b1700
002,145.000000,FM,10k,144 Main,OFF,0.000000,OFF,OFF,670,017,Z,0.000000,0.000000,OFF,0000e4000000e400000000000000000000000180018001800180010000800100008001000080000080008000807b1700
....
````

| Flag | Default | Description |
| ------------------- | ------ | -------------------------------- |
| `-o`, `--overwrite` | true | Overwrite with default value if blank |
| `-v`, `--verbose` | false | Display details of data being written |
| `-r`, `--restart` | false | Restart after writing |

### `x100ecmd exec <command>`

Sends a control command.

```sh
x100ecmd exec restart # Restart
```

| Command | Description |
| ----------------------- | ------------------------ |
| version | Get version information |
| restart | Restart |
| read \<address> | Read memory |
| write \<address> <data> | Write memory 265Byte |

## Restrictions, etc.

- This is an unofficial tool and there is no guarantee that it will work.
- Command arguments and responses may change without notice.
- This may cause data corruption as it rewrites data on the device. Use at your own risk.

- Command line strings are in UTF-8.

- [Windows] The DJ-X100E may not be recognized even if it is connected. In that case, check the serial port status with the `x100ecmd check` command, or check the connection first with the DJ-X100E software provided by the manufacturer. If it is not recognized, it will not work.

- It is unknown how it will work if an unsupported frequency or mode is written.

## :memo: License

[MIT License](./LICENSE)

## Acknowledgements

<https://github.com/musen23872/djx100-commandline-tools>

- メモリーデータ構造の一部は`djx100-unofficial-memory-data.hexpat`を参考にさせていただきました。
