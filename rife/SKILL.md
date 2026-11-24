---
name: rife
description: Use when interpolating video frames to raise frame rate or make slow motion: 2X/4X/16X interpolation between frames or images.
---
来源:https://github.com/hzwer/ECCV2022-RIFE (2026-11-24 ET 核实, stars 5,723, 语言 Python, 许可证 MIT, 上游提交 5d8adbd). 帖子来源:https://x.com/okapi_fukugyo/status/2125013131554660780 (@okapi_fukugyo, 2026-11-23 ET).

> 注意:上游仓库本身是帧插值模型实现,不是原生 Agent Skill.本文件是舰队包装技能:先读同目录 README.md,再按下面流程动手.不要凭记忆编命令,命令以 README 的 CLI Usage 为准.

# RIFE (Real-Time Intermediate Flow Estimation for Video Frame Interpolation)

## 何时用

视频要补帧时:把 30fps 补到 60fps、做慢动作、两张静帧之间插出过渡帧.任意时刻插值, 支持 2X / 4X / 16X.

## 安装

```bash
git clone git@github.com:megvii-research/ECCV2022-RIFE.git
cd ECCV2022-RIFE
pip3 install -r requirements.txt
```

再按下述 README 链接下载预训练 HD 模型, 解压后放进 `train_log/`:

- Google Drive 链接见 README 的 Installation 一节 (百度网盘备用链接也在同一节).
- 论文原版模型见 README 的 Evaluation 一节.
- 注意:模型权重不在仓库里, 必须另下载, 没权重跑不起来.
- 需要 GPU (README 实测:2080Ti 上 2X 720p 插值 30+FPS).另有 Docker 跑法, 见 README 的 Run in docker 一节.还有 Colab 演示.

## 核心用法 (README 原命令)

```bash
# 视频 2X 插值 (生成 video_2X_xxfps.mp4)
python3 inference_video.py --exp=1 --video=video.mp4
# 视频 4X 插值
python3 inference_video.py --exp=2 --video=video.mp4
# 4K 等高分辨率先降采样 (默认 --scale=1.0; 出乱纹试 --scale=2.0)
python3 inference_video.py --exp=1 --video=video.mp4 --scale=0.5
# 从 png 序列读视频 (input/0.png ... input/612.png, 文件名须为数字)
python3 inference_video.py --exp=2 --img=input/
# 慢动作到 60fps (音频会被去掉)
python3 inference_video.py --exp=2 --video=video.mp4 --fps=60
# 原视频与结果并排 montage, 存 png
python3 inference_video.py --video=video.mp4 --montage --png
# 两张图之间 16X 插值 (2^4=16)
python3 inference_img.py --img img0.png img1.png --exp=4
# png 序列转 mp4 / gif (命令见 README 的 Image Interpolation 一节)
ffmpeg -r 10 -f image2 -i output/img%d.png -s 448x256 -c:v libx264 -pix_fmt yuv420p output/slomo.mp4 -q:v 0 -q:a 0
```

## 关键点

- 任意时刻插值:不只 2X, 可以插任意中间时刻.
- 新模型在 Practical-RIFE (上游作者的后续仓库):v4.7-4.10 针对动漫场景优化, 4.22.lite 适合给扩散模型生成的视频做后处理.
- 光流估计与视频拼接见 README 引用的 issue #278 与 #291.
- 相关软件:RIFE-App、FlowFrames、SVFI、VapourSynth-RIFE、RIFE-ncnn-vulkan (见 README 的 Software 一节, 各自独立开发, 与本仓库无关).

## 适用场景

视频补帧升帧率、慢动作、AI 生成视频后处理、静帧之间做过渡.

## 注意事项

- 权重下载链接是 Google Drive / 百度网盘, 自动化环境可能打不开, 需人手动下好放进 train_log/.
- 4K 视频务必先 `--scale=0.5`, 否则显存与速度都吃紧.
- 上游 README 见同目录 README.md, 许可证见 LICENSE.upstream.
