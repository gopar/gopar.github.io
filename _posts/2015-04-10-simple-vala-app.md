---
layout: post
date: 2015-04-10
title: "Simple Vala App"
categories: vala
permalink: simple-vala-app
---

In this post we'll go over how to create a simple [Vala](https://wiki.gnome.org/Projects/Vala) application. This app will just act as a counter. For example, whenever a user presses the "+" button the counter is incremented, the reverse happens with the "-" button.

First of we'll create a file named `simple.vala`. And in it we'll put the following:

{% highlight vala %}
// Including the Gtk namespace into the program
using Gtk;

// Create the window class and inherit from Gtk.Window
class Simple: Gtk.Window{
    // Set up constructor
    public Simple(){
        // Set window title
        this.title = "hello";
        // End the program, when user quits
        this.destroy.connect(Gtk.main_quit);
        // Center the window on screen
        this.window_position = WindowPosition.CENTER;
        // Set the size for window (width, height)
        this.set_default_size(350, 70);
    }
}

int main(string[] args){
    // Prep the Gtk library for use
    // "ref" is Valas way of passing a pointer of "args"
    Gtk.init(ref args);
    // Create instance from the class we created
    Simple app = new Simple();
    // Tell the window to show it self
    app.show_all();
    // Start the main gui event loop
    Gtk.main();
    return 0;
}
{% endhighlight %}

Now to run the program just type
{% highlight vala %}
vala --pkg=gtk+-3.0 simple.vala

{% endhighlight %}
You should get something that looks like this
![](/assets/images/posts/screen-1.png)

Sweet! We have a window. Next, is to add some buttons, text, and something else called a `Box` which is specifially made to hold widgets such as the ones we are going to use.
{% highlight vala %}
class Simple: Gtk.Window{
    // New Global var
    Gtk.Label label;
    public Simple(){
        // PREVIOUS CODE HERE
        // ******************
        // Create Buttons
        Gtk.Button add_btn = new Gtk.Button.with_label("+");
        Gtk.Button subtract_btn = new Gtk.Button.with_label("-");
        // Create Label
        label = new Gtk.Label("0");
        // Create BOX to hold all item
        Gtk.Box vert_box = new Gtk.Box(Gtk.Orientation.VERTICAL, 5);
        // Add button and label to BOX, the order matters
        vert_box.add(add_btn);
        vert_box.add(label);
        vert_box.add(subtract_btn);
        // add BOX to the window
        this.add(vert_box);
    }
{% endhighlight %}

Creating buttons and labels is pretty straight forward, the thing that needs some explaining is the `vert_box` variable. We need a `Gtk.Box` instance variable, such as `vert_box`, because the window is only capable of holding one widget at a time. Since `Gtk.Box` widgets are made to hold other widgets, this solves our problem of displaying multiple widgets at a time. The `Gtk.Box` is created with `Gtk.Orientation.VERTICAL` meaning that it will place widgets in a stack from top to bottom, and the number 5, which means how much *spacing* to give in between widgets.

If you run the code you should see the following like this.

![](/assets/images/posts/window-1.png)

If you press the buttons, you'll see that they do nothing. The reason for this is because we haven't told them to do anything yet, so lets do that next.

{% highlight vala %}
class Simple: Gtk.Window{
    public Simple(){
        // PREVIOUS CODE HERE
        // ******************
        // Tell buttons what to do when they are pressed
        add_btn.clicked.connect(onClick);
        subtract_btn.clicked.connect(onClick);
    }

    // Method that will be called when btns are pressed
    private void onClick(Gtk.Button btn){
        // Convert the string from label to number
        int num = int.parse(label.get_label());
        // Add or subtract depending on which btn was clicked
        (btn.get_label() == "+")? num += 1: num -= 1;
        // Set the new result to the label
        label.set_label(num.to_string());
    }
}
{% endhighlight %}

The way to bind buttons is by doing `btn_var.clicked.connect(method_to_call)` and in this case `onClick`, will be the method called when the buttons are pressed. What happens inside of that method is pretty straight forward. One thing that you may of asked is how did `onClick` receive an argument if we never said we were going to pass one? The answer is simple, by default whichever method you bind to, it can either have no arguments or it passes the instance that cause the event.

If you run the program now, you should be able to change the label depending on which button you are pressing. Well that is all for this vala tutorial. Here is the [complete code for this post](https://github.com/gopar/PyGopar-Website/blob/master/code_from_posts/Simple%20Vala%20App/simple.vala)
