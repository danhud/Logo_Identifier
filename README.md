# Band Logo Identifier

![](https://drive.google.com/uc?export=view&id=1uK2TdenSsdZ1rBB334NJ3U0kZidgbfII)

Metal bands, especially in niche subgenres, often use logos that are difficult to decipher. Despite this, fans are quick to display their allegiance through merchandise,
sometimes exclusively wearing items from bands they like. A great example of this can be seen in the book Defenders of the Faith by Peter Beste, which showcases jackets 
adorned with band patches. You can check it out [here](https://www.peterbeste.com/shop/defenders-of-the-faith-limited-edition).

Now, imagine you come across someone sporting patches from bands you enjoy, along with a few from bands you don't recognize. Given the overlap in your tastes, 
it’s likely you’d enjoy some of the unfamiliar bands as well. The problem? The logos are so hard to read that you can't identify the band or look them up later.

The goal of this project was to make a small vector database of metal band logos, with the purpose of being a proof of concept for an app idea where you could 
upload the image of a unknown band's logo, and have it return the name and details of the band corresponding to the logo. Currently there are 686 bands in the database, which 
is a small fraction of the total bands that one could ask for. The bands in the database are listed in ```included_bands.csv```, and the main notebook is ```Identifier_App```; note that currently the model takes up half a gigbyte of space, so I you can get it [here](https://drive.google.com/drive/folders/1GImWyJTMKJaBGEKJXTT3bI2AhAcdQYys?usp=sharing).

I should mention that this project was possible becuase of the dataframe ```metal_bands_roster.csv```, which I downloaded from [Kaggle](https://www.kaggle.com/datasets/guimacrlh/every-metal-archives-band-october-2024?resource=download&select=metal_bands_roster.csv); thanks to Reddit user **lmarso** for posting this!

### Dataset

The dataset for this project was the logos of 686 bands, which I downloaded from [the Metal Archives](https://www.metal-archives.com/). In order to supplement this, I wrote scripts to 
augment this dataset so that I had 20 'distinct' examples for each band. The way I augmented a bands photo depended on whether the logo was black and white or colour. If the logo was 
in colour, I randomly skewed, stretched, and applied colour jitters to get some variance in the colour. If the logo was black and white, I first made a copy replacing the white
pixles with a random copy, and then applied my colour augmentation scheme. This was done in the ```Colour_Image_Augmentor``` and ```BW_Image_Augmentor``` notebooks. 

The labels of my images are of the form:

```
This is an image of the logo for [band name], who are a [genre] band
from [location]. The band ID for this band is [metallum band ID].
```

The labels were generated in the ```Data_Annotator``` notebook. The choice of these particular labels was due to my choice of model, explained below. 

### Model 

For this project, I selected the [CLIP model](https://openai.com/index/clip/) provided by OpenAI through the HuggingFace Transformers library. My decision was based on two main factors. First, my end goal was to create a vector database for my band logos, and the CLIP architecture is particularly well-suited for this purpose. Second, many metal band logos 
share similar design elements, so I appreciated how CLIP uses differences in the labels to ensure that distinct logos have a significant cosine dissimilarity.

### Fine-tuning

After augemnting my images my dataset had 14686 images, 70% of which I used for training and 30% for validating. I fine-tuned using [contrastive loss](https://medium.com/towards-data-science/contrastive-loss-explaned-159f2d4a87ec) for 30 epochs on a smaller dataset, and then for 60 epochs on a larger dataset; this was done in the ```fine_tuner_v2``` notebook. For the second training loop, I used a learning rate scheduler to help improve learning. In the end, the two training loops combined took just under 3 hours using an A100 GPU through Google Colab. The optimal training and validation losses were:

```Training Loss: 0.0129 - Validation Loss: 0.0393```.

### Testing 

I tested the model on a collection of 68 images of band merch that I took screensshots of. Not all the bands in this collectetion were included in my dataframe, and some of the logos for bands in the dataframe had since been changed. Nonetheless, it achieved 81% accuracy. The images I used are in the ```Test Images``` folder. 

### Outlook

Overall, I'm pleased with the results. I think that my results would dramatically improve with more images of actual band merchandise, but for time reasons this is out of the question. 
As for constructing a vector database with all the bands on the Metal Archives, I think this would be quite a task. As of November 2024, there are more than 180k bands on the website. 
Crude estimations show that there are about ~6GB of band logos. If I were to repeat my regime on this who dataset, it would take well over 200 hours to train which is difficult to justify. In any case, this was a fun project that does pretty well (in my opinion) and taught me a lot!



