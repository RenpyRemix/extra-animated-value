# Extra Animated Value

Extending AnimatedValue for Bars.

![Image of ExtraAnimatedValue Bars](explain_images/extra_animated_value.gif?raw=true)
###### Note: This gif (limited to 256 colours) is not as smooth or pretty as an in-game bar would be.

As you may or may not know, AnimatedValue is pretty limited in what it allows. Internally it just holds a value and an old value then uses a linear progression to move from one to the other over a set duration that is independent of the size of the change.

This extends that fairly limited class and allows:

- Querying (and thus display) of the current value as it changes.
- Facility to use a warper to control the movement curve.
- Ability to set a duration for the full range to change and use times as proportions of that for non full changes.
- Dynamic Text methods to easily display values taken from the class.
- A Displayable designed to help make bar images more dynamic.
