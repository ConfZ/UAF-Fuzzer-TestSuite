### openh264
- Bug type: use-after-free
- CVE ID: [pending](https://github.com/cisco/openh264/issues/3108)
- Download: https://github.com/cisco/openh264
```
git clone https://github.com/cisco/openh264.git
git checkout 8684722271ac16118df2fe50322ffe218b9507a7
```
- Reproduce: `h264dec $POC ./tmp`
- ASAN:
```
H264 source file name: huaf_decoder.cpp:178_2.264..
Sequence output file name: /tmp/test.yuv..
[OpenH264] this = 0x0x61b000000080, Warning:referencing pictures lost due frame gaps exist, prev_frame_num: -1, curr_frame_num: 1
[OpenH264] this = 0x0x61b000000080, Warning:DecodeCurrentAccessUnit() failed (468766) in frame: 1 uiDId: 0 uiQId: 0
[OpenH264] this = 0x0x61b000000080, Warning:DecodeCurrentAccessUnit() failed (468766) in frame: 1 uiDId: 0 uiQId: 0
[OpenH264] this = 0x0x61b000000080, Warning:referencing pictures lost due frame gaps exist, prev_frame_num: 1, curr_frame_num: 9
[OpenH264] this = 0x0x61b000000080, Warning:DecodeCurrentAccessUnit() failed (468766) in frame: 9 uiDId: 0 uiQId: 0
[OpenH264] this = 0x0x61b000000080, Warning:DecodeCurrentAccessUnit() failed (468766) in frame: 9 uiDId: 0 uiQId: 0
[OpenH264] this = 0x0x61b000000080, Warning:referencing pictures lost due frame gaps exist, prev_frame_num: 9, curr_frame_num: 1
[OpenH264] this = 0x0x61b000000080, Warning:DecodeCurrentAccessUnit() failed (468766) in frame: 1 uiDId: 0 uiQId: 0
=================================================================
==29351==ERROR: AddressSanitizer: heap-use-after-free on address 0x615000000dd8 at pc 0x00000051f63e bp 0x7ffd438bb890 sp 0x7ffd438bb888
READ of size 8 at 0x615000000dd8 thread T0
    #0 0x51f63d in WelsDec::ResetRefPicReferences(WelsDec::TagWelsDecoderContext* const&, WelsDec::SPicture* const&) /home/exp/FOT/openh264/asan-real/codec/decoder/core/src/decoder.cpp:178:39
    #1 0x51f63d in WelsDec::DecreasePicBuff(WelsDec::TagWelsDecoderContext*, WelsDec::TagPicBuff**, int, int, int, int) /home/exp/FOT/openh264/asan-real/codec/decoder/core/src/decoder.cpp:236
    #2 0x51f63d in WelsRequestMem /home/exp/FOT/openh264/asan-real/codec/decoder/core/src/decoder.cpp:424
    #3 0x5224c4 in SyncPictureResolutionExt /home/exp/FOT/openh264/asan-real/codec/decoder/core/src/decoder.cpp:857:10
    #4 0x537eea in WelsDec::ConstructAccessUnit(WelsDec::TagWelsDecoderContext*, unsigned char**, TagBufferInfo*) /home/exp/FOT/openh264/asan-real/codec/decoder/core/src/decoder_core.cpp:2249:12
    #5 0x521030 in WelsDecodeBs /home/exp/FOT/openh264/asan-real/codec/decoder/core/src/decoder.cpp:820:7
    #6 0x51885b in WelsDec::CWelsDecoder::DecodeFrame2(unsigned char const*, int, unsigned char**, TagBufferInfo*) /home/exp/FOT/openh264/asan-real/codec/decoder/plus/src/welsDecoderExt.cpp:575:3
    #7 0x5182aa in WelsDec::CWelsDecoder::DecodeFrameNoDelay(unsigned char const*, int, unsigned char**, TagBufferInfo*) /home/exp/FOT/openh264/asan-real/codec/decoder/plus/src/welsDecoderExt.cpp:500:11
    #8 0x511a52 in H264DecodeInstance(ISVCDecoder*, char const*, char const*, int&, int&, char const*, char const*, int, bool) /home/exp/FOT/openh264/asan-real/codec/console/dec/src/h264dec.cpp:222:17
    #9 0x51491b in main /home/exp/FOT/openh264/asan-real/codec/console/dec/src/h264dec.cpp:502:3
    #10 0x7f295b022b96 in __libc_start_main /build/glibc-OTsEL5/glibc-2.27/csu/../csu/libc-start.c:310
    #11 0x41d059 in _start (/home/exp/FOT/openh264/asan-real/h264dec+0x41d059)

0x615000000dd8 is located 216 bytes inside of 499-byte region [0x615000000d00,0x615000000ef3)
freed by thread T0 here:
    #0 0x4d24f8 in __interceptor_free.localalias.0 (/home/exp/FOT/openh264/asan-real/h264dec+0x4d24f8)
    #1 0x51f1bc in WelsDec::DecreasePicBuff(WelsDec::TagWelsDecoderContext*, WelsDec::TagPicBuff**, int, int, int, int) /home/exp/FOT/openh264/asan-real/codec/decoder/core/src/decoder.cpp:237:9
    #2 0x51f1bc in WelsRequestMem /home/exp/FOT/openh264/asan-real/codec/decoder/core/src/decoder.cpp:424

previously allocated by thread T0 here:
    #0 0x4d26b0 in __interceptor_malloc (/home/exp/FOT/openh264/asan-real/h264dec+0x4d26b0)
    #1 0x658e29 in WelsCommon::WelsMalloc(unsigned int, char const*, unsigned int) /home/exp/FOT/openh264/asan-real/codec/common/src/memory_align.cpp:72:30
    #2 0x658e29 in WelsCommon::CMemoryAlign::WelsMalloc(unsigned int, char const*) /home/exp/FOT/openh264/asan-real/codec/common/src/memory_align.cpp:129
    #3 0x658e29 in WelsCommon::CMemoryAlign::WelsMallocz(unsigned int, char const*) /home/exp/FOT/openh264/asan-real/codec/common/src/memory_align.cpp:118

SUMMARY: AddressSanitizer: heap-use-after-free /home/exp/FOT/openh264/asan-real/codec/decoder/core/src/decoder.cpp:178:39 in WelsDec::ResetRefPicReferences(WelsDec::TagWelsDecoderContext* const&, WelsDec::SPicture
* const&)
Shadow bytes around the buggy address:
  0x0c2a7fff8160: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c2a7fff8170: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c2a7fff8180: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 03 fa
  0x0c2a7fff8190: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c2a7fff81a0: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd
=>0x0c2a7fff81b0: fd fd fd fd fd fd fd fd fd fd fd[fd]fd fd fd fd
  0x0c2a7fff81c0: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd
  0x0c2a7fff81d0: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fa
  0x0c2a7fff81e0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c2a7fff81f0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c2a7fff8200: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
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
==29351==ABORTING
```