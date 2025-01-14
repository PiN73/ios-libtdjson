# ios-libtdjson

[![Version](https://img.shields.io/cocoapods/v/libtdjson.svg?style=flat)](https://cocoapods.org/pods/libtdjson)
[![License](https://img.shields.io/cocoapods/l/libtdjson.svg?style=flat)](https://cocoapods.org/pods/libtdjson)
[![Platform](https://img.shields.io/cocoapods/p/libtdjson.svg?style=flat)](https://cocoapods.org/pods/libtdjson)

## Lib versions

| pod   | tdlib |
| ----- | ----- |
| 0.3.1 | [1.8.14](https://github.com/tdlib/td/tree/8517026415e75a8eec567774072cbbbbb52376c1) |
| 0.3.0 | [1.8.7](https://github.com/tdlib/td/tree/a7a17b34b3c8fd3f7f6295f152746beb68f34d83) |
| 0.2.2 | [1.8.1](https://github.com/tdlib/td/tree/92c2a9c4e521df720abeaa9872e1c2b797d5c93f) |
| 0.2.1 | [1.7.9](https://github.com/tdlib/td/tree/7d41d9eaa58a6e0927806283252dc9e74eda5512) |
| 0.2.0 | [1.7.0](https://github.com/tdlib/td/tree/v1.7.0) |

## Supported architectures

| Platform      | Architecture |     |
| ------------- | ------------ | --- |
| iOS           | armv7        | ❌   |
|               | armv7s       | ❌   |
|               | arm64        | ✅   |
| iOS simulator | i386         | ❌   |
|               | x86_64       | ✅   |
|               | arm64 (M1)   | ✅   |
| macOS         | i386         | ❌   |
|               | x86_64       | ✅   |
|               | arm64 (M1)   | ✅   |

## Installation

### CocoaPods

libtdjson is available through [CocoaPods](https://cocoapods.org). To install it, simply add the following line to your Podfile:

```ruby
pod 'libtdjson'
```

or add it to your .podspec file:

```ruby
Pod::Spec.new do |s|
  s.dependency 'libtdjson'
end
```

### Use it as module (iOS, swift)

Because this pod **only** provide .dylib files (to prevent module name conflicts and keep it simplest!), if you want to use it as module (e.q. on iOS with swift), you **have to** add some necessary files:

- Download example `headers` and `module.modulemap`

```bash
curl -SLO https://github.com/up9cloud/ios-libtdjson/releases/download/v0.2.2/cocoapod_modulemap.tar.gz
mkdir include
tar xzf cocoapod_modulemap.tar.gz -C include

# Edit files to whatever you want, e.q. change the module name or remove export symbols you don't need
```

- Add include path and link lib, e.q.

```ruby
Pod::Spec.new do |s|
  s.pod_target_xcconfig = {
    'SWIFT_INCLUDE_PATHS' => '${PODS_TARGET_SRCROOT}/include',
    'OTHER_LDFLAGS' => '-ltdjson',
  }
end
```

- Use it

```swift
import libtdjson
func create() -> Int {
    return Int(bitPattern: libtdjson.td_json_client_create()!)
}
// ... (more usages at ./example/*)
```

### Carthage

TODO:

### Manually

Download prebuilt files from `Release`, then do whatever you want.

## Q&A

> An error was encountered processing the command (domain=FBSOpenApplicationServiceErrorDomain,code=1):

The app will crash if identification name of .dylib isn't correct

```bash
# check id
otool -D libtdjson.dylib

# fix id
install_name_tool -id @rpath/libtdjson.dylib libtdjson.dylib
```

## TODO

- [ ] Package static lib for App Store
- [ ] Support [Carthage](https://github.com/Carthage/Carthage/blob/master/Documentation/Artifacts.md#cartfile)
- [x] Support M1 (Apple Silicon) - migrate to XCFramework, see [PR 1620](https://github.com/tdlib/td/pull/1620)

## Dev memo

> Bump the TDLib version

- Modify the commit id for tdlib in `./build.sh`
- Update the `Lib versions` part in `./README.md`
- Commit and add tag `git tag vx.x.x`
- Push
- Wait for CI build task

> Manually do `pod trunk push` if CI build failed...

```bash
export GITHUB_REF=refs/tags/<the version>
pod trunk push --allow-warnings libtdjson.podspec
pod trunk push --allow-warnings flutter_libtdjson.podspec
```

> what if need commit more fixing things...

```bash
git push --delete origin <the version tag>
git tag -d v0.3.0
git add .
git commit -m "..."
git tag -d <the version tag>
git push && git push --tags
```
