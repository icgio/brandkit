# 共识之中资产源文件

`logo/consensus-*` 的两个成品由这里的 HTML 用 headless Chrome 截图生成。

```bash
# 卡片 1080x339
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless --disable-gpu \
  --hide-scrollbars --no-sandbox --window-size=1080,339 \
  --screenshot=../logo/consensus-card-light1080x339.png \
  "file://$PWD/consensus-card.html"

# 横幅 1080x145，20 帧打字机动画，帧号经 URL hash 传入
for i in $(seq 1 20); do
  "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless --disable-gpu \
    --hide-scrollbars --no-sandbox --window-size=1080,145 \
    --screenshot="f$(printf '%02d' $i).png" "file://$PWD/consensus-banner.html#$i"
done
ffmpeg -y -framerate 4.914 -i "f%02d.png" \
  -vf "split[a][b];[a]palettegen=max_colors=64[p];[b][p]paletteuse=dither=bayer:bayer_scale=3" \
  -loop 0 ../logo/consensus-banner-light1080x145.gif
```

两个 HTML 都引用同目录的 `light690x175nobg.png`（从 `logo/` 拷一份过来即可）。

## 为什么是 1080 宽

微信正文图一律压到 1080px 宽。早前是 1400，会被以 0.771 这种非整数比例重采样一道，
文字边缘和细线发糊。按 1080 原生渲染则零重采样，实测比缩放版明显锐利。

改尺寸时注意：文字必须按新画布原生渲染，不要缩放成品图。
