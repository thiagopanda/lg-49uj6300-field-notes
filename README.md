# 49UJ6300-UA field log

Field log for rooting an LG 49UJ6300-UA on webOS TV Lite 3.9.3-6280413 (manufacturing firmware 06.10.55) with [dejavuln-autoroot](https://github.com/throwaway96/dejavuln-autoroot) v0.0.10 and Homebrew Channel, then turning promo settings off without deleting firmware packages.

No passphrases, keys, serial numbers, network addresses, or other device identifiers are in this repository. The USB trigger itself is the procedure in the dejavuln-autoroot v0.0.10 readme, which this page links.

24 Oct 2026. Local set, owner present. Promo software was switched off after root. Firmware packages stayed on disk.

`Linux LGwebOSTV 4.4.3-119.drd4tv.6 armv7l`

## 49UJ6300-UA · webOS TV Lite 3.9.3

| | |
|---|---|
| release | 3.9.3-6280413 · dreadlocks2-dudhwa · API 4.1.0 |
| firmware | 06.10.55 · built 28 Apr 2025 |
| board | M16P_DVB · HE_DTV_W17M_AFADATAA |
| image | starfish-atsc-secured · ATSC · no 3D · no DVR |
| memory | 1.5 GB RAM, about 200 MB free, swap already in use |
| storage | 4 GB |

## Developer Mode

`prisoner@LGwebOSTV:/media/developer$ ssh -p 9922`

Developer Mode 1.12.2 was on. The jail user is `prisoner` on port 9922, OpenSSH 6.1. macOS OpenSSH completed the handshake only after the algorithms were pinned:

```
KexAlgorithms=ecdh-sha2-nistp256
HostKeyAlgorithms=ssh-rsa
PubkeyAcceptedAlgorithms=ssh-rsa
Ciphers=aes128-ctr
MACs=hmac-sha1
```

The key server on port 9991 served `webos_rsa`. From that jail, `luna-send` is mode 700 and returned permission denied. `luna-send-pub` connected and printed nothing. The system image is read-only. USB mounts visible in `/proc/partitions` were not visible inside the jail, and the jail cannot `mknod` a block device.

SSAP on port 3000 registered after an on-screen Allow. `listApps` returned 75 apps. The only `removable: true` entries were Developer Mode and six store stubs: Netflix 0.0.4, Prime Video 0.0.16, Disney+ 0.0.47, Hulu 0.0.32, Sling TV 0.0.98, LG Channels 0.0.29. There is no SSAP uninstall method.

Firmware apps with `removable: false` include Advertisement (`com.webos.app.adapp`), LivePlus / ACR (`acrcomponent`, `acrhdmi1`–`acrhdmi4`, `acroverlay`, all 1.0.7), Brand Shop, Store Demo, Customer Support, LG Content Store, the browser, and Live TV.

The SSAP client is the small Node client from [0xbyt4/lg-webos1-hub](https://github.com/0xbyt4/lg-webos1-hub). That repository is a webOS 1 hub. It is not a debloater.

## Wrong generation

[CasperDkk’s webos-debloat gist](https://gist.github.com/CasperDkk/7520efa763d9e23c531c2e8564f9bb4e) was written on webOS 26, firmware 11.2. It hides tiles, stops the preload manager, and flips settings. It never deletes the root filesystem. This set has no preload-manager policy and no webOS 26 boot hook. Keys such as `sportsAlarm`, `livePromotion`, and `contentRecommendation` are not the keys stored here.

[cleanyourtv-WebOS](https://github.com/lasharor/cleanyourtv-WebOS) stops on webOS 4 and older. [glasshouse](https://github.com/rorygallagher2024/glasshouse) expects Homebrew root already. [webos-vibehack](https://github.com/OmkarK97/webos-vibehack) is a Developer Mode launcher and does not disable packages.

## Method

Choice followed [webOS Homebrew’s exploit list](https://www.webosbrew.org/rooting/exploits/).

| method | result |
|---|---|
| [RootMyTV](https://rootmy.tv/) | PATCHED · around 2022 |
| [crashd](https://gist.github.com/throwaway96/e811b0f7cc2a705a5a476a8dfa45e09f) | WEBOS 4+ · current Dev Mode jail blocks it |
| GetMeIn | JAIL 2024-04 · this set was already on Dev Mode 1.12.2 |
| [faultmanager](https://github.com/throwaway96/faultmanager-autoroot) | NOT RUN · readme points webOS 3.5 at DejaVuln |
| NVM debug | HARDWARE · high voltage · not used |
| [dejavuln](https://github.com/throwaway96/dejavuln-autoroot) | USED · v0.0.10 worked on 06.10.55 |

[dejavuln-autoroot](https://github.com/throwaway96/dejavuln-autoroot) by throwaway96, AGPL. DejaVuln is credited to [Jacob Clayden](https://jacobcx.dev/). The script also credits [Bitdefender’s LG webOS write-up](https://www.bitdefender.com/blog/labs/vulnerabilities-identified-in-lg-webos/). The release was used as published. The script was not rewritten.

```
dejavuln-autoroot v0.0.10
sha256 83f39e95ec702e6370df65bde16acced9861a87e473f763a9e55de948aa7756f
autoroot.sh          12712 bytes
hbchannel.ipk      1699858 bytes   Homebrew Channel 0.7.2
```

The bundled ipk is [webos-homebrew-channel v0.7.2](https://github.com/webosbrew/webos-homebrew-channel/releases/tag/v0.7.2).

## Run

1. Developer Mode was already on. SSAP pairing was accepted on the TV. The inventory above came from that access.
2. A FAT32 stick was mounted on a computer. The official release was extracted at the volume root, not inside an extra version folder.
3. Photo & Video opens `tmp` and hides the trigger file. The Music app is the player the [release readme](https://github.com/throwaway96/dejavuln-autoroot) names. Playing that file produced a root-owned `/tmp/autoroot.*` directory.
4. Homebrew Channel (`org.webosbrew.hbchannel`) appeared. Persistence is `/var/luna/preferences/devmode_enabled` as a directory. A marker named `autoroot.telnet` was not created.
5. A reboot with Developer Mode still installed kept the Homebrew icon. The release says to uninstall Developer Mode before rebooting, and never to install it again once the set is rooted.
6. Developer Mode was removed. A second reboot kept Homebrew. Port 9922 closed.
7. Telnet was on. It was turned off in Homebrew, and SSH was turned on. Port 23 then refused connections. Port 22 accepted them.

## Root shell

`root@LGwebOSTV:~# ss -ltn`

| | |
|---|---|
| ssh 22 | OPEN · dropbear 2022.83 · user root |
| telnet 23 | CLOSED · unauthenticated on this firmware |
| dev ssh 9922 | CLOSED · Developer Mode removed |
| devmode_enabled | directory · root persistence |
| webosbrew_sshd_enabled | present |
| webosbrew_telnet_disabled | present |

Homebrew’s default root password and `authorized_keys` setup are in the [webos-homebrew-channel](https://github.com/webosbrew/webos-homebrew-channel) readme. A key was not installed.

`luna-send` as root reaches SettingsService, then the parent exits immediately. The JSON arrives in a redirected file about a second later. Waiting about three seconds, then reading the file, returns the payload. `{"keys":["option"]}` with no category comes back as “There is no matched result from DB”. The working shape is `category` plus `keys`.

`systemprefs.db` is the old Palm table, 36 rows, and does not hold these keys. They live in `/var/luna/preferences/option` and `general`, each with an `.md5` sibling. `check_settings_db_health.sh` can factory-reset settings. It was not run.

## Settings readback

Copies of `option`, `general`, and both md5 files were saved at `/home/root/debloat-backup` before the write. Both calls returned `{"returnValue":true}`. A second read at 12:20 local matched this table. `personalRecommend` also flipped `changedByUser` to true.

```
luna-send -n 1 -w 8000 luna://com.webos.settingsservice/setSystemSettings \
  '{"category":"option","settings":{"livePlus":"off","channelplus":"off","watchedListCollection":"off","webOSPromotionVideo":"off","demoMode":"off"}}'

luna-send -n 1 -w 8000 luna://com.webos.settingsservice/setSystemSettings \
  '{"category":"general","settings":{"adCookie":"off","homeAutoLaunch":"off","homePromotion":"off","personalRecommend":{"changedByUser":true,"value":"off"}}}'
```

| key | after |
|---|---|
| livePlus | OFF · option · was on |
| channelplus | OFF · option · was on |
| webOSPromotionVideo | OFF · option · was on |
| watchedListCollection | OFF · option · was on |
| demoMode | OFF · option · was on |
| homePromotion | OFF · general · was on |
| homeAutoLaunch | OFF · general · was on |
| adCookie | OFF · general · was on |
| personalRecommend | OFF · general · value was on |
| quickStartMode | HELD · on · Homebrew icon |
| storeMode | HELD · home |
| storeMode2 | HELD · on |
| storeModeVideo | HELD · off |
| customizedAd | HELD · already off |
| lmt | HELD · already off |

`sam` exposes `updateLaunchPoint` and `removeLaunchPoint`. No hide call was made. The launcher list taken before the write still included LG Channels, LG Content Store, Search, and the streaming stubs. Whether the home screen drops the LG Channels tile after `channelplus` goes off was not rechecked.

## Left in place

| | |
|---|---|
| firmware packages | KEPT · rootfs was not remounted for removal |
| streaming stubs | KEPT · Netflix, Prime, Disney+, Hulu, Sling |
| Developer Mode | UNINSTALLED · do not install it again while rooted |
| locale and alerts | KEPT · zip, country, language, pointer, emergency alert, last input |

## Sources

| | |
|---|---|
| dejavuln-autoroot | [repo](https://github.com/throwaway96/dejavuln-autoroot) · [v0.0.10](https://github.com/throwaway96/dejavuln-autoroot/releases/tag/v0.0.10) · USB procedure and Homebrew install |
| Jacob Clayden | [jacobcx.dev](https://jacobcx.dev/) · credited with finding DejaVuln |
| Bitdefender | [LG webOS write-up](https://www.bitdefender.com/blog/labs/vulnerabilities-identified-in-lg-webos/) · credited in the same script |
| Homebrew Channel | [repo](https://github.com/webosbrew/webos-homebrew-channel) · [v0.7.2](https://github.com/webosbrew/webos-homebrew-channel/releases/tag/v0.7.2) |
| webOS Homebrew | [Rooting](https://www.webosbrew.org/rooting/) · [exploits](https://www.webosbrew.org/rooting/exploits/) |
| faultmanager | [repo](https://github.com/throwaway96/faultmanager-autoroot) · read, not run |
| crashd | [instructions](https://gist.github.com/throwaway96/e811b0f7cc2a705a5a476a8dfa45e09f) · webOS 4+ only |
| RootMy.TV | [rootmy.tv](https://rootmy.tv/) · patched for this firmware age |
| webos-debloat | [CasperDkk gist](https://gist.github.com/CasperDkk/7520efa763d9e23c531c2e8564f9bb4e) · webOS 26 map, not run |
| set aside | [cleanyourtv](https://github.com/lasharor/cleanyourtv-WebOS) · [glasshouse](https://github.com/rorygallagher2024/glasshouse) · [webos-vibehack](https://github.com/OmkarK97/webos-vibehack) |
| lg-webos1-hub | [repo](https://github.com/0xbyt4/lg-webos1-hub) · SSAP client only |
