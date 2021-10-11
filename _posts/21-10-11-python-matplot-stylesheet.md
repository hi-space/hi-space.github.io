---
title: Matplot stylesheet
tags: python
article_header:
    type: overlay
    theme: dark
    background_color: "#123"
    background_image: false
cover: /assets/images/21-10-11-python-matplot-stylesheet-5.png
---

```py
import matplotlib.pyplot as plt
plt.style.use('ggplot') # seaborn-paper, seaborn-poster
```

<!--more-->

Default로 설정되어 있는 matplot 스타일은 시각화 하기엔 그다지 예쁘지 않다. 원하는 스타일 sheet를 위와 같이 설정해서 사용해줄 수 있다. 직접 stylesheet 정의도 가능하지만 번거로우니까 이미 만들어져있는 테마를 사용하자.

Juptter notebook으로 확인해봤을 때 `ggplot`, `seaborn-paper`, `seaborn-poster`가 깔끔한 듯

# Style sheet references

![](/assets/images/21-10-11-python-matplot-stylesheet-1.png)

![](/assets/images/21-10-11-python-matplot-stylesheet-2.png)

![](/assets/images/21-10-11-python-matplot-stylesheet-3.png)

![](/assets/images/21-10-11-python-matplot-stylesheet-4.png)

![](/assets/images/21-10-11-python-matplot-stylesheet-5.png)

아래의 코드를 통해 Matplot에서 제공하고 있는 기본 stylesheet 를 미리 확인할 수 있다.

```py
import numpy as np
import matplotlib.pyplot as plt

# Fixing random state for reproducibility
np.random.seed(19680801)


def plot_scatter(ax, prng, nb_samples=100):
    """Scatter plot."""
    for mu, sigma, marker in [(-.5, 0.75, 'o'), (0.75, 1., 's')]:
        x, y = prng.normal(loc=mu, scale=sigma, size=(2, nb_samples))
        ax.plot(x, y, ls='none', marker=marker)
    ax.set_xlabel('X-label')
    ax.set_title('Axes title')
    return ax


def plot_colored_sinusoidal_lines(ax):
    """Plot sinusoidal lines with colors following the style color cycle."""
    L = 2 * np.pi
    x = np.linspace(0, L)
    nb_colors = len(plt.rcParams['axes.prop_cycle'])
    shift = np.linspace(0, L, nb_colors, endpoint=False)
    for s in shift:
        ax.plot(x, np.sin(x + s), '-')
    ax.set_xlim([x[0], x[-1]])
    return ax


def plot_bar_graphs(ax, prng, min_value=5, max_value=25, nb_samples=5):
    """Plot two bar graphs side by side, with letters as x-tick labels."""
    x = np.arange(nb_samples)
    ya, yb = prng.randint(min_value, max_value, size=(2, nb_samples))
    width = 0.25
    ax.bar(x, ya, width)
    ax.bar(x + width, yb, width, color='C2')
    ax.set_xticks(x + width)
    ax.set_xticklabels(['a', 'b', 'c', 'd', 'e'])
    return ax


def plot_colored_circles(ax, prng, nb_samples=15):
    """
    Plot circle patches.
    """
    for sty_dict, j in zip(plt.rcParams['axes.prop_cycle'], range(nb_samples)):
        ax.add_patch(plt.Circle(prng.normal(scale=3, size=2),
                                radius=1.0, color=sty_dict['color']))
    # Force the limits to be the same across the styles (because different
    # styles may have different numbers of available colors).
    ax.set_xlim([-4, 8])
    ax.set_ylim([-5, 6])
    ax.set_aspect('equal', adjustable='box')  # to plot circles as circles
    return ax


def plot_image_and_patch(ax, prng, size=(20, 20)):
    """Plot an image with random values and superimpose a circular patch."""
    values = prng.random_sample(size=size)
    ax.imshow(values, interpolation='none')
    c = plt.Circle((5, 5), radius=5, label='patch')
    ax.add_patch(c)
    # Remove ticks
    ax.set_xticks([])
    ax.set_yticks([])


def plot_histograms(ax, prng, nb_samples=10000):
    """Plot 4 histograms and a text annotation."""
    params = ((10, 10), (4, 12), (50, 12), (6, 55))
    for a, b in params:
        values = prng.beta(a, b, size=nb_samples)
        ax.hist(values, histtype="stepfilled", bins=30,
                alpha=0.8, density=True)
    # Add a small annotation.
    ax.annotate('Annotation', xy=(0.25, 4.25),
                xytext=(0.9, 0.9), textcoords=ax.transAxes,
                va="top", ha="right",
                bbox=dict(boxstyle="round", alpha=0.2),
                arrowprops=dict(
                          arrowstyle="->",
                          connectionstyle="angle,angleA=-95,angleB=35,rad=10"),
                )
    return ax


def plot_figure(style_label=""):
    """Setup and plot the demonstration figure with a given style."""
    prng = np.random.RandomState(96917002)

    (fig_width, fig_height) = plt.rcParams['figure.figsize']
    fig_size = [fig_width * 2, fig_height / 2]

    fig, axs = plt.subplots(ncols=6, nrows=1, num=style_label,
                            figsize=fig_size, squeeze=True)
    axs[0].set_ylabel(style_label)

    plot_scatter(axs[0], prng)
    plot_image_and_patch(axs[1], prng)
    plot_bar_graphs(axs[2], prng)
    plot_colored_circles(axs[3], prng)
    plot_colored_sinusoidal_lines(axs[4])
    plot_histograms(axs[5], prng)

    fig.tight_layout()

    return fig


if __name__ == "__main__":
    style_list = ['default', 'classic'] + sorted(
        style for style in plt.style.available if style != 'classic')

    for style_label in style_list:
        with plt.rc_context({"figure.max_open_warning": len(style_list)}):
            with plt.style.context(style_label):
                fig = plot_figure(style_label=style_label)

    plt.show()
```

# Refernences

- [https://matplotlib.org/stable/gallery/style_sheets/style_sheets_reference.html](https://matplotlib.org/stable/gallery/style_sheets/style_sheets_reference.html)