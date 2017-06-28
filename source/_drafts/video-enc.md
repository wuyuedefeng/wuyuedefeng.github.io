title: video enc
author: wsen
date: 2017-06-26 19:01:30
tags:
---
install ffmpeg, mp4box
```
brew install ffmpeg
brew install mp4box
```
分离 audio video
```
$ ffmpeg -i source.mp4 -c:v copy -an video.mp4
$ ffmpeg -i source.mp4 -c:a copy -vn audio.mp4
```

```
$ mp4box.exe -crypt crypt.xml video-clear.mp4 -out video-encrypted.mp4
```

crypt.xml
```
<?xml version="1.0" encoding="UTF-8" ?>
<GPACDRM type="CENC AES-CTR">
  <DRMInfo type="pssh" version="0">
    <!-- See Widevine documentation for details. -->
    <BS ID128="EDEF8BA979D64ACEA3C827DCD51D21ED"/><!-- Widevine identifier. -->
    <BS data64="CAESEH1EE/uiUEe/k0upQ9yO8xQaBmF4aW5vbSINc2ludF92aWRfY3VzdCoCU0QyAA=="/>
  </DRMInfo>
  <DRMInfo type="pssh" version="0">
    <!-- This is the PlayReady Header Object (PRO). See PlayReady documentation for details. -->
    <BS ID128="9A04F07998404286AB92E65BE0885F95"/><!-- PlayReady identifier. -->
    <BS bits="32" endian="little" value="956"/><!-- Size of PRO. -->
    <BS bits="16" endian="little" value="1"/>
    <BS bits="16" endian="little" value="1"/>
    <BS bits="16" endian="little" value="946"/><!-- Size of WRMHEADER. -->
    <BS data64="PABXAFIATQBIAEUAQQBEAEUAUgAgAHgAbQBsAG4AcwA9ACIAaAB0AHQAcAA6AC8ALwBzAGMAaABlAG0AYQBzAC4AbQBpAGMAcgBvAHMAbwBmAHQALgBjAG8AbQAvAEQAUgBNAC8AMgAwADAANwAvADAAMwAvAFAAbABhAHkAUgBlAGEAZAB5AEgAZQBhAGQAZQByACIAIAB2AGUAcgBzAGkAbwBuAD0AIgA0AC4AMAAuADAALgAwACIAPgA8AEQAQQBUAEEAPgA8AFAAUgBPAFQARQBDAFQASQBOAEYATwA+ADwASwBFAFkATABFAE4APgAxADYAPAAvAEsARQBZAEwARQBOAD4APABBAEwARwBJAEQAPgBBAEUAUwBDAFQAUgA8AC8AQQBMAEcASQBEAD4APAAvAFAAUgBPAFQARQBDAFQASQBOAEYATwA+ADwASwBJAEQAPgArAHgATgBFAGYAVgBDAGkAdgAwAGUAVABTADYAbABEADMASQA3AHoARgBBAD0APQA8AC8ASwBJAEQAPgA8AEMASABFAEMASwBTAFUATQA+ADMAcQBJADIAOABoAEkAcwBFAHYANAA9ADwALwBDAEgARQBDAEsAUwBVAE0APgA8AEwAQQBfAFUAUgBMAD4AaAB0AHQAcABzADoALwAvAGEAeABjAGEAcwAuAHMAaQBsAHYAZQByAGgAZAAuAGEAeAB0AGUAcwB0AC4AbgBlAHQALwBMAGkAYwBlAG4AcwBpAG4AZwBTAHkAcwB0AGUAbQAvAEwAaQBjAGUAbgBzAGkAbgBnAFMAZQByAHYAaQBjAGUALgBhAHMAbQB4ADwALwBMAEEAXwBVAFIATAA+ADwAQwBVAFMAVABPAE0AQQBUAFQAUgBJAEIAVQBUAEUAUwA+ADwAUAByAG8AdABlAGMAdABlAGQAVwBpAHQAaAA+AFMAaQBsAHYAZQByAEgARAAgAEQAUgBNACAANQAuADEALgA4ADwALwBQAHIAbwB0AGUAYwB0AGUAZABXAGkAdABoAD4APABJAEkAUwBfAEQAUgBNAF8AVgBFAFIAUwBJAE8ATgA+ADcALgAxAC4AMQA1ADYANQAuADQAPAAvAEkASQBTAF8ARABSAE0AXwBWAEUAUgBTAEkATwBOAD4APAAvAEMAVQBTAFQATwBNAEEAVABUAFIASQBCAFUAVABFAFMAPgA8AC8ARABBAFQAQQA+ADwALwBXAFIATQBIAEUAQQBEAEUAUgA+AA=="/>
  </DRMInfo>
  <CrypTrack trackID="1" IsEncrypted="1" IV_size="8" first_IV="0x22ee7d4745d3a26a" saiSavedBox="senc">
	<key KID="0x7D4413FBA25047BF934BA943DC8EF314" value="0xCBC2D56B1B4A847F9B20919AFB32278C"/>
  </CrypTrack>
</GPACDRM>
```

```
mp4box.exe -crypt crypt.xml video-700k.mp4 -out video-700k-encrypted.mp4
mp4box.exe -crypt crypt.xml video-1000k.mp4 -out video-1000k-encrypted.mp4
mp4box.exe -crypt crypt.xml video-1500k.mp4 -out video-1500k-encrypted.mp4
mp4box.exe -crypt crypt.xml video-2000k.mp4 -out video-2000k-encrypted.mp4

mp4box.exe -crypt crypt.xml audio.mp4 -out audio-encrypted.mp4
```

good posts
* http://alphahinex.github.io/2015/12/31/how-to-encrypt-mp4-using-clearkey-and-play

* https://drm.axinom.com/creating-multi-drm-protected-videos-with-free-tools/
