# Notes on XVim

## Codesign

```bash
security find-identity -p codesigning # iPhone Distribution: Nathan Armstrong
codesign -f -s <identity without quotes> -v /path/to/Xcode.app
```

## Install

```bash
sudo xcode-select -s /path/to/Xcode.app
make
```

## Xcode UUID

```
defaults read /path/to/Xcode.app/Contents/Info DVTPlugInCompatibilityUUID
```
