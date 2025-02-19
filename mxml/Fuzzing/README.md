### Mini Xml v2.12
- Bug type: use-after-free
- CVE ID: [CVE-2018-20592](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-20592)
- Download: https://github.com/michaelrsweet/mxml
```
git clone https://github.com/michaelrsweet/mxml.git
git checkout 53c75b04c133a79fbf81782fa83d45a6c7d2dcf1
```
- Reproduce: `./mxmldoc $POC`
- ASAN dumps the backtrace:
```
=================================================================
==27460==ERROR: AddressSanitizer: heap-use-after-free on address 0x6080000005c8 at pc 0x7fdfe37d798c bp 0x7ffc372523d0 sp 0x7ffc372523c8
READ of size 8 at 0x6080000005c8 thread T0
    #0 0x7fdfe37d798b in mxmlAdd /home/exp/FOT/mxml-asan-O0/mxml-node.c:128:27
    #1 0x7fdfe37d8ba3 in mxml_new /home/exp/FOT/mxml-asan-O0/mxml-node.c:869:5
    #2 0x7fdfe37d8cb9 in mxmlNewElement /home/exp/FOT/mxml-asan-O0/mxml-node.c:385:15
    #3 0x519c2c in scan_file /home/exp/FOT/mxml-asan-O0/mxmldoc.c:2739:23
    #4 0x5144ef in main /home/exp/FOT/mxml-asan-O0/mxmldoc.c:503:11
    #5 0x7fdfe25e9b96 in __libc_start_main /build/glibc-OTsEL5/glibc-2.27/csu/../csu/libc-start.c:310
    #6 0x41a919 in _start (/home/exp/FOT/mxml-asan-O0/install/bin/mxmldoc+0x41a919)

0x6080000005c8 is located 40 bytes inside of 88-byte region [0x6080000005a0,0x6080000005f8)
freed by thread T0 here:
    #0 0x4da600 in __interceptor_free.localalias.0 (/home/exp/FOT/mxml-asan-O0/install/bin/mxmldoc+0x4da600)
    #1 0x7fdfe37d8955 in mxml_free /home/exp/FOT/mxml-asan-O0/mxml-node.c:821:3
    #2 0x7fdfe37d82f3 in mxmlDelete /home/exp/FOT/mxml-asan-O0/mxml-node.c:234:5
    #3 0x521f34 in sort_node /home/exp/FOT/mxml-asan-O0/mxmldoc.c:3389:5
    #4 0x517124 in scan_file /home/exp/FOT/mxml-asan-O0/mxmldoc.c:2144:7
    #5 0x5144ef in main /home/exp/FOT/mxml-asan-O0/mxmldoc.c:503:11
    #6 0x7fdfe25e9b96 in __libc_start_main /build/glibc-OTsEL5/glibc-2.27/csu/../csu/libc-start.c:310

previously allocated by thread T0 here:
    #0 0x4da9f8 in calloc (/home/exp/FOT/mxml-asan-O0/install/bin/mxmldoc+0x4da9f8)
    #1 0x7fdfe37d8ab7 in mxml_new /home/exp/FOT/mxml-asan-O0/mxml-node.c:844:15
    #2 0x7fdfe37d8cb9 in mxmlNewElement /home/exp/FOT/mxml-asan-O0/mxml-node.c:385:15
    #3 0x51c56f in scan_file /home/exp/FOT/mxml-asan-O0/mxmldoc.c:3289:19
    #4 0x5144ef in main /home/exp/FOT/mxml-asan-O0/mxmldoc.c:503:11
    #5 0x7fdfe25e9b96 in __libc_start_main /build/glibc-OTsEL5/glibc-2.27/csu/../csu/libc-start.c:310

SUMMARY: AddressSanitizer: heap-use-after-free /home/exp/FOT/mxml-asan-O0/mxml-node.c:128:27 in mxmlAdd
Shadow bytes around the buggy address:
  0x0c107fff8060: fa fa fa fa fd fd fd fd fd fd fd fd fd fd fd fa
  0x0c107fff8070: fa fa fa fa fd fd fd fd fd fd fd fd fd fd fd fa
  0x0c107fff8080: fa fa fa fa 00 00 00 00 00 00 00 00 00 00 00 fa
  0x0c107fff8090: fa fa fa fa fd fd fd fd fd fd fd fd fd fd fd fa
  0x0c107fff80a0: fa fa fa fa fd fd fd fd fd fd fd fd fd fd fd fa
=>0x0c107fff80b0: fa fa fa fa fd fd fd fd fd[fd]fd fd fd fd fd fa
  0x0c107fff80c0: fa fa fa fa 00 00 00 00 00 00 00 00 00 00 00 fa
  0x0c107fff80d0: fa fa fa fa 00 00 00 00 00 00 00 00 00 00 00 fa
  0x0c107fff80e0: fa fa fa fa 00 00 00 00 00 00 00 00 00 00 00 fa
  0x0c107fff80f0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c107fff8100: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07 
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
==27460==ABORTING
```