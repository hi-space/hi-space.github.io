---
title: 💡 Paper Eval Graph
tags: paper 💡 🔥
mathjax_autoNumber: false
article_header:
    type: overlay
    theme: dark
    background_color: "#123"
    background_image: false
cover: /assets/images/21-09-24-paper-2021-09-25-00-56-13.png
---

```sh
# cityscapes epoch 20000
# 2975: 68.03 (epoch: 20,) / 69.12 (epoch: 30,)
# 2000: 56.68 (epoch: 20,)
# 1000: 58.87 (epoch: 20,)
# 500: 55.35 (epoch: 20,)
# 100: 40.5 (epoch: 20,) / 41.63 (epoch: 40,)
```

```sh
# 제안 모델
# 2975: 48.39 (epoch: 20,) / 56.39 (epoch 40,)
# 1000: 48.1 (epoch 20,)
# 500: 49.82 (epoch 20,) / 51.6 (epoch 30,)
# 100: 45.97 (epoch 20,) / 49.19 (epoch 40,)
```

```chart
{
  "type": "line",
  "data": {
    "labels": [
      "2975",
      "1000",
      "500",
      "100"
    ],
    "datasets": [
      {
        "label": "Seg Model",
        "data": [
          68.03,
          58.87,
          55.35,
          40.5
        ],
        "fill": false,
        "backgroundColor": "rgba(255,99,132,1)",
        "borderColor": "rgba(255,99,132,1)",        
        "borderWidth": 2,
        "pointStyle": "circle",
        "pointRadius": 5,
        "lineTension": 0.4
      },
      {
        "label": "DA Model",
        "data": [
          56.39,
          48.10,
          51.6,
          49.19
        ],
        "fill": false,
        "backgroundColor": "rgba(54, 162, 235, 1)",
        "borderColor": "rgba(54, 162, 235, 1)",
        "borderWidth": 2,
        "pointStyle": "circle",
        "pointRadius": 5,
        "lineTension": 0.1,
        "lineTension": 0.4
      }
    ]
  },
  "options": {
    "legend": {
      "position": "bottom",
      "display": true
    },
    "title": {
      "display": true,
      "text": "saa",
      "position": "top"
    },
    "scales": {
      "x": {
        "display": true,
        "title": {
          "display": true,
          "text": "'Month'"
        }
      },
      "y": {
        "display": true,
        "title": {
          "display": true,
          "text": "'Value'"
        }
      }
    }
  }
}
```
