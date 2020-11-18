---
layout: post
title:  "Pytorch finetune"
date:   2019-6-28 10:02:01 +0800
categories: pytorch
tag: finetune
---

* content
{:toc}


[Finetune](#)

往往在加载了预训练模型的参数之后，我们需要finetune模型，可以使用不同的方式finetune。

[局部微调](#)

有时候我们加载了训练模型后，只想调节最后的几层，其他层不训练。其实不训练也就意味着不进行梯度计算，PyTorch中提供的requires_grad使得对训练的控制变得非常简单。

        model = torchvisin,model.resnet18(pretrained=True)
        for param in model.parameters():
            param.requires_grad = False
        model.fc = nn.Linear(512, 100) #替换最后的全连接层，改为训练100类，新构造的模块的参数默认为required_grad为True
        optimizer = optim.SGD(model.fc.parameters(), lr=1e-2, momentum=0.9) #只优化最后的分类层

[全局微调](#)
 
有时候我们需要对全局都进行finetune，只不过我们希望改换过的层和其他层的学习速率不一样，这时候我们可以把其他层和新层在optimizer中单独赋予不同的学习速率。比如：


        ignored_params = list(map(id,model.fc.parameters()))
        base_params = filter(lambda p: id(p) not in ignored_params, model.parameters()}
        optimizer = torch.optim.SGD([
            {'param': base_params},
            {'param': model.fc.parameters(), 'lr':1e-2}
        ], lr=1e-3, momedtum=0.9)

其中base_params使用1e-3来训练，model.fc.parameters使用1e-2来训练，momentum是二者共有的。

[加载预训练模型](#)

Pytorch中的torchvisin 已经支持加载很多常用的模型，可以直接调用： AlexNet VGG Resnet SqueezeNet DenseNet
一行命令即可加载成功，预置Pretrained = true, 即加载其在imagenet上的预训练模型参数。
        
        resnet = torchvision,models.resnet18(pretrained=True)

[加载部分预训练模型](#)

其实大多数时候我们需要根据我们的任务调节我们的模型，所以很难保证模型和公开的模型完全一样，但是预训练模型的参数确实有助于提高训练的准确率，为了结合二者的优点，就需要我们加载部分预训练模型。

        pretrained_dict = model_zoo.load_url(model_urls('resnet50')) #可用torch,load('')替代
        model_dict = model.state_dict()
        pretrained_dict = {k; v for k, v in pretrained_dict.items() if k in model_dict} #将pretrained_dict里不属于model_dict的键剔除掉
        model_dict.update(pretrained_dict) #更新现有的model_dict
        model_load_state_dict(model_dict) #加载我们真正需要的state_dict

有时遇到需要加载的预训练模型和我们即将训练的模型键值对不匹配的问题，此时我们需要修改键值对以加载预训练模型参数。
贴出一些code供参考：

        pretrained_dict = {('module.' + k ) : v for k, v in pretrained_dict.items() if k not in model_dict and 'module.base_model.' + k in model_dict}




        sd = torch.load(' ')
        sd = sd['state_dict']
        model_dict = model.state_dict()
        replace_dict = []
        for k, v in sd.items():
            if k not in model_dict and k.replace('.net', '') in model_dict:
                print('=> Load after remove .net: ', k)
                replace_dict.append((k, k.replace('.net', '')))
        for k, v in model_dict.items():
            if k not in sd and k.replace('.net', '') in sd:
                print('=> Load after adding .net: ', k)
                replace_dict.append((k.replace('.net', ''), k))

        for k, k_new in replace_dict:
            sd[k_new] = sd.pop(k)
        keys1 = set(list(sd.keys()))
        keys2 = set(list(model_dict.keys()))
        set_diff = (keys1 - keys2) | (keys2 - keys1)
        print('#### Notice: keys that failed to load: {}'.format(set_diff))
        if args.dataset not in args.tune_from:  # new dataset
            print('=> New dataset, do not load fc weights')
        sd = {k: v for k, v in sd.items() if 'fc' not in k}
        if args.modality == 'Flow' and 'Flow' not in args.tune_from:
            sd = {k: v for k, v in sd.items() if 'conv1.weight' not in k}
        model_dict.update(sd)
        model.load_state_dict(model_dict)

有时候遇到预训练模型参数因为和模型参数维度不同的情况size mismatch问题，此时可使用squeeze和unsqueeze函数解决这个问题。

[训练部分参数](#)

1）添加下面一句话到模型中

        for p in self.parameters():
            p.requires_grad = False

比如加载了resnet预训练模型之后，在resenet的基础上连接了新的模快，resenet模块那部分可以先暂时冻结不更新，只更新其他部分的参数，那么可以在下面加入上面那句话

        class REANET_MF(nn.Module):
            def _init_(self, module,pretrained):
                super(RESNET_MF, self),_init_()
                self.resnet = model(pretrained)
                for p in self.parameters():
                    p.requires_grad = False
                self.f = SpectralNorm(nn.Conv2d(2048, 512, 1))
                self.g = SpectralNorm(nn.Conv2d(2048, 512, 1))
                self.h = SpectralNorm(nn.Conv2d(2048, 2048, 1))
                ...

同时在优化器中添加：filter(lambda p: p.requires_grad, model.parameters())

        optimizer = optim.Adam(filter(lambda p: p.requires_grad, model.parameters()), lr=0.001, betas=(0.9, 0.999),eps=1e-08,weight_decay=1e-05)

参数保存在有序的字典中，那么可以通过查找参数的名字对应的id值，进行冻结
   
查找的代码：

        model_dict = torch.load('net.pth.tar').state_dict()
        dict_name = list(model_dict)
        for i, p in enumerate(dict_name):
            print(i, p)

保存一下这个文件，可以看到大致是这个样子的：

0 gamma

1 resnet.conv1.weight

2 resnet.bn1.weight

3 resnet.bn1.bias

4 resnet.bn1.running_mean

5 resnet.bn1.running_var

6 resnet.layer1.0.conv1.weight

7 resnet.layer1.0.bn1.weight

8 resnet.layer1.0.bn1.bias

9 resnet.layer1.0.bn1.running_mean
....

同样在模型中添加这样的代码：

        for i.p in enumerate(net.parameters()):
            if i < 165:
                p.requires_grad = False

[idea](#)

之前看到迁移学习方面的一篇文章，其把其他模型中的参数逐层，逐通道对应迁移到现有模型上，这应该属于更高层次的finetue了吧，有机会我会找出那篇文章深入剖析一下。
