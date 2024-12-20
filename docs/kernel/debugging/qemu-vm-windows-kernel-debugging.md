---
description: set up 2 qemu vm's (host, target) for windows debugging.
---

# qemu vm windows kernel debugging

add `<vendor_id state="on" value="KVMKVMKVM"/>` to hyperv block of virt-manager xml (see [link](https://www.binary-zone.com/2021/07/19/windows-kernel-debugging-using-two-vms-on-linux/)) in target vm (host doesn't need this)

copy kdnet.exe and VerifiedNICList.xml (not 100% sure if needed) from host (C:\Program Files (x86)\Windows Kits\10\Debuggers\x64) to target (c:\KDNET)

make sure host and target can ping each other

open ps as admin and run `.\kdnet.exe` to make sure your nic is supported.

run `.\kdnet.exe <host ip> <port>` (recommended port range is 50000-50039). it will print a key

open windbg on host and open kernel debugging. enter the port you selected and the key and connect.

restart target `shutdown -r -t 0`

enjoy the fact you didn't spend 5+h to troubleshoot the connection. (hopefully)

## source debugging

```
0: kd> .symfix // windows debugging symbols
0: kd> .sympath+ <c:\path\to\project>
0: kd> .reload /f
0: kd> ed nt!Kd_Default_Mask 0xf // get debugging messages
```

## resources

[msdn kdnet](https://learn.microsoft.com/en-us/windows-hardware/drivers/debugger/setting-up-a-network-debugging-connection-automatically)

[windows debugging on qemu](https://www.binary-zone.com/2021/07/19/windows-kernel-debugging-using-two-vms-on-linux/)

[msdn source debugging](https://learn.microsoft.com/en-us/windows-hardware/drivers/debugger/debug-universal-drivers---step-by-step-lab--echo-kernel-mode-#work-with-breakpoints-and-source-code)
