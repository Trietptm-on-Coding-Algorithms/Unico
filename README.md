# Unico

Unicorn PE function runner

## Installation

At first, install [Unicorn Engine](http://www.unicorn-engine.org/).
Then, install required PyPI packages by `pip install -r requirements.txt`.

## Usage

```
$ python unico.py
Usage: python unico.py PE_FILE [ADDR_START ADDR_END]
```

If `ADDR_START` is omitted, it starts from `AddressOfEntryPoint`.
If `ADDR_END` is omitted, it tries to exit when the function returns.
To get the better result, you need to narrow the emuration range.

## Dump and diff the memory states

Unico outputs the below 4 files.

* `pe_image_{before,after}.dump`: memory state of PE image range at the beginning/end of the emuration
* `pe_image.diff`: diff of above
* `stack.dump`: memory state of stack range at the end of the emuration

Also, it outputs the register values to stdout at the end of the emuration.

## Trace memory accesses and IAT calls

The example below traces IAT calls and examines memory WRITEs to sockaddr struct specified as the 2nd argument of connect().

```
$ python unico.py Release.exe >trace.log

$ grep IAT trace.log
[0x2000004] IAT function is being CALL, GetSystemTimeAsFileTime(0x117f7ec, 0x0, 0x0, 0xdddddddd, 0xdddddddd, 0x0) = 0x1
[0x2000004] IAT function is being CALL, GetCurrentThreadId(0x117f7ec, 0x0, 0x0, 0xdddddddd, 0xdddddddd, 0x0) = 0x1
[0x2000004] IAT function is being CALL, GetCurrentProcessId(0x117f7ec, 0x0, 0x0, 0xdddddddd, 0xdddddddd, 0x0) = 0x1
[0x2000004] IAT function is being CALL, QueryPerformanceCounter(0x117f7e4, 0x117f7ec, 0x0, 0x0, 0xdddddddd, 0xdddddddd) = 0x1
[0x2000004] IAT function is being CALL, IsProcessorFeaturePresent(0xa, 0x0, 0xdddddddd, 0xdddddddd, 0xdddddddd, 0xdddddddd) = 0x1
[0x2000004] IAT function is being CALL, _initterm_e(0x402110, 0x40211c, 0x8, 0x117f7e4, 0x117f7ec, 0x0) = 0x0
[0x2000004] IAT function is being CALL, _initterm(0x402104, 0x40210c, 0x8, 0x117f7e4, 0x117f7ec, 0x0) = 0x1
[0x2000004] IAT function is being CALL, __p___argv(0x8, 0x117f7e4, 0x117f7ec, 0x0, 0x117f700, 0x0) = 0x1
[0x2000004] IAT function is being CALL, __p___argc(0x8, 0x117f7e4, 0x117f7ec, 0x0, 0x117f700, 0x0) = 0x1
[0x2000004] IAT function is being CALL, _get_initial_narrow_environment(0x8, 0x117f7e4, 0x117f7ec, 0x0, 0x117f700, 0x0) = 0x1
[0x2000004] IAT hook: memset 0x117d7a0 0x0 0x2000
[0x2000004] IAT function is being CALL, memset(0x117d7a0, 0x0, 0x2000, 0x1, 0xdddddddd, 0xdddddddd) = 0x117d7a0
[0x2000004] IAT function is being CALL, WSAStartup(0x202, 0x117d608, 0x1, 0xdddddddd, 0xdddddddd, 0x117d7a0) = 0x1
[0x2000004] IAT function is being CALL, WSASocketW(0x2, 0x1, 0x0, 0x0, 0x0, 0x0) = 0x1
[0x2000004] IAT hook: getaddrinfo 'www.example.com:http'
[0x2000004] IAT function is being CALL, getaddrinfo(0x402140, 0x402138, 0x0, 0x117d5fc, 0x2, 0x1) = 0x0
[0x2000004] IAT hook: socket connect '0.0.0.0:5889'
[0x2000004] IAT function is being CALL, WSAConnect(0x1, 0x0, 0x10, 0x0, 0x0, 0x0) = 0x0
[0x2000004] IAT hook: socket send 'GET / HTTP/1.0\r\nHost: www.example.com\r\n\r\n'
[0x2000004] IAT function is being CALL, WSASend(0x1, 0x117d600, 0x1, 0x0, 0x0, 0x0) = 0x0
[0x2000004] IAT function is being CALL, Sleep(0x3e8, 0x1, 0x117d600, 0x1, 0x0, 0x0) = 0x1
[0x2000004] IAT function is being CALL, WSARecv(0x1, 0x117d600, 0x1, 0x0, 0x117d79c, 0x0) = 0x1
[0x2000004] IAT function is being CALL, puts(0x117d7a0, 0x1, 0x117d600, 0x1, 0x0, 0x117d79c) = 0x1
[0x2000004] IAT function is being CALL, closesocket(0x1, 0x1, 0x117d600, 0x1, 0x0, 0x117d79c) = 0x1
[0x2000004] IAT function is being CALL, WSACleanup(0x1, 0x1, 0x117d600, 0x1, 0x0, 0x117d79c) = 0x1
[0x2000004] IAT hook: memset 0x117d7a0 0x0 0x2000
[0x2000004] IAT function is being CALL, memset(0x117d7a0, 0x0, 0x2000, 0x1, 0xdddddddd, 0x2000) = 0x117d7a0
[0x2000004] IAT function is being CALL, WSAStartup(0x202, 0x117d600, 0x1, 0xdddddddd, 0x2000, 0x117d7a0) = 0x1
[0x2000004] IAT function is being CALL, WSASocketW(0x2, 0x1, 0x0, 0x0, 0x0, 0x0) = 0x1
[0x2000004] IAT hook: gethostbyname 'www.example.com'
[0x2000004] IAT function is being CALL, gethostbyname(0x402140, 0x2, 0x1, 0x0, 0x0, 0x0) = 0x1
[0x2000004] IAT hook: htons 80
[0x2000004] IAT function is being CALL, htons(0x50, 0x402140, 0x2, 0x1, 0x0, 0x0) = 0x5000
[0x2000004] IAT hook: socket connect '0.0.0.0:80'
[0x2000004] IAT function is being CALL, connect(0x1, 0x117d790, 0x10, 0x50, 0x402140, 0x2) = 0x0
[0x2000004] IAT hook: socket send 'GET / HTTP/1.0\r\nHost: www.example.com\r\n\r\n'
[0x2000004] IAT function is being CALL, send(0x1, 0x117d7a0, 0x29, 0x0, 0x1, 0x117d790) = 0x29
[0x2000004] IAT function is being CALL, Sleep(0x3e8, 0x1, 0x117d7a0, 0x29, 0x0, 0x1) = 0x1
[0x2000004] IAT function is being CALL, recv(0x1, 0x117d7a0, 0x2000, 0x0, 0x3e8, 0x1) = 0x1
[0x2000004] IAT function is being CALL, puts(0x117d7a0, 0x1, 0x117d7a0, 0x2000, 0x0, 0x3e8) = 0x1
[0x2000004] IAT function is being CALL, closesocket(0x1, 0x1, 0x117d7a0, 0x2000, 0x0, 0x3e8) = 0x1
[0x2000004] IAT function is being CALL, WSACleanup(0x1, 0x1, 0x117d7a0, 0x2000, 0x0, 0x3e8) = 0x1
[0x2000004] IAT hook: inet_addr '192.168.0.1'
[0x2000004] IAT function is being CALL, inet_addr(0x40217c, 0x2180200, 0x117f7fc, 0x401507, 0x117f7, 0x117f7) = 0x100a8c0
[0x2000004] IAT function is being CALL, __acrt_iob_func(0x1, 0x1, 0x117f7f4, 0x0, 0x117f7b4, 0x401336) = 0x1
[0x2000004] IAT function is being CALL, __stdio_common_vfprintf(0x0, 0x0, 0x1, 0x402188, 0x0, 0x117f7a8) = 0x1
[0x2000004] IAT function is being CALL, GetModuleHandleW(0x0, 0x401511, 0x8, 0x117f7e4, 0x117f7ec, 0x0) = 0x400000
[0x2000004] IAT function is being CALL, exit(0x0, 0x8, 0x117f7e4, 0x117f7ec, 0x0, 0x117f700) = 0x1
[0x2000004] IAT function is being CALL, _cexit(0x0, 0x8, 0x117f7e4, 0x117f7ec, 0x0, 0x117f700) = 0x1

$ grep 0x117d79 trace.log
[0x40116c] Memory is being WRITE at 0x117d79c, data size = 4, data value = 0x0
[0x401176] Memory is being WRITE at 0x117d580, data size = 4, data value = 0x117d79c
[0x2000004] IAT function is being CALL, WSARecv(0x1, 0x117d600, 0x1, 0x0, 0x117d79c, 0x0) = 0x1
[0x2000004] IAT function is being CALL, puts(0x117d7a0, 0x1, 0x117d600, 0x1, 0x0, 0x117d79c) = 0x1
[0x2000004] IAT function is being CALL, closesocket(0x1, 0x1, 0x117d600, 0x1, 0x0, 0x117d79c) = 0x1
[0x2000004] IAT function is being CALL, WSACleanup(0x1, 0x1, 0x117d600, 0x1, 0x0, 0x117d79c) = 0x1
[0x40121e] Memory is being WRITE at 0x117d790, data size = 2, data value = 0x2
[0x401234] Memory is being WRITE at 0x117d794, data size = 4, data value = 0x0
[0x401240] Memory is being WRITE at 0x117d792, data size = 2, data value = 0x5000
[0x40124f] Memory is being WRITE at 0x117d5c8, data size = 4, data value = 0x117d790
[0x2000004] IAT function is being CALL, connect(0x1, 0x117d790, 0x10, 0x50, 0x402140, 0x2) = 0x0
[0x2000004] IAT function is being CALL, send(0x1, 0x117d7a0, 0x29, 0x0, 0x1, 0x117d790) = 0x29
```

Note that `[0x2000004] IAT hook: socket connect '0.0.0.0:5889'` is apparently wrong.
By examining trace.log, you can find the below warning.

```
WARNING: memory is being WRITE to NULL page, it may cause inaccurate results from here.

[0x401c0e] Memory is being WRITE at 0x0, data size = 4, data value = 0x117f7ec
```

## Add your own hooks

Unico has 3 types of hooks.

* INIT hook, to initialize registers and memory state
  * call `unico.add_init_hook(handler)`
* IAT hook, to emurate imported functions
  * call `unico.add_iat_hook(name, handler)`
  * The return value of hook function is handled as the one of hooked function
* EIP hook, to change the state at some instruction addresses
  * call `unico.add_eip_hook(address, handler)`
  * The return value of hook function is handled as the new EIP value

For more details, read the code.

## License

This project is a derivation from [Unicorn Engine](http://www.unicorn-engine.org/), thus released under the [GPL license](COPYING).
