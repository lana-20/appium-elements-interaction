# Interacting with Mobile Elements in Appium

*Mobile elements allow all the same types of interaction as web elements: click, send keys, and so on.*

let's talk about what we can do with the elements we found.

The main point here is that element interaction works the same with Appium as with Selenium. So we have the same basic 4 commands that give us most of what we need:

1. First, we can click elements. Actually, in the world of mobile devices, we should say 'tap', but because click and tap trigger the same behaviors in the usual cases, there's no special 'tap' method. When you call the regular old WebDriver protocol click command in Appium, under the hood a tap on the element is generated.
2. Secondly, we can call send_keys with a string parameter. This works on input fields as you would expect. With Appium, it can also work on a few other kinds of input controls. Mobile apps tend to have a greater variety of input controls like sliders, switches and so on. We can use send_keys to set the value of a slider, for example, not only send text strings to a text input field.
3. Third, we can reverse the send keys command with an element.clear().
4. And finally, we can get the text inside an element using the element.text property. The behavior here differs from Selenium slightly. With Selenium, if you call .text on an element which has other elements inside of it, you'll get the text of all the descendant elements, concatenated into one string. Appium, on the other hand, will only give you the text of the single element you're working with. In practice this difference doesn't tend to matter much, but I mention it so that you can be aware.

Before we move on to a coding exercise, let's take this opportunity to look at a few other types of element interactions. These methods all work with Selenium too! I consider them a bit more advanced since we don't need to use them as frequently, but they're all useful in one way or another.

1. Element objects have a get_attribute method which takes an attribute name as a string parameter. Remember when we looked at the app page source or the source in Appium Desktop's Inspector? Elements have all kinds of attributes like name, label, and so on. You can get the values of these attributes in your client scripts using this get_attribute method.
2. Element objects also have a property called rect, which returns the size and position of the element. This is often quite useful in situations where you need to perform some kind of low-level touch action on an element, and therefore need to know its exact midpoint relative to the screen coordinates, or something like that. When you retrieve this property, you get back a dictionary with width, height, x, and y keys.
3. A final method we'll consider is named is_displayed. When you call this method, you get back a boolean True or False indicating whether the element you've found is actually displayed on the screen. This can be useful since sometimes you might find elements which are not actually visible, and it can be a good idea to check that they are visible before interacting with them

Let's move to a code exercise to work with all these methods on iOS and Android. Here I am in my editor. I'm going to create a new file [<code>elements_ios.py</code>]().



