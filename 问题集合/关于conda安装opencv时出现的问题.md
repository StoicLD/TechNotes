# 关于conda安装OpenCV时候出现的问题

其实就是安装的时候，提示

qt的一个包一直安装不成功。

详细的看失败log。发现有

说没有找到`chcp`这个命令。

查找资料后得知，这个是windows/System32目录下的一个命令。

然而我的电脑上只有`chcp.com`这个可执行文件。

事实证明，`chcp.com`应该就是`chcp`，所以我在System32目录下直接复制了

`chcp.com`然后改名为`chcp.exe`。解决了问题。