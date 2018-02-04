---
layout: post
date: 2015-03-27
title: "Kivy's Screen Manager And Back Button"
categories: kivy
permalink: kivys-screenmanager-and-back-button
---

This is a follow up post to my previous one where I discuss [how to capture the back button press in Kivy.](http://www.pygopar.com/kivy-and-the-back-button/) In this post we will go over an example on how to use that knowledge to check if we can go back to any previous screen, just like any other regular Android/iOS app. First lets create two files, `main.py` and `example.kv`.

In `main.py` put the following:

{% highlight python %}
from kivy.app import App
from kivy.core.window import Window
from kivy.uix.boxlayout import BoxLayout
from kivy.properties import ObjectProperty


################################################################################
class ExampleRoot(BoxLayout):
    """ Root widget for app """
    screen_manager = ObjectProperty(None)

    def __init__(self, *args, **kwargs):
        super(ExampleRoot, self).__init__(*args, **kwargs)
        # list to keep track of screens we were in
        self.list_of_prev_screens = []

    # --------------------------------------------------------------------------
    def onNextScreen(self, btn, next_screen):
        # add screen we were just in
        self.list_of_prev_screens.append(btn.parent.name)
        # Go to next screen
        self.screen_manager.current = next_screen

################################################################################
class ExampleApp(App):
    """ App to show how to use back button """

    def __init__(self, *args, **kwargs):
        super(ExampleApp, self).__init__(*args, **kwargs)

    # --------------------------------------------------------------------------
    def build(self):
        return ExampleRoot()

if __name__ == "__main__":
    ExampleApp().run()
{% endhighlight %}

The only thing that I feel needs to be explained is the `onNextScreen` method. This will be called from our `kv` file which we will create in just a moment, and its sole purpose is to switch screens and add the current screen it's in to `list_of_prev_screens`. This variable will keep a record of visited screens so that we can go to them when the user presses the beloved back button.

Now in `example.kv` put the following:

{% highlight python %}
<ExampleRoot>:
    screen_manager: screen_manager
    ScreenManager:
        id: screen_manager
        Screen:
            name: "screen_hello"
            Button:
                text: "Hello Mate"
                on_release: root.onNextScreen(self, "screen_scream")
        Screen:
            name: "screen_scream"
            Button:
                text: "SCREAM"
                on_release: root.onNextScreen(self, "screen_spider_pig")
        Screen:
            name: "screen_spider_pig"
            Button:
                text: "Spider Pig!"
{% endhighlight %}

Pretty simple stuff so far, and as you can see we pass an instace of the button itself in `onNextScreen` and the name of screen we want to go to next. If you run the app right now, you should be able to iterate through the screens but when you press the back button (in a deskop, press `Esc` to simulate a back button press) the app should just close. Alright, now lets do all the binding and make it possible to go back to previous screens!

`main.py`:

{% highlight python %}
class ExampleRoot(BoxLayout):
    # Previous code
    # --------------------------------------------------------------------------
    def onBackBtn(self):
        # Check if there are any screens to go back to
        if self.list_of_prev_screens:
            # If there are then just go back to it
            self.screen_manager.current = self.list_of_prev_screens.pop()
            # We don't want to close app
            return True
        # No more screens so user must want to exit app
        return False

class ExampleApp(App):
    # Previous code
    # --------------------------------------------------------------------------
    def onBackBtn(self, window, key, *args):
        """ To be called whenever user presses Back/Esc key """
        # 27 is back press number code
        if key == 27:
            # Call the "OnBackBtn" method from the "ExampleRoot" instance
            return self.root.onBackBtn()
        return False
{% endhighlight %}

Sweet! If you run it now, you should be able to go back to previous screens and once there are no more screens to go to, it should just close. Pretty easy stuff, huh? I won't go over the code since it's small and the I think the comments describe what is happening pretty well.
