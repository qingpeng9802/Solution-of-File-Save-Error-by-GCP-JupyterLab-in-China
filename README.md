# 中国大陆使用 Google Cloud Platform (GCP) 运行 JupyterLab 遇到 File Save Error 的解决方案

## 背景  
（2021年 3月）  
由于中国大陆连接Google的网络不稳定，使用在 GCP 上运行的 JupyterLab 的时候（特别是使用...notebooks.googleusercontent.com的时候），
如果发生网络不稳定，特别是长期有输出更新notebook内容的场景下（如长时间训练，有冗长的训练损失输出），notebook很容易因为内容同步不及而出现 File Save Error.
而且出现 File Save Error 后大概率不能后续解决冲突，重启kernel或者实例都不一定能save成功，容易造成巨大损失.  

## 方案
因此，建议方案是将 GCP instance 端口映射到 localhost 上，然后使用 Colab 连接 localhost 的端口来进行工作.  
请进行以下步骤：
1. 安装 `Cloud SDK` 到 Windows
2. 使用 `Google Cloud Shell` (不要使用 `Cloud Tools for Powershell`)  
https://research.google.com/colaboratory/local-runtimes.html  
https://cloud.google.com/ai-platform/deep-learning-vm/docs/jupyter  
3.  输入
    ```shell
    gcloud compute ssh "my-instance" --project "my-project-id" --zone "my-zone" -- -L 8080:localhost:8080
    ```
    (在Chrome上用其它端口可能不会成功, 只有8080端口能保证成功)  
    你现在应该能用 `localhost:8080` 连接到 JupyterLab.  
4. 在 Colab 中, 选择 `连接到本地运行时`, 然后输入 `localhost:8080` ，连接.

## 提示
实际上如果是在本地使用 `localhost:8080` 来进行工作，似乎 ssh 并不容易发生中断（而 ...notebooks.googleusercontent.com 有较大中断风险），保存工作是足够稳定的. 但是 JupyterLab 的修订版本控制不是很完善，万一出现不可解决的 File Save Error 损失较大，推荐使用 Colab 进行工作.  
  
Colab 有优秀的修订版本控制系统，即使在长时间失去同步、脱离连接或多窗口修改的状态下都可以通过diff来解决冲突并确定最新版本. 而且 Colab 还有优秀的代码提示环境.  
