# Intellisense

## C & C++

If the C/C++ extension is installed intellisense can be enabled by adding a c_cpp_properties.json to the .vscode directory.  If you have a compile commands file, intellisense should work.

```json
{
    "configurations": [
        {
            "name": "Linux",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [],
            "compilerPath": "/usr/bin/clang",
            "cStandard": "c17",
            "cppStandard": "c++14",
            "intelliSenseMode": "linux-clang-x64",
            "compileCommands": "${workspaceFolder}/builddir/compile_commands.json"
        }
    ],
    "version": 4
}
```
