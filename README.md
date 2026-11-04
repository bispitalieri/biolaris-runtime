# biolaris-runtime

Standalone project for generating platform runtime assets used by Biolaris
Media. It downloads the tools for the machine running the command, verifies
published SHA-256 checksums, smoke-tests each executable, and writes the
finished runtime under `dist/<rust-target-triple>/`.

## Requirements

- Node.js 20 or newer
- Rust and Cargo (used to install the unpins installer on macOS and Linux)
- `tar` on macOS and Linux; Windows uses the downloaded 7-Zip extractor
- An internet connection

## Download a runtime

Run on a native machine matching the desired runtime:

```sh
npm run binaries:download
```

The downloader detects the host target and creates its runtime directory and
`runtime-manifest.json` in `dist/`. To download again and replace the existing
files:

```sh
npm run binaries:download -- --force
```

The optional `--target <rust-target-triple>` must match the host; cross-platform
downloads are rejected. Run the downloader on each platform you intend to
distribute.

Supported targets:

| Target | Tools |
| --- | --- |
| `x86_64-pc-windows-msvc` | FFmpeg, FFprobe, ImageMagick, Poppler, yt-dlp, libvips |
| `x86_64-apple-darwin` | FFmpeg, FFprobe, ImageMagick, Poppler, yt-dlp |
| `aarch64-apple-darwin` | FFmpeg, FFprobe, ImageMagick, Poppler, yt-dlp |
| `x86_64-unknown-linux-gnu` | FFmpeg, FFprobe, ImageMagick, Poppler, yt-dlp |

libvips is not included on macOS or Linux because there is no supported
standalone build configured for those platforms. The generated manifest records
the target, included tools, download sources, versions, and available checksums.

## Distribution and licenses

Each `dist/<target-triple>/` directory is a self-contained runtime folder that
can be archived and published as a platform-specific download. Keep its
`runtime-manifest.json` with the binaries. The tools are separate programs with
their own licenses; review the licenses and codec configuration of FFmpeg,
ImageMagick, Poppler, yt-dlp, libvips, and their dependencies before
redistribution. The project downloader is licensed under AGPL-3.0-only.

The repository's **Biolaris runtime assets** GitHub Actions workflow runs on
runtime-project changes, weekly, or by manual dispatch. It builds each
supported target on a native runner, packages the result with a SHA-256
checksum, and publishes the assets to the `runtime-latest` prerelease. The
main app downloader fetches and verifies the matching asset before falling
back to individual upstream downloads when no asset has been published yet.
Use **Actions → Biolaris runtime assets → Run workflow** to regenerate all
platform bundles on demand.
