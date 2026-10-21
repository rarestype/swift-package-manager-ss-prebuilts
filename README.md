# swift-package-manager-ss-prebuilts

Builds [SwiftPM](https://github.com/swiftlang/swift-package-manager) from
[rarestype/swift-package-manager](https://github.com/rarestype/swift-package-manager), and
signed [swift-syntax](https://github.com/swiftlang/swift-syntax) prebuilts for macros, for
each Swift release we use.

The fork carries [swiftlang/swift-package-manager#10404](https://github.com/swiftlang/swift-package-manager/pull/10404),
so that builds of a single product (`swift build --product`, `swift package js`) use
prebuilt swift-syntax instead of compiling it, and it fetches prebuilts from
`https://get.rarestype.com/prebuilts`, trusting [`Certificates/root.cer`](Certificates/root.cer)
by default.

## Releases

Each file in [`Releases/`](Releases) pins a fork commit and the swift-syntax versions to
build prebuilts for. Running the **Deploy** workflow for a release:

1.  builds SwiftPM natively on x86_64 and aarch64 Linux, against the dependency revisions
    tagged for that toolchain, and builds unsigned prebuilts with the toolchain’s own compiler,
2.  signs the prebuilts,
3.  installs each SwiftPM over a toolchain and checks that it accepts the signed prebuilts,
4.  publishes the prebuilts to `get.rarestype.com/prebuilts/swift-syntax/<version>/`, never
    replacing one that is already published, and SwiftPM as a release named
    `swift-<release>-<commit>`.

Each SwiftPM archive installs over a toolchain’s `usr`, and keeps the license files of every
package linked into it under `usr/share/swift-package-manager/licenses/`, at their original
paths.

## Moving to a new Swift release

1.  Rebase our commits onto the new release tag as a new branch in the fork, such as
    `rarest/6.4.2`. Never force-push a branch that a release pins.
2.  Add `Releases/<release>` pinning the new branch’s head, in a pull request.
3.  Run **Deploy** for the new release, then use the new archive’s checksum in the images.
