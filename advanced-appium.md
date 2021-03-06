# Locator Strategies

Appium implements the W3C Webdriver Protocol, and as such we implement some of the same locator strategies that are used to identify web elements. Since they're used to target elements in a native environment, we have to choose locator strategies for native elements that are "analagous" to their web counterparts. 

For 'xpath' and 'tag name', for example, we can't target an HTML DOM, like we do in Web, because native applications are native and don't have a DOM. For Appium, we have our own facsimile of the DOM, which traverses the application hierarchy and constructs an XML representation of the application and then we can use 'xpath' and 'tag name' to target elements in that hierarchy.

Problem with this is that every single time you wish to locate an element using the Xpath, Appium has to re-construct the XML which is a quite expensive, lengthy operation, because it requires us to visit every single element in the application and retrieve the name of the element and several different attributes. 

On top of that, Xpath has it's own problems because they're very brittle. Take an Xpath like this, for example:

`/hierarchy/android.widget.FrameLayout/android.view.ViewGroup/android.widget.FrameLayout[2]/android.widget.LinearLayout/android.widget.TextView[3]`

Xpaths like this may get the job done in the short term, but all it would take as just the slightest change to your application hierarchy and it's broken.

## Recommended Locator Strategies

Probably the best way to select elements is the accessibility ID selector

```java
MobileElement elementOne = (MobileElement) driver.findElementByAccessibilityId("SomeAccessibilityID");
```

The `accessibility id` maps to Android's "content description" and IOS's "accessibility identifier", which are both attributes that describe what the element is and are used by screenreaders. When you think of Appium as a testing framework that models user behaviour, the idea of using accessibilty id's kind of makes sense. You're locating elements the same way that a visually impaired person would find elements. And it's more performant than Xpath, because it doesn't require the construction of XML.

This may mean that you'll have to work with the developers have to add accessibility id's to your apps, but by doing so, you can achieve two goals at the same time. 1) you get to add accessibility information to your app (just make sure you're adding helpful accessibility info, and not fluff just for the sake of making the app "testable"; 2) you're making the app more testable by allowing for less brittle, more performant selectors.

## Other Locator Strategies

On top of the standard W3C selector strategies, Appium has a bunch of custom selector strategies that are platform and library specific. These selectors typically make use of element location strategies that are provided by the underlying automation libraries. Due to time constraints, I'll go them briefly and just give an idea of what they are and how they're beneficial

| Name | Platform|Automation|Example|About them|
|-------|-------|-------|-------|-------|
| -ios classchain | iOS | XCUITest | `XCUIElementTypeWindow/XCUIElementTypeButton[3]` | High performance alternative to Xpath. Xpath-like syntax |
| -ios predicate string | iOS | XCUITest | `collectionViews()[0].cells().withPredicate(\"ANY staticTexts.isVisible == TRUE\")` | Makes use of XCUITest's predicate string |
| -android uiautomator | Android | UiAutomator2 | `new UiSelector().className("android.widget.TextView").instance(0)` | Makes use of Android's UiAutomator selection strategy |
| -android datamatcher | Android | Espresso | `findElement("-android datamatcher", JSON.stringify({"name": "hasEntry", "args":["title", "TextClock"]})) | Uses Espresso's onData matcher which can locate elements off of the screen |



# Set Up App State Instantly

So a pretty common test case with Appium is that you need to log into your application. So you need to provide the user and password credentials. The standard way of handling such a use case is to write a test that looks something like this

```java
function logIn() {
  var login = driver.elementById("login_input");
  var password = driver.elementById("password_input");
  login.setValue("JohnDoe");
  password.setValue("SuperSecretPa$$word");
  var loginButton = driver.elementById("login_button");
  loginButton.click();
}
```

This is a very common use case, and if your app has an extensive login area you may have to run this script many many times. Let's suppose, we have an app test case that requires to run this script 100 times and that this script takes 20 seconds to run every single time. If we do some math on that, that's about 30 minutes of testing time. The benefit of this is that you get to test your login a lot and you know that your login screen will be _very_ well tested. However, we're probably overdoing it. We probably don't need to test the login 100 times, once should be fine. We should have one user interface test case that tests the login workflow and the other 100 tests, it would be nice if we could just cheat and skip past this and save those 20 seconds.

Luckily, we have a couple of features in Android and iOS that, with a little bit of tweaking of the applications, can accomplish.

So first I'll look at Android. Android has a thing called "Activities" which represent the contents of the screen at a given point for the activity. Android equivalent to what a page would be for web. When writing a Selenium web test, you can navigate to any webpage using the Go To Url method, and you can provide the query string and post parameters. With Appium, we have the ability to jump to any activity. And Activities have an object called "Intents" that are plain data objects that are passed into the activity that are the activity's parameters.

So for example, you might have an activity that is your "Login Activity" and that when you fill out the login activity form it will create an Intent that contains your username and your password and then passes that to your post-login activity.



# Instant Apps

Android has a feature called "Instant Apps". 

# IOS Touch ID



