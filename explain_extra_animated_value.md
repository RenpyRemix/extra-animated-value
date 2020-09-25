# Explain ExtraAnimatedValue

Like AnimatedValue, instances of ExtraAnimatedValue are created inside the actual screen rather than as defaults or defines. They seem to use the screen refresh/redraw as a way of governing the oldwidget-->newwidget replacement cycle which is then used to control the display.

As mentioned on the ReadMe/Home page, there are several extra features within , so there are also extra parameters that can be declared.
```py
default extra_animated_value_points = 500
default extra_animated_value_max = 1000

screen extra_animated_value_screen():

    $ bar_val = ExtraAnimatedValue(
                    # the variable that controls the value we want shown (after movement/animation completes)
                    value=extra_animated_value_points, 
                    
                    # the maximum value (this could be hard coded rather than using a variable)
                    range=extra_animated_value_max, 
                    
                    # The time it takes to animate the value, in seconds. Defaults to 1.0.
                    # Not needed if using range_delay
                    # delay = 1.5,

                    # The time it takes to animate the value, in seconds across the full
                    # range. Overrides `delay` if set. Defaults to None
                    range_delay=2.5,

                    # String warper name to use to animate between values
                    warper="easein_elastic"
                    )
```
#### Now for the extra features we added in.

So, let's tackle this one at a time, feature by feature. The class after all is just a copy of AnimatedValue with some methods over loaded and a couple of new ones added.

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

Now we come to the utility methods which are just here to make it easier to display the internal values without needing to write a DynamicDisplayable for each.  
```py
        @property
        def percent(self):
            """
            Percent expressed as a float between 0.0 and 1.0
            (suitable for use with PyFormat :.01% syntax)
            """
            return float(self.current_value) / float(self.range)

        def text(self, format="{.current_value:.0f}", **kwargs):
            return DynamicDisplayable(self.dynamic_text, 
                                      format=format, 
                                      **kwargs)

        def dynamic_text(self, st, at, **kwargs):
            value = kwargs.pop('format', "{.current_value:.0f}").format(self)
            delay = (0.1 if self.value == self.current_value else 0.02)
            return Text(value, **kwargs), delay
```
###### percent(self):

A property method (so we can call it just using `instance_name.percent`) that returns a percentage expressed as a float which is nicely suitable for the `:.01%` format syntax. This allows us to use it inside the format paramter of `instance_name.text()` calls to display the current percentage easily.

###### text(self, format, **kwargs):

This is the method that we actually call to show a text widget to display a dynamic value from the bar. It simply passes the parameters supplied to it into a DynamicDisplayable (which updates at 10fps when stationary and 50fps when the bar is moving) and returns the Text displayable that is created.  
We can reference values from the bar within the `format` value, either singly as `{percent:.02f}` or using 0. as a prefix for more than one value like below.
```py
                add bar_val.text(
                    "{0.current_value:.0f}/{0.range:.0f}",
                    size = 22,
                    color = "#DDE",
                    outlines = [(abs(1), "#222")],
                    bold = True,
                    xcenter = 0.5,
                    ycenter = 0.5)
```
As you can see, after the `format` argument the rest are interpreted as style keywords and are passed into the Text widget.  
For yours, I'd advise creating styles and just passing in the style or style_prefix (or let the screen do it hierarchically)

### Navigation:

The additional image displayable is explained in [Explain ValueImage](explain_value_image.md)

Back to the main page [Home](README.md)
