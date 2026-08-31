# Deploy kineworld.com

GitHub `zoahdev/kineworld-site` is source of truth. The live domain is on Aliyun OSS.
Until this folder is synced, `/vs-baize.html` and `/exp002.html` exist only on GitHub.

```
# after installing ossutil and configuring the bucket
ossutil cp -r . oss://<bucket>/ --exclude ".git/*" --exclude "DEPLOY.md"
```

Required public files: index.html, report.html, vs-baize.html, exp002.html, kine-bench.html, leaderboard.html, assets/*.
