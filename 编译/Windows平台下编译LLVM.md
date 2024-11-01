# Windows平台下编译LLVM(以LLVM14为例)

0. 以**管理员**身份运行"适用于 VS 2022 的开发人员命令提示符".此命令提示符提供指向 Visual Studio 和已安装工具的正确路径和环境变量.然后注册 Microsoft 调试接口访问 (DIA) DLL
```sh
regsvr32 "%VSINSTALLDIR%\DIA SDK\bin\msdia140.dll"
regsvr32 "%VSINSTALLDIR%\DIA SDK\bin\amd64\msdia140.dll"
```

1. 克隆llvm仓库到本地目录并选择14版本分支
```sh
git clone https://github.com/llvm/llvm-project.git
git checkout release/14.x
```

2. 安装CMake 版本 >= 3.10
```sh
scoop install cmake(如果已经安装了scoop)
否则去到 https://cmake.org/download/
```
<div align=center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="image.png">
</div>

3. 打开CMake-gui
选择LLVM仓库下的llvm子目录

设置
```sh
LLVM_TARGETS_TO_BUILD=X86(这里仅以X86平台为例)
LLVM_ENABLE_LIBXML2=OFF
LLVM_ENABLE_ZLIB=OFF
CMAKE_INSTALL_REFIX=C:/LLVM14
```
然后点Configure和Generate生成LLVM的VS工程

如果是其他指令集架构参考以下
<div align=center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="image-2.png">
</div>

4. 打开LLVM.sln,选择Release模式
<div align=center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="image-3.png">
</div>


4. 生成INSTALL项目会自动安装到C:\LLVM14目录下
<div align=center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="image-1.png">
</div>

[https://llvm.gnu.ac.cn/docs/CMake.html](https://llvm.gnu.ac.cn/docs/CMake.html)

[https://llvm.gnu.ac.cn/docs/GettingStartedVS.html](https://llvm.gnu.ac.cn/docs/GettingStartedVS.html)