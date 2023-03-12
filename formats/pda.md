A file with the `.pda` extension represents audio data that has been compiled by `pdc`. This format uses little endian byte order.

## Header

| Offset | Type     | Detail |
|:-------|:---------|:-------|
| `0`    | `char[12]` | Ident "Playdate AUD" |
| `12`   | `uint24`  | Sample rate (in Hz) |
| `15`   | `uint8`  | [Audio data format](#audio-data-format) |

### Audio Data Format

The audio data format field in the file header seems to map to the `playdate.sound` constants in the official SDK:

| Value | SDK Constant | Detail |
|:------|:-------------|:-------|
| `0`   | `kFormat8bitMono` | unsigned 8-bit PCM, one channel |
| `1`   | `kFormat8bitStereo` | unsigned 8-bit PCM, two channels |
| `2`   | `kFormat16bitMono` | signed 16-bit little endian PCM, one channel |
| `3`   | `kFormat16bitStereo` | signed 16-bit little endian PCM, two channels |
| `4`   | `kFormatADPCMMono` | 4-bit IMA ADPCM, one channel |
| `5`   | `kFormatADPCMStereo` | 4-bit IMA ADPCM, two channels |

## Audio Data

The format flag in the file header indicates how the audio is stored:

### 4-bit IMA ADPCM

In this format, the audio is encoded in blocks. The audio data begins with an `uint16` which gives the size of a single block, and the start of the first block begins immediately after.

Each block also begins with a small header consisting of 4 bytes for each audio channel:

| Type   | Detail |
|:-------|:-------|
| `uint16` | ADPCM predictor |
| `uint8` | ADPCM step index |
| `uint8` | Reserved, should always be zero |

The rest of the block contains regular 4-bit IMA ADPCM audio samples. For stereo audio, the left channel uses the high nibble of every byte, while the right channel uses the low nibble.

### 8-bit PCM

Standard 8-bit PCM. Each sample can be converted to signed 16-bit PCM with `(sample - 0x80) << 8`. For stereo audio, the channels are interleaved so you read one sample for the left channel, the next one for the right, next one for the left, and so on.

### 16-bit PCM

Standard signed 16-bit PCM in little-endian byte order. As with the 8-bit format, for stereo audio, the channels are interleaved so you read one sample for the left channel, the next one for the right, next one for the left, and so on.
