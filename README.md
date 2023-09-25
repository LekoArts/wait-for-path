# Wait for Path

This is a fork of [aleclarson/wait-for-path](https://github.com/aleclarson/wait-for-path) to update dependencies and do the occasional fix.

> Efficiently wait for a path to exist (cross-platform)

## Usage

### Programmatic

```shell
npm install @lekoarts/wait-for-path
```

```ts
import { waitForPath } from "@lekoarts/wait-for-path"

waitForPath("./foo/bar/baz").then(stat => {
  console.log(stat); // fs.Stat object
})
```

## How it's implemented

No polling. This library uses the `fs.watch` API Node.js provides which is an abstraction over `inotify(7)` on Linux, `kqueue(2)` and `FSEvents` on MacOS, and `ReadDirectoryChangesW` on Windows systems. Read more about it here in the official NodeJS documentation: https://nodejs.org/docs/latest/api/fs.html#fs_caveats. Platforms which do not support the `fs.watch` API at all will not work with this library. Eventually a fallback polling approach could be implemented for those platforms.

`wait-for-path` attaches a listener for each segment of the path until it encounters a path segment that does not exist. Whenever any of these watchers reports a change, all watchers are closed, `fs.stat` is attempted on the full path and if the path still does not exist watchers are added again starting at the root directory.
