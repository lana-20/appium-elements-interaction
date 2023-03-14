# Interacting with Mobile Elements in Appium

*Mobile elements allow all the same types of interaction as web elements: click, send keys, and so on.*

let's talk about what we can do with the elements we found.

The main point here is that element interaction works the same with Appium as with Selenium. So we have the same basic 4 commands that give us most of what we need:

1. First, we can click elements. Actually, in the world of mobile devices, we should say 'tap', but because click and tap trigger the same behaviors in the usual cases, there's no special 'tap' method. When you call the regular old WebDriver protocol click command in Appium, under the hood a tap on the element is generated.
2. Secondly, we can call <code>send_keys</code> with a string parameter. This works on input fields as you would expect. With Appium, it can also work on a few other kinds of input controls. Mobile apps tend to have a greater variety of input controls like sliders, switches and so on. We can use <code>send_keys</code> to set the value of a slider, for example, not only send text strings to a text input field.
3. Third, we can reverse the send keys command with an <code>element.clear()</code>.
4. And finally, we can get the text inside an element using the <code>element.text</code> property. The behavior here differs from Selenium slightly. With Selenium, if you call <code>.text</code> on an element which has other elements inside of it, you'll get the text of all the descendant elements, concatenated into one string. Appium, on the other hand, will only give you the text of the single element you're working with. In practice this difference doesn't tend to matter much, but I mention it so that you can be aware.

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


...

    # TODO Wait for 'echo box' element and click to go to next screen
    # TODO Wait for input field element and type 'Hello' into it
    # TODO Click the save button
    # TODO Assert that the text I typed has been echoed back
    # TODO Go back to the main screen
    # TODO Go back to the echo box screen
    # TODO Assert that the text I typed is still saved and displayed


...







