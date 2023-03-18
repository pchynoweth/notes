# Debugging

## Generic C & C++ Binary Debugging on Linux

You can debug binary by including a launch.json in the .vscode directory.

```js
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "C++ Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/builddir/${relativeFileDirname}/${fileBasenameNoExtension}",
            "args": [],
            "envFile": "${workspaceFolder}/gst.env",
            "environment": [{ "name": "config", "value": "Debug" }],
            "cwd": "${workspaceFolder}/builddir"
        }
    ]
}
```

There are a couple of limitataions.

1. The executable needs to be run from the source file containing the main function.
2. The binary name needs to be based on the source file name.

In the above example the environment is sourced from a file.  This is needed for gstreammer source because it uses a virtual environment.
