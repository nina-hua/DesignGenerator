# DesignGenerator
Final Project for MSDS Deep Learning course.

Interior design is a mix of art and science. However, there is a limit to what human designers can imagine when creating new furniture. Most designers look at past work for inspiration. With Generative Adversarial Networks, we can help create the augmented artist, enabling them to search and create in new ways. Aside from us being a data scientist, we are both interested in interior design and would like to explore potential tools that interior designers and furniture architects may benefit from.

Our goal was to bring a designer’s sketch to life. Given a sketch of a couch, our model generates a rough design - cutting the time a designer would spend in digitally rendering their design. Possible benefits that may arise from this project are equipping designers to:
- Create a polished design in as little time as possible, which allows them to prototype through plans faster
- Experiment with different styles and style combinations, saving them time during the brainstorming phase


### Contributors:
- [Nina Hua](https://github.com/nina-hua)
- [Victoria Suarez](https://github.com/vasuarez)


### Description:
To train the model, we used the Kaggle dataset [Furniture Detector](https://www.kaggle.com/akkithetechie/furniture-detector), which has 1,000 images of beds, chairs, and sofas each. These raw images are considered our ground truth during training. We then applied the Canny Edge Detection algorithm to the raw images to generate the input sketches.

**GAN Intro:**

The general structure of a GANs includes a discriminator and a generator. The discriminator’s goal is to detect when an image is real or fake (i.e the real couch or an artificially generated couch). The generator’s goal is to generate images that “trick” the discriminator. Both the discriminator and the generator have their own respective loss functions and their loss functions are fighting a min-max game against each other.

**Architecture:**

A sketch is given as an input to the generator. The generator then outputs a new image based on the sketch. The architecture of the generator is a U-Net. It compresses the sketch down to its important features and then rebuilds an image based on these features. An image and a sketch are given as inputs to the discriminator. The image could be the real or the generated image and the sketch is used to condition the discriminator's prediction. It then predicts if the image is real based on the sketch. The architecture of the discriminator is a neural network of fully connected convolutional layers.

**Loss Functions:**

The loss functions which we used are as follows. For the discriminator, the loss is the sum of the MSE of the discriminator’s prediction on the actual images (real images) plus the MSE of the discriminator’s prediction on the generated images (fake images). For the generator, the loss is the sum of the MSE of the discriminator’s predictions on the generated images and a scaled factor of the L1 difference of the pixel values of generated image vs. the actual image. The difference in the generator’s loss is that we lie and say that the generated images are real when computing the MSE of the discriminator's predictions. The lying helps our generator produce images which have the same underlying features of the real images and the scaled L1 pixel loss helps the generator produce images in the right color.

### Experiments:
For our experimentations, we tried two different generators and explored the effects of clamping v.s. not clamping.

**Different Generators:**

Both generators we tried were versions of a U-Net architecture, but the way the model goes “down” and “up” are different.

Generator A uses a max pooling layer and a double convolutional layer with batch norm and ReLU when going “down”. When going “up”, generator A uses upsampling + a double convolutional layer with batch norm and ReLU. Generator B uses a single convolutional layer with instance normalization, LeakyReLU, and dropout when going “down”, When going “up”, generator B uses a transposed convolution (i.e. learned upsampling), instance normalization, ReLU, and dropout.

Generator B produced more vivid and colorful images, but lacked in capturing the form of the couch clearly, while Generator A produced images which captured the couch structure better, but the images were dull, gray, and blurry.

**Clamping:**

Clamping is when you force the pixels to be within a range. For images, the range of pixels is either 0 to 1, if normalized, or 0 to 255, if not normalized. The generator could produce pixels out of this range, and so clamping throughout the generation model could help generate better images. Using Generator B, we found that clamping, surprisingly, worsened our generated images.

### Conclusions:
Throughout this project, we made a lot of progress. Overall, this project has taught us that the choice of loss functions can make a dramatic change in performance, clamping may not always be the best option (even if it’s the more logical one), and different generators produce dramatically different images.

Our early-stage models were producing black images. We saw the most improvement in our models when we implemented the loss functions we discussed above. Those loss functions helped bring the generator to a state where it was producing images which were tricking the discriminator, through the shape and color of the generated courches.

With more fine-tuning of the hyperparameters, such as the value to scale the L1 pixel loss, will help the model generate more realistic images.
