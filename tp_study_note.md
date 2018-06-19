## tp study note

### Hook

Ring3层下的Hook包括消息钩子和API Hook，消息钩子又分为全局消息钩子和进程内消息钩子，API Hook又分为IAT Hook和API Inline Hook。

内核下Hook就比较多了，比如Object Hook，SSDT Hook，SSDT Shadow Hook，Inline Hook，IDT Hook，SysEnter Hook等等。

### PatchGuard
PatchGuard就是Windows Vista的内核保护系统，防止任何非授权软件试图“修改”Windows内核，也就是说，Vista内核的新型金钟罩。
x64下因为有PatchGuard的限制，很多保护都被巨硬给抹掉了。
比如SSDT Hook Inline Hook 所以TP无法继续使用这些保护手段了。
除非腾Xun冒着被巨硬吊销数字签名的风险来阻止我们调试。
我曾经在看雪论坛里看过有个人写的文章，它亲自测试在x64环境下清零调试端口，结果发生了蓝屏，
所以在x64下TP是不会清零的。这样就省了不少的事情。
但是自从那次TP大更新后新加入了ValidAccessMask清零。
那么在ring0中 TP除了ValidAccessMask清零，还有反双机调试，这里不是我们讨论的范畴。
我们只是讨论如何才能正常调试游戏，于是在内核层我们只要解决ValidAccessMask清零就可以了。

### ValidAccessMask
调试权限（ValidAccessMask）的清零
这个标志位其实是DebugObject （调试对象）的权限，如果为0将导致无法创建调试对象（缺少权限,常见的情况就是OD无法附加任何进程。
这个DebugObject在很多调试函数中都有用到 ，比如NtCreateDebugObject就有需要访问调试对象。
所以要想办法恢复这个标志位。
我们有三种选择：1、自己恢复原来的值 2、找到TP清零的位置Nop掉 3、移位(重建DebugObject)
这里我选择了第一种。