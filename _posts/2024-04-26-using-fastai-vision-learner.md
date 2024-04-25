# Using Fastai vision leaner

1. TOC
{:toc}

Fastai is a deep learning library with a significant focus on ease of use and flexibility. Under the hood, it uses PyTorch for its models and comes with common models for training. Here's how to use the `Learner` classes from `fastai.vision` to train a model.

## Loading Data
First, we need to load our data. Fastai provides several ways to do this, but one of the most common is to use the ImageDataLoaders class. This class can load images from a folder and automatically split them into a training set and a validation set.

```python
path = Path('path/to/your/data')
dls = ImageDataLoaders.from_folder(path, train='train', valid='valid')
```
However, a better way to get the dataloaders as it's often that you won't have training or testing splits prior, is to create a `DataBlock`. This is a more flexible way to load data, and can be used to load data from a variety of sources.

```python
dls = DataBlock(
    blocks=(ImageBlock, CategoryBlock), 
    get_items=get_image_files, 
    splitter=RandomSplitter(valid_pct=0.2),
    get_y=parent_label,
).dataloaders(path, bs=128)
```
Let's break this down. We create a `DataBlock` class to separate the data. The blocks arguments takes a tuple of block data types, which allows us to specify the feature and target data. The above example specifies images and categories: Hence, this would be an image classification problem. `fastai` then provides many helpers to fill out the remaining parameters. `get_items` is the method of where to get the data - for this example, we're retrieving image files from a path. The splitter is used to specify your test/train split. Unlike in the raw data loaders, you don't need to handle the splitting yourself. The `get_y` argument specifies what our target variable will be. In this case, it's the parent label of the image. - which is the folder name inside our path.
Finally, we call the `dataloaders` method on the `DataBlock` object to get our data loaders. Here we give it the path to use to retrieve the data, and can adjust our batch size accordingly.

## Creating the Learner
Next, we create a Learner using the cnn_learner function. This function takes our data loaders, a model architecture (like resnet34), and a metric to monitor (like accuracy).
```python
learn = cnn_learner(dls, resnet34, metrics=accuracy)
```

You can specify a loss function, but `fastai` has good defaults. It defaults to cross entropy loss for classification tasks, which is the standard loss function.

## Training
Training is made very simple with `fastai`.

```python
epochs = 5
learn.fine_tune(epochs)
```

This will train the model for the number of specifics epochs, plus one cycle of unfreezing. Under the hood, it's actually executing the following function for the specified number of epochs - though it does the unfreezing beforehand.

```python
learn.fit_one_cycle(epochs)
```

It's a very robust training loop, and simple to use but also very powerful. 

## Metrics and Results
The completed model can be used to predict on new data, and the results can be evaluated using the `learn.show_results()` function. This will show a few examples of the model's predictions from the validation set - so you can see the true label and actual.

```python
learn.show_results()
```

Additionally, it can predict for a single, new using:
```python
learn.predict(new_data)
```

It also stores the metric provided in the `metrics` argument when creating the learner. These can be accessed using the `learn.recorder` object. Our example used accuracy as the metric: hence, the accuracy can be accessed using:

```python
accuracies = learn.recorder.metrics
```

Furthermore, it stores the validation loss, but not the training loss by default - though this can be enabled.