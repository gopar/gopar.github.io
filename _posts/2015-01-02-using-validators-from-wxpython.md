---
layout: post
date: 2015-1-2
title: "Using Validators From WxPython"
categories: wxpython
permalink: using-validators-from-wxpython
---
We'll be going over how to use the validators (PyValidator) that [WxPython](http://wxpython.org) provides. I couldn't find any tutorials online about this, the only search results I would get using Google was StackOverflow questions relating validators. Now I'm assuming that you already have wxpython installed and have some background in using it. I'll do two quick examples and those are:

* Validate user input data once "Ok" button is clicked

* My (incorrect?) version on how to validate data from current widget and other widgets together.

**Note**: You can do other stuff like sending data to and from the validator and cheking input as it is being entered.

I'll explain the second example in the list. I created a custom dialog which would let the user input two different numbers, lower and upper limits for the program I was making. In this dialog, I set up a validator that would check that the input was a number and not anything else. This was one thing I wanted the validator to do but the problem I had was how could I use validators to check that the upper limit that the user entered was truly a higher number than the lower limit? Since the way I've seen validators used was to check only one widget at a time.

Alright, so copy the `validator_example.py`

{% highlight python %}
import wx
###############################################################################
class CustomNumbers(wx.Dialog):
    """ Dialog for choosing custom numbers """
    def __init__(self, *args, **kwargs):
        super(CustomNumbers, self).__init__(*args, **kwargs)
        self.SetBackgroundColour("WHITE")
        self.widget_dict = {}

        self.initUI()
        self.SetSizerAndFit(self.main_sizer)

        self.Layout()
        self.Refresh()

    # --------------------------------------------------------------------------
    def initUI(self):
        """ """
        self.createSizer()
        self.createText()
        self.createInputBox()
        self.createButton()
        self.addSizerContent()

    # --------------------------------------------------------------------------
    def createSizer(self):
        self.main_sizer = wx.BoxSizer(wx.VERTICAL)

    # --------------------------------------------------------------------------
    def createText(self):
        """ """
        low_num_text = wx.StaticText(self, -1, "Low Number")
        high_num_text = wx.StaticText(self, -1, "High Number")

        self.widget_dict["low_num_text"] = low_num_text
        self.widget_dict["high_num_text"] = high_num_text

    # --------------------------------------------------------------------------
    def createInputBox(self):
        """ """
        low_input = wx.TextCtrl(self, validator=CustomNumValidator(),
                                name="low_input")
        high_input = wx.TextCtrl(self, validator=CustomNumValidator(),
                                 name="high_input")
        self.widget_dict["low_input"] = low_input
        self.widget_dict["high_input"] = high_input

    # --------------------------------------------------------------------------
    def createButton(self):
        """ """
        ok_btn = wx.Button(self, wx.ID_OK, "Enter")
        cancel_btn = wx.Button(self, wx.ID_CANCEL, "Cancel")

        self.widget_dict["ok_btn"] = ok_btn
        self.widget_dict["cancel_btn"] = cancel_btn

    # --------------------------------------------------------------------------
    def addSizerContent(self):
        """ """
        top_sizer = wx.BoxSizer()
        top_sizer.Add(self.widget_dict["low_num_text"], 3, wx.ALL, 10)
        top_sizer.Add(self.widget_dict["low_input"], 7, wx.ALL ^ wx.RIGHT, 10)

        btm_sizer = wx.BoxSizer()
        btm_sizer.Add(self.widget_dict["high_num_text"], 3, wx.ALL, 10)
        btm_sizer.Add(self.widget_dict["high_input"], 7, wx.ALL, 10)

        btn_sizer = wx.BoxSizer()
        btn_sizer.Add(self.widget_dict["ok_btn"], 0, wx.CENTER | wx.ALL, 10)
        btn_sizer.Add(self.widget_dict["cancel_btn"], 0,
                      wx.CENTER | wx.ALL, 10)

        self.main_sizer.Add(top_sizer)
        self.main_sizer.Add(btm_sizer)
        self.main_sizer.Add(btn_sizer, 0, wx.CENTER | wx.ALL, 10)

    # --------------------------------------------------------------------------
    def getValues(self):
        """ """
        low = self.widget_dict["low_input"].GetValue()
        high = self.widget_dict["high_input"].GetValue()
        return (int(low), int(high))


