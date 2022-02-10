# The fastai book

> References:
>
> https://github.com/fastai/fastbook
>
> https://learning.oreilly.com/library/view/deep-learning-for/9781492045519/



Training a machine learning model:![image-20220204110930690](.fastbook-images/image-20220204110930690.png)

Detailed training loop:

![image-20220207095219011](.fastbook-images/image-20220207095219011.png)

A classification model is one that attempts to predict a class, or category. That is, it’s predicting from a number of discrete possibilities, such as “dog” or “cat.”

A *regression model* is one that attempts to predict one or more numeric quantities, such as a temperature or a location.

Example of overfitting:

![Example of overfitting](.fastbook-images/dlcf_0109.png)

A *metric* is a function that measures the quality of the model’s predictions using the validation set, and will be printed at the end of each epoch:

-  `error_rate`tells you what percentage of images in the validation set are being classified incorrectly.
-  `accuracy` (which is just `1.0 - error_rate`)

*Machine learning* is a discipline in which we define a program not by writing it entirely ourselves, but by learning from data.

*Deep learning* is a specialty within machine learning that uses *neural networks* with multiple *layers*.

*Image classification* is a representative example. We start with *labeled data*—a set of images for which we have assigned a *label* to each image, indicating what it represents. Our goal is to produce a program, called a *model*, that, given a new image, will make an accurate *prediction* regarding what that new image represents.

Every model starts with a choice of *architecture*, a general template for how that kind of model works internally. The process of *training* (or *fitting*) the model is the process of finding a set of *parameter values* (or *weights*) that specialize that general architecture into a model that works well for our particular kind of data. To define how well a model does on a single prediction, we need to define a *loss function*, which determines how we score a prediction as good or bad.

To make the training process go faster, we might start with a *pretrained model*—a model that has already been trained on someone else’s data. We can then adapt it to our data by training it a bit more on our data, a process called *fine-tuning*.

When we train a model, a key concern is to ensure that our model *generalizes*: it learns general lessons from our data that also apply to new items it will encounter, so it can make good predictions on those items. The risk is that if we train our model badly, instead of learning general lessons, it effectively memorizes what it has already seen, and then it will make poor predictions about new images. Such a failure is called *overfitting*.

To avoid this, we always divide our data into two parts, the *training set* and the *validation set*. We train the model by showing it only the training set, and then we evaluate how well the model is doing by seeing how well it performs on items from the validation set. In this way, we check if the lessons the model learns from the training set are lessons that generalize to the validation set. In order for a person to assess how well the model is doing on the validation set overall, we define a *metric*. During the training process, when the model has seen every item in the training set, we call that an *epoch*.

*categorical* (contain values that are one of a discrete set of choices, such as `occupation`) versus *continuous* (contain a number that represents a quantity, such as `age`).

---

Split our dataset into two sets: the *training set* (which our model sees in training) and the *validation set*, also known as the *development set* (which is used only for evaluation). Introduce another level of even more highly reserved data: the *test set*.

---

Computer vision:

- recognize items in an image at least as well as people can - object recognition
- where objects in an image are, and can highlight their locations and name each found object - object detection
- synthetically generate variations of input images, such as by rotating them or changing their brightness and contrast - data augmentation

---

In a cell, typing `?func_name` - the signature of the function and a short description.

In a cell, typing `??func_name` - the signature of the function, a short description, and the source code.

If you are using the fastai library: `doc(*func_name*)` in a cell will open a window with the signature of the function, a short description, and links to the source code on GitHub and the full documentation of the function in the [library docs](https://docs.fast.ai).

Type `%debug` in the next cell and execute to open the [Python debugger](https://oreil.ly/RShnP), which will let you inspect the content of every variable.

---

`DataLoaders` is a thin class that just stores whatever `DataLoader` objects you pass to it and makes them available as `train` and `valid`.

```
class DataLoaders(GetAttr):
    def __init__(self, *loaders): self.loaders = loaders
    def __getitem__(self, i): return self.loaders[i]
    train,valid = add_props(lambda i,self: self[i])
```

---

*Data augmentation* refers to creating random variations of our input data, such that they appear different but do not change the meaning of the data. Examples of common data augmentation techniques for images are rotation, flipping, perspective warping, brightness changes, and contrast changes.

---

```
interp = ClassificationInterpretation.from_learner(learn)
interp.plot_confusion_matrix()
```

![img](.fastbook-images/dlcf_02in07.png)

Therefore, the diagonal of the matrix shows the images that were classified correctly, and the off-diagonal cells represent those that were classified incorrectly. 

---

Creating a Notebook App from the Model

- IPython widgets (ipywidgets) - GUI components
- Voilà

---

Cells that begin with a `!` do not contain Python code, but instead contain code that is passed to your shell (bash, Windows PowerShell, etc.).

---

Measurement bias