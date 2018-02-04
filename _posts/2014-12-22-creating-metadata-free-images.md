---
layout: post
date: 2014-12-22
title: "Creating Metadata Free Images"
permalink: creating-metadata-free-images
categories: python
---

In this post we'll go over how to view metadata in images and how to create a *new* metadata-free image from it. If you don't know what metadata is, it's basically described as "data about data". What this means is that it hold information about the data, in this case the image, such as the time the image was taken, camera used, model of camera, GPS location, etc. In images, I've found out that this is commonly known as *exif* data.

Before we begin you need to install *Pillow*, it's the library we'll be using in this tutorial. Also a quick note, I'm using Python 2 but I tested out the code in Python 3 and it should work just fine. You can install Pillow through pip: `pip install Pillow` or if you're in Windows you can use [this site](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pillow).

We'll begin by creating a file, `metadata.py`, and creating a function to view the metadata first.

{% highlight python %}
# 3rd party libs
from PIL import Image
from PIL.ExifTags import Tags, GPSTAGS  # Dictionaries containing key codes

def getMetadata(file_path):
    '''
    Read metadata (exif) from images and return it
    :param file_path: file path to where image is at
    :return: metadata from image
    '''
    # Open Image
    img = Image.open(file_path)
    # Grab metadata from image if any
    exif_data = img._getexif()
    # if image has metadata do this
    if exif_data is not None:
        # make metadata 'readable'
        exif_data = {TAGS[k] if k in TAGS  # Known generic exif codes
                     else GPSTAGS[k] if k in GPSTAGS  # Known GPS exif codes
                     else k: v for k, v in exif_data.items()}
    return exif_data

{% endhighlight %}

Here everything is straight forward, we open the image, read it for metadata and if doesn't have any it'll simply return `None`. If it has data we do a dictionary comprehension running the data we retrieved with the commonly known exif keys. If you want to see what is happening, you can print `exif_data` before and after we run the dictionary comprehension. Also here is file that contains [those keys](https://github.com/python-pillow/Pillow/blob/master/PIL/ExifTags.py) if you're curious.

To test our new fuction, lets add this to the end of the file:

{% highlight python %}
if __name__ == '__main__':
    from pprint import pprint as _ # This modules makes printing stuff pretty
    path = r'path/to/image.jpg'
    metadata = getMetadata(path)
    # print out the metadata
    _(metadata)
{% endhighlight %}

If you want, you can download this image and test it out. It's the local rodeo from where I live. It has some metadata.
![](/assets/images/posts/rodeo.jpg)

The `pprint` library is something I use when I'm printing stuff to the screen. It just makes the output 'pretty', just like it's name. Next we give the path to where the image is located, pass that to the function and then print the metdata we received. This function serves well if you just want to see what metadata you have in your image, but what if you want to create a new image **without** the metadata? The next function we will build takes care of that. Let's make it

{% highlight python %}
def createCleanImage(file_path):
    '''
    Create a metadata free image from image path given
    :param file_path: path of file to create a 'clean' copy.
    :return: path of 'clean' image
    '''
    # Open Image
    img = Image.open(file_path)

    # Next 3 lines strip metadata
    data = list(img.getdata())  # Get pixels
    # Create image with same size and mode only
    clean_img = Image.new(img.mode, img.size)
    # Put the pixels in here
    clean_img.putdata(data)

    # Save file with 'CLEAN_' appended to the font of file name
    path, filename = os.path.split(file_path)
    name, extension = filename.split('.')
    filename = "CLEAN_{name}.{ext}".format(name=name, ext=extension)

    path = os.path.join(path, filename)
    # quality=100, if you don't want your image to be compressed
    # If not passed, it uses the default compression ratio
    clean_img.save(path)
{% endhighlight %}

Here we are getting the pixels from the image we want, create a new template image with the same size and mode so everything fits together and then finally throw the pixel data into the new image. This way, no metadata is copied over. The next couple lines just create a path string where your file should be save. It'll also be saved under a different name. The last line is the bit of code that actually saves the image to your computer. The `save` method takes in a couple of optional parameters, you can read them [here](http://pillow.readthedocs.org/en/latest/handbook/image-file-formats.html#jpeg), but basically if you don't want your file to be compressed, pass in `quality=100` else just leave it as is.

Lets test this out.

{% highlight python %}
if __name__ == "__main__":
    from pprint import pprint as _
    path = r'/path/to/image/rodeo.jpg'
    metadata = getMetadata(path)
    _(metadata)
    # If image has metadata, lets create one that doesn't
    if metadata is not None:
        print("Creating clean image")
        createCleanImage(path)
{% endhighlight %}

If you run this you should be able to see the metadata the file contained and then after a new image created in the same directory. If you check the metadata on the new image you will see that it won't have any. Also, if you din't pass `quality=100` you should be able to see the size of image changed drastically. It was 2.2MB and now it's only .5MB, this happened because of the defaul compresion that happens with jpg. There you go, you now know how to create metadata free images! unfortunately, I don't think that you are able to edit the metadata from Pillow. From what I've read, you would have to use a library called [pexif](https://github.com/bennoleslie/pexif).
