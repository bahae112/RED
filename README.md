```markdown
# RED

| **Challenge**  | RED                          |
| -------------- | ---------------------------- |
| **Event**      | picoCTF 2025                 |
| **Category**   | Forensics                    |
| **Difficulty** | Easy (100 pts)               |
| **Author**     | Shuailin Pan (LeConjuror)    |

---

## Challenge Description

> RED, RED, RED, RED
>
> Download the image: red.png

---

## Hints

1. The picture seems pure, but is it though?
2. Red?Ged?Bed?Aed?
3. Check whatever Facebook is called now.

---

## Initial Analysis

We download the PNG image:

```bash
$ wget https://challenge-files.picoctf.net/c_verbal_sleep/831307718b34193b288dde31e557484876fb84978b5818e2627e453a54aa9ba6/red.png
$ file red.png
red.png: PNG image data, 128 x 128, 8-bit/color RGBA, non-interlaced
```

The image appears as a solid red square (as suggested by the title). The hints point to steganography, specifically examining the Least Significant Bits (LSB) of the image data. The mention of "whatever Facebook is called now" is a playful clue for "Meta" (Facebook's parent company), pointing to the tool `zsteg`, which is a Python tool for detecting hidden data in PNG files.

---

## Extracting Hidden Data with `zsteg`

We install `zsteg` if needed (`gem install zsteg`), then run it with verbose options to reveal any hidden content:

```bash
$ zsteg -a red.png
```

The output shows several interesting pieces, including a poem and a hidden Base64‑encoded string repeated four times:

```
b1,rgba,lsb,xy      .. text: "cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ=="
```

The repeated string is clearly Base64‑encoded and starts with `cGljb0NURnt...`, which decodes to `picoCTF{...}`.

---

## Decoding the Base64

We decode the Base64 string:

```bash
$ echo "cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==" | base64 -d
picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}
```

The flag is successfully recovered.

---

## Flag

```text
picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}
```

---

## Exploitation Chain

```text
Download red.png
         │
         ▼
Inspect with file → PNG, 128x128 RGBA
         │
         ▼
Use zsteg to extract hidden data (LSB)
         │
         ▼
Find Base64‑encoded string
         │
         ▼
Decode Base64 → flag
```

---

## Lessons Learned

- PNG images can hide data in the least significant bits of colour channels (LSB steganography).
- Tools like `zsteg` automate the detection and extraction of LSB‑hidden data.
- Repeated Base64‑encoded strings are a strong indicator of a hidden payload.
- The challenge name "RED" and hints "Red?Ged?Bed?Aed?" hint at looking at the colour channels (RGBA).
- Always try common steganography tools when dealing with image files.

---

## Repository Structure

```text
.
├── README.md
└── red.png
```

---

## References

- [zsteg GitHub repository](https://github.com/zed-0xff/zsteg)
- [LSB steganography](https://en.wikipedia.org/wiki/Steganography#Digital_steganography)
```
