---
description: set up 2 qemu vm's (host, target) for windows debugging.
---

# qemu vm windows kernel debugging

{% embed url="https://learn.microsoft.com/en-us/windows-hardware/drivers/debugger/setting-up-a-network-debugging-connection-automatically" %}
main windows article on the topic. also read link below when you realize that it's not working
{% endembed %}

{% embed url="https://www.binary-zone.com/2021/07/19/windows-kernel-debugging-using-two-vms-on-linux/" %}
link that saved me from pulling my hair out
{% endembed %}

add `<vendor_id state="on" value="KVMKVMKVM"/>` to hyperv block of virt-manager xml (see link above) in target vm (host doesn't need this)

copy kdnet.exe and VerifiedNICList.xml (not 100% sure if needed) from host (C:\Program Files (x86)\Windows Kits\10\Debuggers\x64) to target (c:\KDNET)

make sure host and target can ping each other

open ps as admin and run `.\kdnet.exe` to make sure your nic is supported.

run `.\kdnet.exe <host ip> <port>` (recommended port range is 50000-50039). it will print a key

open windbg on host and open kernel debugging. enter the port you selected and the key and connect.

restart target `shutdown -r -t 0`

enjoy the fact you didn't spend 5+h to troubleshoot the connection. (hopefully)
