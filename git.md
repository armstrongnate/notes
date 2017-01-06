# Notes on Git

## Quickly resolve merge conflicts

```bash
grep -lr '<<<<<<' . | sed -e 's/\(.*\)/"\1"/' | xargs git checkout --ours
```
