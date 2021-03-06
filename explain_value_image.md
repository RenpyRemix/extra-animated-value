# Explain ValueImage

So, using ExtraAnimatedValue,, we have an animating bar that knows its current_value and float percentage at any moment in time. Now suppose you wanted one or more image parts of that bar to change depending on those values.  

- Maybe you want a bar that is red up until 50% then green for the rest.  
- Maybe you want a bar where the thumb turns into a skull when the value drops below 20%.  
- Maybe you even yearn for a bar that uses images that dissolve between set pairs between set percentages.

Well, that is what ValueImage is for... All of that is possible, semi simply.

If we create the ExtraAnimatedValue as a named instance inside the screen (using `$ instance = ExtraAnimatedValue(...)`) we can then reference that instance name and pass it into a Displayable.  
When that Displayable comes to render it can use values from that instance to control what it actually displays.

Using different parameters, we can create different images that control their dynamic state based on values of the bar.  
Let's take the `Maybe`s as examples...
```py
## Maybe you want a bar that is fully red up until 50% then fully green for the rest.
    bar:
        value my_ExtraAnimatedValue_instance
        left_bar ValueImage(
            my_ExtraAnimatedValue_instance,
            ("images/bar/red_bar.png", 0.0),
            ("images/bar/green_bar.png", 0.50),
            dissolve = False
            )
        
## Maybe you want a bar where the thumb turns into a skull when the value drops below 20%. 
    bar:
        value my_ExtraAnimatedValue_instance
        thumb ValueImage(
            my_ExtraAnimatedValue_instance,
            ("images/bar/skull.png", 0.0),
            (Null(xysize=(50,50)), 0.20),
            dissolve = False
            )
        thumb_offset 25

## Maybe you even yearn for a bar that uses images that dissolve between set pairs between set percentages.
    bar:
        value my_ExtraAnimatedValue_instance
        left_bar ValueImage(
            my_ExtraAnimatedValue_instance,
            "images/bar/red_bar.png",
            "images/bar/yellow_bar.png",
            "images/bar/green_bar.png"
            )
```
First just note the first parameter is the ExtraAnimatedValue instance. This allows the displayable to read the .percent value and adjust the image it shows based on that value.

Notice on the last one how we've only specified the images and not tuples of (image, start percent). For parameters passed like that the system calculates the percent to cover the 0.0 to 1.0 range based on how many there are. For those three images it would calculate 0.0, 0.5 and 1.0.

Also see that, unlike the first two, the last one does not pass the dissolve value. This leaves it as `True (the default)` which tells the Displayable to dissolve between each image depending upon the .percent value. Thus, with `red, yellow and green` at `0.0, 0.5 and 1.0`, if the value was 0.25 the resulting image would be `red with a 50% transparent yellow on top` as it is 50% of the way from red toward yellow.

When the render method of the ValueImage is called it creates a render and then swiftly blits the image or images it needs onto that. Even with one of those images having altered opacity, this is a cheap/fast operation and should be suitable for even lower spec platforms. That render method, after the initial call for first display, is only called again during frames where the bar is changing value. When the bar is just staying at one value, this Displayable goes dormant and does not keep evaluating the same image.



### Navigation:

The overview of the system is more fully explained in [Explain ExtraAnimatedValue](explain_extra_animated_value.md)

Back to the main page [Home](README.md)
