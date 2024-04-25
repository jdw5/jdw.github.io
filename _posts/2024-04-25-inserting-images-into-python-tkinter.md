# Inserting images into Python Tkinter GUI

1. TOC
{:toc}

## Introduction
Tkinter is a minimalistic GUI which comes default with Python, able to access the underlying Tcl/Tk libraries which come as part of the operating system. This makes it great for implementing simple GUIs for tasks which don't require a lot of complexity or rely on UI/UX design.

However, it can be a bit tricky to insert images into a Tkinter GUI. Fortunately, it works great with `matplotlib` which can be used to display images - and is a staple in many Python workflows.

## Getting an Image
Let's say there's an image at `image_path` which we want to display. We begin by opening the image. We might also do some resizing to ensure it fits correctly:

```python
from PIL import Image
image = Image.open(image_path)
image.thumbnail((600, 600))
image = image.convert('L')
```

## Creating a Display Figure
We can create a `matplotlib` figure, and set the quality, aspect ratio among other things. We then just do an `imshow` as you normally would, but on the figure subplot.

```python
from matplotlib.figure import Figure
fig = Figure(figsize=(5, 5), dpi=100)
a = fig.add_subplot(111)
a.imshow(image, cmap='gray')
a.axis('off') 
```

To hook into the Tkinter GUI, we can use the `FigureCanvasTkAgg` object, which can be used to draw the canvas on the Tkinter frame.

```python
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
canvas = FigureCanvasTkAgg(fig, master=parent)
canvas.draw()
```
## Inserting to Tkinter
This will draw the image on the Tkinter frame - defined here as parent, but this can really be whatever your structural element is. Then it's as simple as packing the canvas to the frame - and that's it.
```python
import tkinter as tk
canvas.get_tk_widget().pack(side=tk.TOP, fill=tk.BOTH, expand=1)
```

## Conclusion and Example
This works identically for displaying a standard `pyplot` image, after generation within the Python script. This was a great way to implement it for my Fingerprint GUI project, found [here](https://github.com/jdw5/elec4630-fingerprint-gui). Here's an example of how the image looks from that project.

![](/images/tkinter_gui_example.png "Example tkinter gui with image")