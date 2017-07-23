title: hls enc
author: wsen
date: 2017-06-28 22:43:24
tags:
---
### 生成加密key
`enc.key`
```
$ openssl rand 16 > enc.key
```

### 加密文件
`enc.keyinfo`

```
Key URI
Path to key file
IV (optional)
```
* Key URI 播放视频请求的解密key路径（url）
* path to key file : 当前视频加密使用的文件路径
* IV

eg: 
```
https://hlsbook.net/enc.key
enc.key
ecd0d06eaf884d8226c33928e87efa33
```

### 开始加密

mp4 [测试通过]
```
ffmpeg -i mv.mp4 -c copy -bsf:v h264_mp4toannexb -hls_time 10 -hls_key_info_file enc.keyinfo playlist.m3u8
```
```
ffmpeg -f lavfi -re -i mv.mp4 -c:v h264 -hls_flags delete_segments \
  -hls_key_info_file file.keyinfo mv.m3u8
```

mov
```
ffmpeg -y \
    -i sample.mov \
    -hls_time 9 \
    -hls_key_info_file enc.keyinfo
    -hls_playlist_type vod \
    -hls_segment_filename "fileSequence%d.ts" \
    prog_index.m3u8
```



参考： openssl rand 16 > enc.key
* http://hlsbook.net/how-to-encrypt-hls-video-with-ffmpeg/
* https://stackoverflow.com/questions/32734578/ffmpeg-encryption
* http://www.jianshu.com/p/053665062f22