# Explain ExtraAnimatedValue

So, let's tackle this feature by feature. The class after all is just a copy of AnimatedValue with some methods over loaded and a couple of new ones added.

#### Querying (and thus display) of the current value as it changes.

To add this, a new attribute is included called current_value.  
Then, rather than just altering the display by using the value and old_value we compute that current value, store it as current_value then alter the display.
```py
                self.current_value = self.old_value \
                                   + fraction * (self.value - self.old_value)

            self.adjustment.change(self.current_value)
```

#### Facility to use a warper to control the movement curve.

Once again we add another attribute to the class and allow it to be passed in during instantiation.
```py
        def __init__(self, ...., warper="linear"):
            ...
            self.warper = warper
```
Then, when we come to calculating the display proportion, we reference that named warper against the base fraction.
```py
            ... test, then if it is still moving re-evaluate using the warper
            fraction = renpy.atl.warpers[self.warper](base_fraction)
```

#### Ability to set a duration for the full range to change and use times as proportions of that for non full changes.

Rather than just delay, we include a passable attribute called range_delay. With this we can set a duration for movement across the entire bar and then calculate partial movements as fractions of that.  
We can say the range_delay is 5.0 and expect a 50% movement to take just 2.5 seconds... Keeping the speed static.

All those were just small tweaks and additions to the `__init__` and `periodic` methods. Quite simple stuff that adds a little extra.

#### Dynamic Text methods to easily display values taken from the class.

Now we 

### Navigation:

The additional image displayable is explained in [Explain ValueImage](explain_value_image.md)

Back to the main page [Home](README.md)
