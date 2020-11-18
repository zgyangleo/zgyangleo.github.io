---
layout: post
title:  "train cnn simultaneously"
date:   2019-09-09 19:05:01 +0800
categories: cnn
tag: pytorch 
---

* content
{:toc}


The network architecture:

![network]({{ '/styles/images/Capture.JPG' | prepend: site.baseurl  }})

Train theses CNN network simultaneously for the given input and then merge their loss function and then carry out backpropagation on combined loss function in pytorch.

code:

    class MyModel(nn.Module):
        def __init__(self, nb_vowels=5, nb_consonants=24):
            super(MyModel, self).__init__()
            self.base = nn.Sequential(
                nn.Conv2d(3, 6, 3, 1, 1),
                nn.ReLU(),
                nn.MaxPool2d(2),
                nn.Conv2d(6, 12, 3, 1, 1),
                nn.ReLU(),
                nn.MaxPool2d(2)
            )
            self.fc1 = nn.Linear(12 * 6 * 6, 64)
        
            self.fc_vowels = nn.Linear(64, nb_vowels)
            self.fc_cons = nn.Linear(64, nb_consonants)
        
        def forward(self, x):
            x = self.base(x)
            x = x.view(x.size(0), -1)
            x = F.relu(self.fc1(x))
        
            x_vowels = self.fc_vowels(x)
            x_cons = self.fc_cons(x)
            return x_vowels, x_cons


    model = MyModel()

    # Define your different loss functions here
    criterion_vowels = nn.CrossEntropyLoss()
    criterion_cons = nn.CrossEntropyLoss()

    x = torch.randn(2, 3, 24, 24)
    target_vowels = torch.randint(0, 5, (2,))
    target_cons = torch.randint(0, 24, (2,))

    output_vowels, output_cons = model(x)
    loss_vowels = criterion_vowels(output_vowels, target_vowels)
    loss_conv = criterion_cons(output_cons, target_cons)

    loss = loss_vowels + loss_conv
    loss.backward()
