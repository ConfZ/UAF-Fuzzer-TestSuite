### libpff
- Bug type: use-after-free
- CVE ID: [pending](https://github.com/libyal/libpff/issues/61)
- Download: https://github.com/libyal/libpff
```
git clone https://github.com/libyal/libpff.git
git checkout 4938b7a891c6ec2112e5f059e13426915ae49adb
```
- Reproduce: ```./pffinfo $POC```
- ASAN:
```
=================================================================
==8991==ERROR: AddressSanitizer: heap-use-after-free on address 0x6030000011c8 at pc 0x0000005324a5 bp 0x7ffd6bcbe970 sp 0x7ffd6bcbe968
READ of size 4 at 0x6030000011c8 thread T0
    #0 0x5324a4 in libpff_item_tree_create_node /home/hongxu/FOT/libpff/libpff/libpff_item_tree.c:816:32
    #1 0x5318e1 in libpff_item_tree_create_node /home/hongxu/FOT/libpff/libpff/libpff_item_tree.c:570:13
    #2 0x5318e1 in libpff_item_tree_create_node /home/hongxu/FOT/libpff/libpff/libpff_item_tree.c:570:13
    #3 0x533939 in libpff_item_tree_create /home/hongxu/FOT/libpff/libpff/libpff_item_tree.c:1211:11
    #4 0x519ac2 in libpff_file_open_read /home/hongxu/FOT/libpff/libpff/libpff_file.c:1042:11
    #5 0x518e2e in libpff_file_open_file_io_handle /home/hongxu/FOT/libpff/libpff/libpff_file.c:580:6
    #6 0x5189ee in libpff_file_open /home/hongxu/FOT/libpff/libpff/libpff_file.c:322:6
    #7 0x512dcf in info_handle_open_input /home/hongxu/FOT/libpff/pfftools/info_handle.c:298:6
    #8 0x516afc in main /home/hongxu/FOT/libpff/pfftools/pffinfo.c:284:6
    #9 0x7f57c2b28b96 in __libc_start_main /build/glibc-OTsEL5/glibc-2.27/csu/../csu/libc-start.c:310
    #10 0x41a1f9 in _start (/home/hongxu/FOT/libpff/install/bin/pffinfo+0x41a1f9)

0x6030000011c8 is located 24 bytes inside of 32-byte region [0x6030000011b0,0x6030000011d0)
freed by thread T0 here:
    #0 0x4d9ee0 in __interceptor_free.localalias.0 (/home/hongxu/FOT/libpff/install/bin/pffinfo+0x4d9ee0)
    #1 0x616484 in libpff_index_value_free /home/hongxu/FOT/libpff/libpff/libpff_index_value.c:125:3
    #2 0x5c3e9c in libfcache_cache_value_set_value /home/hongxu/FOT/libpff/libfcache/libfcache_cache_value.c:409:8
    #3 0x5c2f84 in libfcache_cache_set_value_by_index /home/hongxu/FOT/libpff/libfcache/libfcache_cache.c:549:6
    #4 0x5e316c in libfdata_tree_set_node_value /home/hongxu/FOT/libpff/libfdata/libfdata_tree.c:850:6
    #5 0x5e59f3 in libfdata_tree_node_set_node_value /home/hongxu/FOT/libpff/libfdata/libfdata_tree_node.c:905:6
    #6 0x60c629 in libpff_index_read_node_data /home/hongxu/FOT/libpff/libpff/libpff_index.c:989:6
    #7 0x5e1c31 in libfdata_tree_get_node_value /home/hongxu/FOT/libpff/libfdata/libfdata_tree.c:627:7
    #8 0x5e5883 in libfdata_tree_node_get_node_value /home/hongxu/FOT/libpff/libfdata/libfdata_tree_node.c:848:6
    #9 0x613781 in libpff_index_tree_node_get_leaf_node_by_identifier /home/hongxu/FOT/libpff/libpff/libpff_index_tree.c:530:7
    #10 0x613df6 in libpff_index_tree_node_get_leaf_node_by_identifier /home/hongxu/FOT/libpff/libpff/libpff_index_tree.c:681:14
    #11 0x6131e3 in libpff_index_tree_get_leaf_node_by_identifier /home/hongxu/FOT/libpff/libpff/libpff_index_tree.c:436:11
    #12 0x53228d in libpff_item_tree_create_node /home/hongxu/FOT/libpff/libpff/libpff_item_tree.c:772:14
    #13 0x5318e1 in libpff_item_tree_create_node /home/hongxu/FOT/libpff/libpff/libpff_item_tree.c:570:13
    #14 0x5318e1 in libpff_item_tree_create_node /home/hongxu/FOT/libpff/libpff/libpff_item_tree.c:570:13
    #15 0x533939 in libpff_item_tree_create /home/hongxu/FOT/libpff/libpff/libpff_item_tree.c:1211:11
    #16 0x519ac2 in libpff_file_open_read /home/hongxu/FOT/libpff/libpff/libpff_file.c:1042:11
    #17 0x518e2e in libpff_file_open_file_io_handle /home/hongxu/FOT/libpff/libpff/libpff_file.c:580:6
    #18 0x5189ee in libpff_file_open /home/hongxu/FOT/libpff/libpff/libpff_file.c:322:6
    #19 0x512dcf in info_handle_open_input /home/hongxu/FOT/libpff/pfftools/info_handle.c:298:6
    #20 0x516afc in main /home/hongxu/FOT/libpff/pfftools/pffinfo.c:284:6
    #21 0x7f57c2b28b96 in __libc_start_main /build/glibc-OTsEL5/glibc-2.27/csu/../csu/libc-start.c:310

previously allocated by thread T0 here:
    #0 0x4da0b0 in malloc (/home/hongxu/FOT/libpff/install/bin/pffinfo+0x4da0b0)
    #1 0x6161ff in libpff_index_value_initialize /home/hongxu/FOT/libpff/libpff/libpff_index_value.c:62:17
    #2 0x60c238 in libpff_index_read_node_data /home/hongxu/FOT/libpff/libpff/libpff_index.c:893:6
    #3 0x5e1c31 in libfdata_tree_get_node_value /home/hongxu/FOT/libpff/libfdata/libfdata_tree.c:627:7
    #4 0x5e5fe1 in libfdata_tree_node_get_number_of_sub_nodes /home/hongxu/FOT/libpff/libfdata/libfdata_tree_node.c:1015:8
    #5 0x53159e in libpff_item_tree_create_node /home/hongxu/FOT/libpff/libpff/libpff_item_tree.c:475:6
    #6 0x5318e1 in libpff_item_tree_create_node /home/hongxu/FOT/libpff/libpff/libpff_item_tree.c:570:13
    #7 0x5318e1 in libpff_item_tree_create_node /home/hongxu/FOT/libpff/libpff/libpff_item_tree.c:570:13
    #8 0x533939 in libpff_item_tree_create /home/hongxu/FOT/libpff/libpff/libpff_item_tree.c:1211:11
    #9 0x519ac2 in libpff_file_open_read /home/hongxu/FOT/libpff/libpff/libpff_file.c:1042:11
    #10 0x518e2e in libpff_file_open_file_io_handle /home/hongxu/FOT/libpff/libpff/libpff_file.c:580:6
    #11 0x5189ee in libpff_file_open /home/hongxu/FOT/libpff/libpff/libpff_file.c:322:6
    #12 0x512dcf in info_handle_open_input /home/hongxu/FOT/libpff/pfftools/info_handle.c:298:6
    #13 0x516afc in main /home/hongxu/FOT/libpff/pfftools/pffinfo.c:284:6
    #14 0x7f57c2b28b96 in __libc_start_main /build/glibc-OTsEL5/glibc-2.27/csu/../csu/libc-start.c:310

SUMMARY: AddressSanitizer: heap-use-after-free /home/hongxu/FOT/libpff/libpff/libpff_item_tree.c:816:32 in libpff_item_tree_create_node
Shadow bytes around the buggy address:
  0x0c067fff81e0: fa fa 00 00 00 00 fa fa 00 00 00 00 fa fa 00 00
  0x0c067fff81f0: 00 00 fa fa fd fd fd fd fa fa fd fd fd fd fa fa
  0x0c067fff8200: fd fd fd fd fa fa fd fd fd fd fa fa fd fd fd fd
  0x0c067fff8210: fa fa fd fd fd fd fa fa fd fd fd fd fa fa fd fd
  0x0c067fff8220: fd fd fa fa fd fd fd fd fa fa fd fd fd fd fa fa
=>0x0c067fff8230: fd fd fd fd fa fa fd fd fd[fd]fa fa 00 00 00 00
  0x0c067fff8240: fa fa 00 00 00 00 fa fa 00 00 00 00 fa fa 00 00
  0x0c067fff8250: 00 00 fa fa 00 00 00 00 fa fa 00 00 00 00 fa fa
  0x0c067fff8260: 00 00 00 00 fa fa 00 00 00 00 fa fa 00 00 00 00
  0x0c067fff8270: fa fa 00 00 00 00 fa fa 00 00 00 00 fa fa 00 00
  0x0c067fff8280: 00 00 fa fa 00 00 00 00 fa fa 00 00 00 00 fa fa
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
==8991==ABORTING
```
