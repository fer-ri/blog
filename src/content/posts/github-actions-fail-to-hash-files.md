---
title: "Github Actions: Fail to hash files under directory"
description: Resolved GitHub action failure due to hashFiles issue. Updated key for composer lock files to address directory hash failure.
publishDate: 2023-12-29
tags: [github, ci/cd, composer]
---

One of my GitHub repository actions suddenly failed with the following error:

```bash
Error: The template is not valid. .github/workflows/run-tests.yml (Line: 38, Col: 16): hashFiles('**/composer.lock') failed.
Fail to hash files under directory '/home/runner/work/lims/lims'
```

After investigating, I came across a relevant [Github issue](https://github.com/actions/runner/issues/449). Following several attempts, I managed to resolve the issue.

```diff
- key: ${{ runner.os }}-composer-${{ hashFiles('**/composer.lock') }}

+ key: ${{ runner.os }}-composer-${{ hashFiles('composer.lock', '*/composer.lock') }}
```

It appears that `hashFiles` may encounter failures when numerous files exist in the working directory.
