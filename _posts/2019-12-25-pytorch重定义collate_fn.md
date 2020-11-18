---
layout: post
title:  "pytorch重定义collate_fn"
date:   2019-12-25 8:54:01 +0800
categories: pytorch
tag: pytorch
---

* content
{:toc}

default_collate是DataLoader的默认collate_fn，并传给了_DataLoaderIter作为_worker_loop处理数据的基本函数.　源代码如下：


        def default_collate(batch):
            "Puts each data field into a tensor with outer dimension batch size"
            if torch.is_tensor(batch[0]):
                out = None
                if _use_shared_memory:
                    # If we're in a background process, concatenate directly into a
                    # shared memory tensor to avoid an extra copy
                    numel = sum([x.numel() for x in batch])
                    storage = batch[0].storage()._new_shared(numel)
                    out = batch[0].new(storage)
                return torch.stack(batch, 0, out=out)
            elif type(batch[0]).__module__ == 'numpy':
                elem = batch[0]
                if type(elem).__name__ == 'ndarray':
                    return torch.stack([torch.from_numpy(b) for b in batch], 0)
                if elem.shape == ():  # scalars
                    py_type = float if elem.dtype.name.startswith('float') else int
                    return numpy_type_map[elem.dtype.name](list(map(py_type, batch)))
            elif isinstance(batch[0], int):
                return torch.LongTensor(batch)
            elif isinstance(batch[0], float):
                return torch.DoubleTensor(batch)
            elif isinstance(batch[0], string_classes):
                return batch
            elif isinstance(batch[0], collections.Mapping):
                return {key: default_collate([d[key] for d in batch]) for key in batch[0]}
            elif isinstance(batch[0], collections.Sequence):
                transposed = zip(*batch)
                return [default_collate(samples) for samples in transposed]

            raise TypeError(("batch must contain tensors, numbers, dicts or lists; found {}"
                            .format(type(batch[0]))))


torch.stack目的：将batch size个样本合成为一个batch (加了一个维度)．if判断传入的数据的类型并做相应的处理．colllate_fn这个函数的输入就是一个list, list的长度是一个batch_size, list中的每个元素都是__getitem__得到的结果．

一般我们使用默认的collate_fn即可．但也不乏需要根据我们实际的数据集的输入重写新的collate_fn的情况．YOLO3即重写了collate_fn函数.

我在实验过程中需要处理输入的句子, 所以也重写了collate_fn, 代码如下：

        # 假如每一个样本为：
        sample = {
            # 一个句子中各个词的id
            'token_list' : [5, 2, 4, 1, 9, 8],
            # 结果y
            'label' : 5,
        }


        # 重写collate_fn函数，其输入为一个batch的sample数据
        def collate_fn(batch):
            # 因为token_list是一个变长的数据，所以需要用一个list来装这个batch的token_list
            token_lists = [item['token_list'] for item in batch]
            
            # 每个label是一个int，我们把这个batch中的label也全取出来，重新组装
            labels = [item['label'] for item in batch]
            # 把labels转换成Tensor
            labels = torch.Tensor(labels)
            return token_lists, labels
        


        # 在使用DataLoader加载数据时，注意collate_fn参数传入的是重写的函数
        DataLoader(trainset, batch_size=4, shuffle=True, num_workers=4, collate_fn=collate_fn)
