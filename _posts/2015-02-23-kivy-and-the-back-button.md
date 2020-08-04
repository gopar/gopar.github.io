---
layout: post
date: 2015-02-23
title: "Kivy And The Back Button"
categories: kivy
permalink: kivy-and-the-back-button
---

[Kivy](http://kivy.org/#home) gives the ability to create not only desktop apps but mobile ones as well. And when you're creating apps, specifically mobile ones, you'll want to be able to do other things when the back button is pressed. For example on an Android app you might want to switch back to a previous screen instead of the default which is exiting the app completely.

To be able to capture/hook the back button press or the equivalent of it which is the Esc key, you must first bind an event with the current window to do something whenever the keyboard is pressed. Here is an example of how it should look like:

```python
from kivy.app import App
from kivy.uix.button import Button
from kivy.core.window import Window # Need this import


class SimpleApp(App):
    def __init__(self, **kwargs):
        super(SimpleApp, self).__init__(**kwargs)
        # Setting it up to listen for keyboard events
        Window.bind(on_keyboard=self.onBackBtn)

    def onBackBtn(self, window, key, *args):
        """ To be called whenever user presses Back/Esc Key """
        # If user presses Back/Esc Key
        if key == 27:
            # Do whatever you need to do, like check if there are any
            # screens that you can go back to.
            # return True if you don't want to close app
            # return False if you do
            return True
        return False

    def build(self):
        return Button(text="Can't Close Me")

if __name__ == "__main__":
    SimpleApp().run()
```

There you go! Pretty simple to do. Hope this helps some people.

*Note*: I have made another post on how to use this with Kivy's ScreenManager. Check it out [here](http://www.pygopar.com/kivys-screenmanager-and-back-button/)
