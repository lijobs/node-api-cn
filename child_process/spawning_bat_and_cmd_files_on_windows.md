
[`child_process.exec()`] 和 [`child_process.execFile()`] 之间区别的重要性可能因平台而异。
在 Unix 类型的操作系统（Unix、Linux、macOS）上，[`child_process.execFile()`] 可以更高效，因为默认情况下它不会衍生 shell。
但是在 Windows 上，`.bat` 和 `.cmd` 文件在没有终端的情况下不能自行执行，因此无法使用 [`child_process.execFile()`] 启动。
当在 Windows 上运行时，要调用 `.bat` 和 `.cmd` 文件，可以使用设置了 `shell` 选项的 [`child_process.spawn()`]、或 [`child_process.exec()`]、或衍生 `cmd.exe` 并将 `.bat` 或 `.cmd` 文件作为参数传入（也就是 `shell` 选项和 [`child_process.exec()`] 所做的）。
在任何情况下，如果脚本的文件名包含空格，则需要加上引号。

```js
// 仅在 Windows 上。
const { spawn } = require('child_process');
const bat = spawn('cmd.exe', ['/c', 'my.bat']);

bat.stdout.on('data', (data) => {
  console.log(data.toString());
});

bat.stderr.on('data', (data) => {
  console.error(data.toString());
});

bat.on('exit', (code) => {
  console.log(`子进程退出，退出码 ${code}`);
});
```

```js
// 或：
const { exec } = require('child_process');
exec('my.bat', (err, stdout, stderr) => {
  if (err) {
    console.error(err);
    return;
  }
  console.log(stdout);
});

// 文件名中包含空格的脚本：
const bat = spawn('"my script.cmd"', ['a', 'b'], { shell: true });
// 或：
exec('"my script.cmd" a b', (err, stdout, stderr) => {
  // ...
});
```

