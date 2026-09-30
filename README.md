# v2nodePro installer

This repository distributes the installer and runtime packages for v2nodePro. The latest release is [v0.3.7](https://github.com/fsh2502/v2nodePro-install/releases/tag/v0.3.7), built with Xray Core 26.7.28.

The repository contains installation and management scripts, a sample configuration, documentation, and the license. Binaries and GeoIP/GeoSite data are GitHub Release assets. No Go application source, Go module files, or source repository Git history is included here.

## Quick install on Linux

```bash
wget -N https://raw.githubusercontent.com/fsh2502/v2nodePro-install/main/script/install.sh && bash install.sh
```

## Advanced server setup

```bash
wget -N https://raw.githubusercontent.com/fsh2502/v2nodePro-install/main/script/caidatserver.sh && bash caidatserver.sh
```

Run these commands as root. The standard installer selects the latest release for Linux x86_64, ARM64, or s390x. It downloads the ZIP and its SHA-256 file, verifies the package, and only then replaces an existing installation. Append `v0.3.7` to the standard install command to select this version explicitly. Keep node credentials in `/etc/v2node/config.json`; do not upload them to GitHub.

For other platforms, download the matching package from [Releases](https://github.com/fsh2502/v2nodePro-install/releases), verify its adjacent `.sha256` file, and unpack it. Each package contains the executable, GeoIP/GeoSite data, a sample configuration, and documentation.

## Source and license

See [SOURCE-NOTICE.md](SOURCE-NOTICE.md) and [LICENSE](LICENSE). Distribution of binaries still requires access to the corresponding source of MPL 2.0 covered components. If the source repositories become private, provide that source to binary recipients through another channel.
