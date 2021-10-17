---
title: Pytorch Tensor 이미지 시각화 (make_grid)
tags: pytorch
article_header:
    type: overlay
    theme: dark
    background_color: "#123"
    background_image: false
# cover: /assets/images/21-10-03-pytorch-image-visualize-2021-10-03-23-41-37.png
---

```python
import torchvision
import matplotlib.pyplot as plt

plt.imshow(torchvision.utils.make_grid(images.cpu(), normalize=True).permute(1,2,0))
plt.show()
```

`make_grid`는 image grid를 포함한 tensor를 반환해준다. batch의 갯수만큼 포함해서 하나의 이미지로 출력해준다.

<!--more-->

> [make_grid documents](https://pytorch.org/vision/stable/utils.html#torchvision.utils.make_grid)

![](/assets/images/21-10-03-pytorch-image-visualize-2021-10-03-23-15-38.png)

## Multiple Images

![](/assets/images/21-10-03-pytorch-image-visualize-2021-10-03-23-41-37.png)

```python
import torchvision
import matplotlib
import matplotlib.pyplot as plt

matplotlib.use('TkAgg')

fig = plt.figure('dataloader')

ax1 = fig.add_subplot(2, 1, 1)
ax2 = fig.add_subplot(2, 1, 2)

ax1.imshow(torchvision.utils.make_grid(images.cpu(), normalize=True).permute(1,2,0))
ax2.imshow(torchvision.utils.make_grid(images_t.cpu(), normalize=True).permute(1,2,0))

ax1.axis('off')
ax2.axis('off')

ax1.set_title('GTA')
ax2.set_title('Cityscapes')

# non-blocking 하게 plt를 그리고 update 하기 위함
plt.draw()
plt.pause(0.001)
```

`make_grid` 함수가 Tensor를 List로 받을 수도 있지만 matplot의 figure를 사용해 따로 출력해줄 수도 있다.

## Prediction visualize

### Save Image files

```py
fig = plt.figure('eval')
self.ax1, self.ax2, self.ax3 = fig.add_subplot(1, 3, 1), fig.add_subplot(1, 3, 2), fig.add_subplot(1, 3, 3)
self.ax1.axis('off'), self.ax2.axis('off'), self.ax3.axis('off')
self.ax1.set_title('train input'), self.ax2.set_title('train output'), self.ax3.set_title('train gt')

labels = labels.cpu()
labels[labels==255] = 0

self.ax1.imshow(torchvision.utils.make_grid(images[0, :, :, :].cpu(), normalize=True).permute(1,2,0))
self.ax2.imshow(torch.argmax(pred1, 1)[0:1, :, :].cpu().permute(1,2,0))
self.ax3.imshow(labels[0:1, :, :].permute(1,2,0))

plt.draw()
plt.savefig('eval.png')
```

### Save to tensorboard

```py
import torchvision
from PIL import Image

palette = [128, 64, 128, 244, 35, 232, 70, 70, 70, 102, 102, 156,
           190, 153, 153, 153, 153, 153, 250,
           170, 30,
           220, 220, 0, 107, 142, 35, 152, 251, 152,
           70, 130, 180, 220, 20, 60, 255, 0, 0, 0, 0,
           142, 0, 0, 70,
           0, 60, 100, 0, 80, 100, 0, 0, 230, 119, 11, 32]
zero_pad = 256 * 3 - len(palette)
for i in range(zero_pad):
    palette.append(0)


def colorize_mask(mask):
    # mask: numpy array of the mask
    new_mask = Image.fromarray(mask.astype(np.uint8)).convert('P')
    new_mask.putpalette(palette)
    return new_mask


def draw_in_tensorboard(writer, images, i_iter, pred_main, num_classes, type_):
    grid_image = torchvision.utils.make_grid(images[:3].clone().cpu().data, 3, normalize=True)
    writer.add_image('Image - {type_}', grid_image, i_iter)

    grid_image = torchvision.utils.make_grid(torch.from_numpy(np.array(colorize_mask(np.asarray(
        np.argmax(F.softmax(pred_main, dim=1).cpu().data[0].numpy().transpose(1, 2, 0),
                axis=2), dtype=np.uint8)).convert('RGB')).transpose(2, 0, 1)), 3,
                        normalize=False, value_range=(0, 255))
    writer.add_image('Prediction - {type_}', grid_image, i_iter)

    output_sm = F.softmax(pred_main, dim=1).cpu().data[0].numpy().transpose(1, 2, 0)
    output_ent = np.sum(-np.multiply(output_sm, np.log2(output_sm)), axis=2,
                        keepdims=False)
    grid_image = torchvision.utils.make_grid(torch.from_numpy(output_ent), 3, normalize=True,
                        value_range=(0, np.log2(num_classes)))
    writer.add_image('Entropy - {type_}', grid_image, i_iter)

draw_in_tensorboard(writer, images, i_iter, pred2, 19, 'S')
draw_in_tensorboard(writer, images_t, i_iter, pred_target2, 19, 'T')
```

## Troubleshooting

```
<stdin>:1: UserWarning: Matplotlib is currently using agg, which is a non-GUI backend, so cannot show the figure.
```

matplot으로 show 했을 때 위와 같은 에러가 발생할 수 있다. 

```sh
sudo apt install python3-tk
```

```python
import matplotlib
matplotlib.use('TkAgg')
```

GUI backend 로 사용할 모듈을 설치해주고, backend 모듈로 `TkAgg`를 사용한다고 명시해주면 된다.