###############################################################################
class CustomNumValidator(wx.PyValidator):
    """ Validator for entering custom low and high limits """
    def __init__(self):
        super(CustomNumValidator, self).__init__()

    # --------------------------------------------------------------------------
    def Clone(self):
        """ """
        return CustomNumValidator()

    # --------------------------------------------------------------------------
    def Validate(self, win):
        """ """
        textCtrl = self.GetWindow()
        text = textCtrl.GetValue()

        if text.isdigit():
            return True
        else:
            wx.MessageBox("Please enter numbers only", "Invalid Input",
            wx.OK | wx.ICON_ERROR)
        return False

    # --------------------------------------------------------------------------
    def TransferToWindow(self):
        return True

    # --------------------------------------------------------------------------
    def TransferFromWindow(self):
        return True


if __name__ == "__main__":
    app = wx.App()
    dlg = CustomNumbers(None, -1, "Custom Numbers")
    if dlg.ShowModal() == wx.ID_OK:
        print("YES")
        dlg.getValues()
    else:
        print("wOW YOU SUCK")
    dlg.Destroy()
    app.MainLoop()
{% endhighlight %}

Alright so we have a custom dialog class along with a custom validator, `CustomNumValidator`. The way we use validators is by passing an instance of them in the parameters when we are creating a widget, in this example we pass them in the `createInputBox` method when we create the `TextCtrl` widgets. Take a look. Now in our custom validator we have a couple of methods: `Clone`, `TransferToWindow`, `TransferFromWindow` and `Validate`. All these methods are needed by any class that inherits from `wx.Pyvalidator`. The `Clone` method explains itself, and both methods, `TransferToWindow` and `TransferFromWindow`, are used to send data to and from the validator. A link that shows examples on how to use them are above.

The method that we will actually work on is the `Validate` one. In this method we get the text from the input box and check if it's a number. We do that by running the `isdigit` method on it. If it's a number we return `True`, meaning that the input the user provided is valid, and we return `False` if input was invalid. If the user provides something that is invalid we can create a message box and tell the user what happen, like in the example above. Now remember that the validator is only called when the user clicks the "Ok" button and the validator is only run once against each input box. Both input boxes are not checked at the same time, they are checked by order. If you want to validate input as it is being entered go to the example link above, there is a `onChar` method that will help you do this.

If you run this example you should see an error message popup when you enter something that is not a number. Validators are pretty easy to use this way once you've seen an example or two. Alrighty now lets move on to the second example. The only thing we have to change is the `Validate` method. Here it is.

{% highlight python %}
def Validate(self, win):
    """ """
    high, low = None, None
    for child in win.GetChildren():
        if child.GetName() == "low_input":
            low = child.GetValue()
        elif child.GetName() == "high_input":
            high = child.GetValue()

    textCtrl = self.GetWindow()
    text = textCtrl.GetValue()

    # No use for non-digit input
    if not text.isdigit():
        wx.MessageBox("Please enter numbers only", "Invalid Input",
        wx.OK | wx.ICON_ERROR)
        return False
    # Highest number cannot be smaller than the lowest number
    elif textCtrl.GetName() == "high_input" and int(high) <= int(low):
    wx.MessageBox("'High Number' must be greater than 'Low Number'",
    "Invalid Input", wx.OK | wx.ICON_ERROR)
            return False

    # All other fail cases have passed
    else:
        return True
{% endhighlight %}

Here we make use of the `win` argumnet that gets passed into the method. This variable is actually the dialog window that we are using and if we call its `GetChildren` method we will be able to iterate through them until we find the two input boxes that we need. As you can see we search for the input boxes and then save them in `low` and `high`. Next we check if the current input box we are checking has valid input (We don't care if it's the low or high input box, both need to be numerical input). The next conditional statement only runs if we are currently validating the high limit input box, it also only runs if the lower limit is greater than the upper one, since thats not supposed to happen. If all of those conditions pass, as in there werer no errors in validating the data then we say that the data inputed is valid. Now you have seen my version of how to handle this type of situation. If you have a different/official way of doing the same thing, leave a comment I would love to know how you were able to do it.
