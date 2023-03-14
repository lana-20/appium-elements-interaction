# Interacting with Mobile Elements in Appium

*Mobile elements allow all the same types of interaction as web elements: click, send keys, and so on.*

Let's talk about what we can do with the elements we found.

| Mobile Element Interaction |  |
| ---- | ---- |
| <code>element.click()</code> | Click or tap the element. |
| <code>element.send_keys('text')</code> | Send 'text' as a series of keystrokes into an input field element. |
| <code>element.clear()</code> | Remove any text or value from an input field element. |
| <code>text = element.text</code> | Retrieve the text displayed within the element, and assign it to Python variable <code>text</code>. |

The main point here is that element interaction works the same with Appium as with Selenium. So we have the same basic 4 commands that give us most of what we need:

1. First, we can click elements. Actually, in the world of mobile devices, we should say 'tap', but because click and tap trigger the same behaviors in the usual cases, there's no special 'tap' method. When you call the regular old WebDriver protocol click command in Appium, under the hood a tap on the element is generated.
2. Secondly, we can call <code>send_keys</code> with a string parameter. This works on input fields as you would expect. With Appium, it can also work on a few other kinds of input controls. Mobile apps tend to have a greater variety of input controls like sliders, switches and so on. We can use <code>send_keys</code> to set the value of a slider, for example, not only send text strings to a text input field.
3. Third, we can reverse the send keys command with an <code>element.clear()</code>.
4. And finally, we can get the text inside an element using the <code>element.text</code> property. The behavior here differs from Selenium slightly. With Selenium, if you call <code>.text</code> on an element which has other elements inside of it, you'll get the text of all the descendant elements, concatenated into one string. Appium, on the other hand, will only give you the text of the single element you're working with. In practice this difference doesn't tend to matter much, but I mention it so that you can be aware.

| Other Element Interaction |  |
| ---- | ---- |
| <code>element.get_attribute('attr')</code> | Get the value of the element's <code>attr</code> attribute. |
| <code>element.rect</code> | Get the size and position of the element as a Python dictionary:  <code>{'x': 100, 'y': 50, 'height': 25, 'width': 40}</code> |
| <code>element.is_displayed()</code> | Check whether the element is actually displayed. |


Before we move on to a coding exercise, let's take this opportunity to look at a few other types of element interactions. These methods all work with Selenium too! I consider them a bit more advanced since we don't need to use them as frequently, but they're all useful in one way or another.

1. Element objects have a <code>get_attribute</code> method which takes an attribute name as a string parameter. When we look at the app page source or the source in Appium Inspector, we can see that elements have all kinds of attributes like name, label, and so on. You can get the values of these attributes in your client scripts using this get_attribute method.
2. Element objects also have a property called <code>rect</code>, which returns the size and position of the element. This is often quite useful in situations where you need to perform some kind of low-level touch action on an element, and therefore need to know its exact midpoint relative to the screen coordinates, or something like that. When you retrieve this property, you get back a dictionary with <code>width</code>, <code>height</code>, <code>x</code>, and <code>y</code> keys.
3. A final method we'll consider is named <code>is_displayed</code>. When you call this method, you get back a boolean <code>True</code> or <code>False</code> indicating whether the element you've found is actually displayed on the screen. This can be useful since sometimes you might find elements which are not actually visible, and it can be a good idea to check that they are visible before interacting with them


