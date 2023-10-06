---
title: Screenshot OCR
date: 2023-10-06
tags:
  - points
---

```bash
flameshot gui --raw | \
  tesseract stdin stdout | \
  xclip -in -selection clipboard
```

-   [flameshot](https://github.com/flameshot-org/flameshot) :: Powerful yet simple to use screenshot software
-   [tesseract](https://github.com/tesseract-ocr/tesseract) :: Tesseract Open Source OCR Engine
-   [xclip](https://github.com/astrand/xclip) :: Command line interface to the X11 clipboard

The `flameshot gui` launches a GUI that allows you to select a region of the screen, and then press enter. The image output is piped to `tesseract` which does the OCR and in turn pipes it to `xclip` which copies it to the clipboard.

I think this is the first time I've seen a GUI tool that follows the [Unix Philosophy](https://en.wikipedia.org/wiki/Unix_philosophy).