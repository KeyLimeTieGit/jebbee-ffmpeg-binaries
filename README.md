# jebbee-ffmpeg-binaries

Pinned FFmpegKit native binaries used by the Jebbee mobile app. Upstream FFmpegKit was retired, so the app downloads these exact files from this repo's releases and verifies their SHA-256 before using them.

Do not replace an asset in an existing release. Publish a new release and update the pinned URL and hash in the app.

## Release `v6.0-jebbee.1`

| Asset | SHA-256 |
|---|---|
| `ffmpeg-kit-full-gpl-6.0-2-16kb-jebbee.1.aar` | `01814d77998447b22bebe556d92250f63ea053677634963c863cd0c73d04b7c2` |
| `ffmpeg-kit-ios-full-gpl-6.0-80adc.zip` | `00af5c43a67fbf82f117da6058364af2d28e37f82db5a8460565eba719468ce3` |

### Android AAR

- Source: `ffmpeg-kit-main-full-gpl-ndk-r27-16k.aar` from release `ffmpeg-kit-android-full-gpl-2026-02-27T04-59-58` of `JamaisMagic/ffmpeg-kit-16KB` (FFmpegKit 6.0, full-gpl, NDK r27, 16 KB page size).
- Patched: `libavdevice.so` (and `libavdevice_neon.so` for `armeabi-v7a`) references hidapi (`PLATFORM_hid_*`) and OpenSL ES symbols without linking a library that provides them, so Android refuses to load it and the app crashes at startup. Each of those libraries got `libffmpeghidstub.so` added as a `DT_NEEDED` entry (`patchelf --add-needed libffmpeghidstub.so`). The stub is not in this AAR: the app builds it from `android/app/src/main/cpp/ffmpeghidstub.c` and ships it in `jniLibs`; it defines the hidapi symbols as no-ops and links `libOpenSLES.so`.
- Repacked with deflate compression (the source stores the `.so` files uncompressed). Every entry has the same CRC and size as the patched archive.

### iOS xcframeworks

- Source: the `latest` tag archive of `NooruddinLakhani/ffmpeg-kit-ios-full-gpl` (tag commit `8006263515e85a382052fd6388ed57b6a019811c`), unmodified. Top-level directory inside the zip: `ffmpeg-kit-ios-full-gpl-latest/`.

## License

These are FFmpegKit **full-gpl** builds. They include GPL components (for example `libx264`), so the binaries are distributed under the GPL v3.0. The corresponding source is FFmpegKit 6.0 (`arthenica/ffmpeg-kit`) and the FFmpeg and external library versions it pins, plus the patch described above.
