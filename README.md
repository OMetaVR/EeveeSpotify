> **Archived.** This pipeline moved to [OMetaVR/EeveeSpotifyReincarnated](https://github.com/OMetaVR/EeveeSpotifyReincarnated), a fork built by the shared [ipa-pipeline](https://github.com/OMetaVR/ipa-pipeline). Old releases stay here.

# EeveeSpotify builds

Automatic IPA builds of [EeveeSpotifyReincarnated](https://github.com/SideloadLabs/EeveeSpotifyReincarnated), the maintained continuation of whoeevee's EeveeSpotify (EeveeSpotifyReborn was archived in December 2025). Each [release](../../releases) has:

| File | For |
| --- | --- |
| `EeveeSpotify_<spotify>_v<tweak>_sideload.ipa` | SideStore, AltStore, LiveContainer, Sideloadly, signing services |
| `EeveeSpotify_<spotify>_v<tweak>.ipa` | TrollStore (keeps CarPlay/Siri) |
| `*_iphoneos-arm64.deb` / `*_iphoneos-arm64e-roothide.deb` | Rootless / RootHide jailbreak, mirrored from upstream's release |

The sideload IPA has upstream's zxPluginsInject added, which is what upstream recommends for non-jailbroken installs. Upstream doesn't publish a rootful `.deb`.

Also available in the [Meta's Apps source](https://ometavr.github.io/ipa-source/source.json).

## How it works

Same pipeline as [OMetaVR/uYouPlus](https://github.com/OMetaVR/uYouPlus):

```
Watch (every 3h, Ubuntu)
  upstream published a new release?  ── no ──▶ done
        │ yes
  decrypted Spotify <version it targets> in the base-ipas draft?
        │ no ──▶ opens a "Decrypted Spotify X needed" issue (you get notified)
        │ yes
Build (macOS, ~10 min)
  upstream's released .deb + patches/*.patch + base IPA → 2 IPAs + .debs → GitHub release
        │
ipa-source picks up the new release within the hour
```

- **Target version:** upstream's README states the supported Spotify version (`**Spotify Version:** \`9.1.88\``); `config.json` → `targetVersion` says where to read it. It's read from the default branch, because upstream bumps it there after releases without code changes (v7.0.0's tag says 9.1.84, Master says 9.1.88).
- **Nothing is compiled except zxPluginsInject.** The tweak itself is upstream's own prebuilt `.deb`, the same input as upstream's `build-and-release-yourself.yml`.
- **Base IPAs** live in the private `base-ipas` draft release.
- Failed builds open an issue and aren't retried automatically; an upstream change to `build-and-release-yourself.yml` opens an issue too.

## Supplying a decrypted IPA

```sh
scripts/push-ipa.py ~/Downloads/Spotify.ipa
```

It checks the bundle ID (`com.spotify.client`) and that the binaries are decrypted, uploads it as `Spotify_<version>.ipa`, and runs the watcher. `--no-build` only uploads. To build a different Spotify version than upstream targets, upload with `--no-build`, then run **Actions → Build** with `spotify_version` set.

## Customizing

`patches/*.patch` are applied to upstream (with `git apply`) before building, e.g. to change `Tools/alt-icons.sh` or the zxPluginsInject module.
