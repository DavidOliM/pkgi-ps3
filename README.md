# PKGi PS3

[![Downloads][img_downloads]][pkgi_downloads] [![Release][img_latest]][pkgi_latest] [![License][img_license]][pkgi_license]


**PKGi PS3** is a PlayStation 3 port of PSVita [pkgi](https://github.com/mmozeiko/pkgi).

The `pkgi-ps3` homebrew app allows to download and install `.pkg` files directly on your PS3.

**Comments, ideas, suggestions?** You can contact [me](https://github.com/bucanero/) on [Twitter](https://twitter.com/dparrino) and on [my website](http://www.bucanero.com.ar/).

# Features

* **easy** way to list available downloads, including searching, filtering, and sorting.
* **standalone**, no PC required, everything happens directly on the PS3.
* **automatic** downloads, just choose an item, and it will be downloaded by the app to your HDD (`direct mode`) or queued for background download (`background mode`) using the internal Download Manager.
* **resumes** interrupted downloads. You can stop download at any time, switch applications, and come back to resume the download at any time.

Notes:
* **queuing** up multiple downloads is only supported when using `background download` mode.
* **background download tasks** will only show up after rebooting your PS3.

# Download

Get the latest version as a [.pkg file here][pkgi_latest].

# Setup instructions

You need to create a `pkgi.txt` file in `/dev_hdd0/game/NP00PKGI3/USRDIR` that contains the items available for installation. The file uses a very
simple CSV format where each line means one item in the list:

```
contentid,flags,name,description,rap,url,size,checksum
```

where:

| Column | Description |
|--------|-------------|
| `contentid` | is the full content id of the item, for example: `UP0000-NPXX99999_00-0000112223333000`.
| `flags` | is currently an unused number, set it to 0.
| `name` | is a string for the item's name.
| `description` | is a string for the item's description. Currently unused, can be left empty.
| `rap` | the 16 hex bytes for a RAP file, if needed by the item (`.rap` file will be created on `/dev_hdd0/exdata`). Leave empty to skip the `.rap` file.
| `url` | is the HTTP URL where to download the `.pkg`.
| `size` | is the size in bytes of the `.pkg`, or 0 if unknown.
| `checksum` | is a sha256 digest of the `.pkg` (as 32 hex bytes) to make sure the file is not tampered with. Leave empty to skip the check.

**Note:** `name` and `description` cannot contain newlines or commas.

An example `pkgi.txt` file:
```
EP0000-NP9999999_00-0AB00A00FR000000,0,My PKG Test,A description of my pkg,dac109e963294de6cd6f6faf3f045fe9,http://192.168.1.1/html/mypackage.pkg,2715513,afb545c6e71bd95f77994ab4a659efbb8df32208f601214156ad89b1922e73c3
UP0001-NP00PKGI3_00-0000000000000000,0,PKGi PS3 v0.1.0,,,http://bucanero.heliohost.org/pkgi.pkg,284848,3dc8de2ed94c0f9efeafa81df9b7d58f8c169e2875133d6d2649a7d477c1ae13
```

# Usage

Using the application is pretty straight forward. Select the item you want to install and press **(X)**. To sort/filter/search press **triangle**.
It will open the context menu. Press **triangle** again to confirm the new settings, or press **(O)** to cancel any changes.

Press left or right trigger buttons **(L1/R1)** to move pages up or down.

# Q&A

1. Where to get a `rap` string? 

  You can use a tool like RIF2RAP to generate a `rap` from your existing `rif` files. Then you can use any generic Hex viewer to get the hex byte string.

2. Where to get `.pkg` links?

  You can use [PSDLE][] to find `.pkg` URLs for the games you own. Then either use the original URL, or host the file on your own web server.

3. Where to remove interrupted/failed downloads to free up the space?

  In the `/dev_hdd0/packages` folder - each download will be in separate `.pkg` file by its title id. Simply delete the file and start again.

4. Download speed is too slow!

  Optimization is still pending. If `direct` download is slow, you can use `background download` mode to download files using the internal Download Manager.

# Building

You need to have installed:

- [PS3 toolchain](https://github.com/bucanero/ps3toolchain)
- [PSL1GHT](https://github.com/bucanero/PSL1GHT) library
- [tiny3D lib & libfont](https://github.com/wargio/tiny3d) (from Estwald)
- [YA2D lib](https://github.com/bucanero/ya2d_ps3) (an extended version from my repo)
- [dbglogger lib](https://github.com/bucanero/psl1ght-libs/tree/master/dbglogger) (my own debug logging library)

Run `make` to create a debug build, or `make -DCMAKE_BUILD_TYPE=Release` to create an optimized release build.

After than run `make pkg` to create a pkg install file. You can set environment variable `PS3LOAD` (before running `make`) to the PS3's IP address
, that will allow to use `make run` for sending `pkgi-ps3.self` file directly to the PS3Load listener.

To enable debugging logging pass `-DPKGI_ENABLE_LOGGING=ON` argument to make. Then application will send debug messages to
UDP multicast address 239.255.0.100:30000. To receive them you can use [socat][] on your PC:

    $ socat udp4-recv:30000,ip-add-membership=239.255.0.100:0.0.0.0 -

# License

`pkgi-ps3` is released under the [MIT License](LICENSE).

[PSDLE]: https://repod.github.io/psdle/
[socat]: http://www.dest-unreach.org/socat/
[pkgi_downloads]: https://github.com/bucanero/pkgi-ps3/releases
[pkgi_latest]: https://github.com/bucanero/pkgi-ps3/releases/latest
[pkgi_license]: https://github.com/bucanero/pkgi-ps3/blob/master/LICENSE
[img_downloads]: https://img.shields.io/github/downloads/bucanero/pkgi-ps3/total.svg?maxAge=3600
[img_latest]: https://img.shields.io/github/release/bucanero/pkgi-ps3.svg?maxAge=3600
[img_license]: https://img.shields.io/github/license/bucanero/pkgi-ps3.svg?maxAge=2592000
