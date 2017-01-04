# Notes on XVim

## Codesign

```bash
security find-identity -p codesigning
codesign -f -s <identity without quotes> -v /path/to/Xcode.app
```