Let's move to a code exercise to work with all these methods on iOS and Android. Here I am in my editor. I'm going to create a new file [<code>elements_ios.py</code>](https://github.com/lana-20/appium-elements-interaction/blob/main/elements_ios.py):

    from os import path
    from appium import webdriver
    from appium.webdriver.common.mobileby import MobileBy
    from selenium.webdriver.support.wait import WebDriverWait
    from selenium.webdriver.support import expected_conditions as EC

    CUR_DIR = path.dirname(path.abspath(__file__))
    APP = path.join(CUR_DIR, 'TheApp.app.zip')
    APPIUM = 'http://localhost:4723'
    CAPS = {
        'platformName': 'iOS',
        'platformVersion': '16.2',
        'deviceName': 'iPhone 14 Pro',
        'automationName': 'XCUITest',
        'app': APP,
    }

    driver = webdriver.Remote(APPIUM, CAPS)
    try:
        wait = WebDriverWait(driver, 10)
        wait.until(EC.presence_of_element_located(
            (MobileBy.ACCESSIBILITY_ID, 'Echo Box'))).click()
        wait.until(EC.presence_of_element_located(
            (MobileBy.ACCESSIBILITY_ID, 'messageInput'))).send_keys('Hello')
        driver.find_element(MobileBy.ACCESSIBILITY_ID, 'messageSaveBtn').click()
        saved = driver.find_element(MobileBy.ACCESSIBILITY_ID, 'savedMessage').text
        assert saved == 'Hello'
        driver.back()

        wait.until(EC.presence_of_element_located(
            (MobileBy.ACCESSIBILITY_ID, 'Echo Box'))).click()
        saved = driver.find_element(MobileBy.ACCESSIBILITY_ID, 'savedMessage').text
        assert saved == 'Hello'
    finally:
        driver.quit()

... img echo box

I'd like to actually go to a screen called the 'Echo Box'. Let me show you what happens with this view. Here's my simulator. I can open The App, and I'm going to choose the Echo Box view. This view is very simple. It just has a text field that I can type into. I'll type something and hit Save. And now the view just echoes back to me what I wrote. I can even go back to the home screen, and then come back to this view, and it will remember what I wrote before. OK, back to our code. What I want to to specifically is test this functionality. We can think of it as consisting of several steps, which I'll write out as TODO comments to keep us on task.

    # TODO Wait for 'echo box' element and click to go to next screen
    # TODO Wait for input field element and type 'Hello' into it
    # TODO Click the save button
    # TODO Assert that the text I typed has been echoed back
    # TODO Go back to the main screen
    # TODO Go back to the echo box screen
    # TODO Assert that the text I typed is still saved and displayed

Now, as we go through, we can address each of these todo items without worrying we're going to leave something out.

First, we can wait for the echo box and click it all in one go:

    wait.until(EC.presence_of_element_located(
            (MobileBy.ACCESSIBILITY_ID, 'Echo Box'))).click()

Here I know that we can use this accessibility ID for this element, and we can just call <code>click()</code> directly on the result of the call to <code>wait.until</code>, since it returns the element. Great! One TODO down. Onto the next one. We need to find the input field element and type this string into it. We can also do this in one go, and again using an Accessibility ID I know that the input field has on it:

    wait.until(EC.presence_of_element_located(
            (MobileBy.ACCESSIBILITY_ID, 'messageInput'))).send_keys('Hello')

Notice that we're waiting for the message input field to show up. Why do we need to wait? Because we know that in between clicking the Echo Box item on the previous view, and the new view loading and displaying the message input field, there might be some time elapsed due to view transitions or animations, etc. So we're using a <code>wait</code> here just to be safe. And as you can see we're calling <code>send_keys</code> directly on the wait result again.

Nice, that's another TODO we can delete. Now, to click the save button. We know the save button is already displayed at this point since we already found the text field, so we can just use a regular <code>find_element</code> to find it and then click it:

    driver.find_element(MobileBy.ACCESSIBILITY_ID, 'messageSaveBtn').click()

Again I was able to use an Accessibility ID I had put on the element. OK, another TODO down. At this point, we need to assert that the echoed message has displayed correctly. To do this, we need to find the element where the text lives, and retrieve its text in order to assert that it is correct:

    saved = driver.find_element(MobileBy.ACCESSIBILITY_ID, 'savedMessage').text
        assert saved == 'Hello'

I'm doing this by creating a variable to hold the saved message text, and then getting the text property of a call to <code>find_element</code>, again using the Accessibility ID strategy. Then, I assert that this new variable is equal to the correct string. Alright, just three TODOs left! Going back to the main screen is easy:

    driver.back()

Yes, we can use <code>.back</code> even with Appium. It's not quite as reliable as on a mobile browser, since on iOS there is no universal operating system supported back feature. But if there's an element with the accessibility ID of "Back", which is a convention in apps, then Appium will happily go back for you using this command. (On Android, by the way, <code>.back</code> is more of an official thing since many devices actually have physical back buttons.)

From here on out, we've already implemented the logic for our final TODOs. Navigating to the Echo Box screen and asserting on the value of the saved message are actions that we've already coded up. So I'm just going to copy and paste them here, first the wait and click, and then the two lines that form the text assertion.

And we're done! I'll delete the last of the TODOs. I'm going to run this and see if it works. I'll head over to my terminal and run:

    python elements_ios.py

Or, on macOS:

    python3 elements_ios.py

The app is launching, and Appium is walking through the flow we described. Filling out the field, going back and forward, and... it's done. Let's check the output from the Python script. No output, that means everything passed. Excellent!

So, that's an example of how we do element interaction in Appium. I'm not going to walk through the same example for Android, but I think it would be a good idea for you to try it on your own. Apart from the app and capabilities, there's actually only one line in the iOS version of the script that needs to be changed for it to work on Android. Using Appium Inspector, you can fix that line so an Android version of this test will pass too.






