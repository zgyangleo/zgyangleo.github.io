---
layout: post
title:  "Merge multiple scalar into one figure in tensorboardX"
date:   2019-07-01 21:28:01 +0800
categories: pytorch
tag: tensorboardX  
---

* content
{:toc}


关于pytorch的可视化软件有Visdom和tensorboardX两种，用过的人很明显可以知道tensorboardX的强大。关于tensorboardX的安装和使用我就不再赘述了，网上有很多优秀的帖子，新手可以参考。

这篇博客主要总结一下如何在同一个figure里绘制多个训练曲线。使用add_scalars即可解决这个问题。add_scalar是绘制单个曲线。我来举个例子，
原先单独绘制曲线的code如下：
        
        writer = SummaryWriter()

        for iter in emuerate(xx):
            writer.add_scalar('loss', loss, iter)
            if iter % 1000 == 0:
                writer.add_scalar('top1', top1, iter)
                writer.add_scalar('top5', top5, iter)

        writer.close()

修改为以下的code即可将多个曲线绘制在一个图中：

        writer = SummaryWriter()

        for n_iter in emuerate(10000):
            writer.add_scalars('data/scalar_group', {'loss': n_iter*np.arctan(n_iter)}, n_iter)
            if iter % 1000 == 0:
                writer.add_scalars('data/scalar_group', {'top1': n_iter*np.arctan(n_iter)}, n_iter)
                writer.add_scalars('data/scalar_group', {'top5': n_iter*np.arctan(n_iter)}, n_iter)

        writer.close()

结果如下				{#zgyangleo}
------------------------

![结果]({{ '/styles/images/scalar.png' | prepend: site.baseurl  }})

[其它问题](#)

遇到到过在输入
        
        tensorboard --logdir = 路径

后，显示错误问题为接口本地接口被占用，此时更换接口即可:

        tensorboard --logdir = 路径 --port 6007

