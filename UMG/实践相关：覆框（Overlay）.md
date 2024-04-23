视频链接：[复刻一个UI的美术向UMG开发简单流程展示（非正式内容）\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1Nd4y1r7v9/?spm_id_from=333.788.recommend_more_video.0&vd_source=ec161869fed250aa616ee2fdd48e6494)

---

参考图如下，分析上方 UI 部分

![[Pasted image 20240408153106.png]]

- 水平排列（水平框）：很明显 UI 要素是水平排列所以肯定需要一个水平框
- 背景模糊/偏暗（简单实现可以加一张图片设置不透明度）

水平框的元素是水平排列的，但是上图很明显人物选择 UI 是和背景的 image 进行叠放的，所以这里需要用到一个**Overlay**实现叠放效果

**Overlay**控件描述如下：
![[Pasted image 20240408153334.png]]

所以大概实现是这样：![[Pasted image 20240408153504.png]]

