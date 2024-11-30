# Linux kernel notes

refs: [learningjourney](https://thelearningjourneyebooks.com/)

## Syscalls

refs: [s0](https://blog.packagecloud.io/the-definitive-guide-to-linux-system-calls/)

### `int 0x80`/iret (legacy syscalls)

linux needs 32-bit emulation parameter [CONFIG_IA32_EMULATION](https://cateee.net/lkddb/web-lkddb/IA32_EMULATION.html)
it's a slow path to execute a system call. syscall and sysenter should be
preferred unless not available.

### sysenter/sysexit

you should always use __kernel_vsyscall (vdso) to call sysenter as the
convention is likely to change.

kernel must write msr (segment selector, instruction pointer, stack pointer)
to specify how sysenter works.

### syscall/sysret

syscall loads rip from LSTAR msr register.

### virtual dynamic shared object (VDSO)

special memory area that is mapped into the address space of a user-space
process at a fixed address. It is used to reduce the cost of making system
calls. libc searches for the VDSO and uses it to make system calls if present.
VDSO allows for vsyscalls, which are "system calls" that run in user space.
examples are `gettimeofday` and `getpid`. these are faster than traditional
system calls because they don't require a context switch.

## uefi

refs: [osdev/uefi](https://wiki.osdev.org/UEFI)
