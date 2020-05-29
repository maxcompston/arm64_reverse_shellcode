cmake_minimum_required (VERSION 2.8)
project (CMakeSC)

#version number
set (CMakeSC_VERSION_MAJOR 1)
set (CMakeSC_VERSION_MINOR 0)

#indicate the entry point for the executable
add_executable (reverse-shellcode-mapped-ipv4 reverse-shellcode-ipv4.c)
add_executable (reverse-shellcode-mapped-ipv6 reverse-shellcode-ipv6.c)

install (TARGETS reverse-shellcode-mapped-ipv4 DESTINATION bin)
install (TARGETS reverse-shellcode-mapped-ipv6 DESTINATION bin)
