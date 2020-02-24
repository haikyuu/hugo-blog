---
title: "React Native - Webview communication"
date: 2020-02-24
draft: false
toc: false
featuredImg: "../../static/images/rn-webview-image.jpg"
tags: 
  - JavaScript
  - React
  - React Native
  - Webview
---

# Introduction

There are various cases in which you would want to display a Webview in a mobile app. You may want to display your privacy policy that's present in your website, or you may want to integrate with a service that requires the use of a Webview (HelloSign) ...

Whatever is your use case, you want the best User Experience. So let's dive in and see how we can communicate back and forth between native and a Webview.

First, let's take a look at the official guide for [communicating between js and native](https://github.com/react-native-community/react-native-webview/blob/master/docs/Guide.md#communicating-between-js-and-native). I sum it up below.

tl;dr, There are three ways:

1. React Native -> Web: The `injectedJavaScript` prop:
  1.1. `injectedJavaScript`: This is a script that runs **immediately after the web page loads** for the first time.
  1.2. `injectedJavaScriptBeforeContentLoaded`: This is a script that runs **before the web page loads** for the first time.
The downside of these methods is that they run **only once**
2. React Native -> Web: The `injectJavaScript` method enables you to run some JavaScript in the webpage whenever you want.
3. Web -> React Native: The `postMessage` method and `onMessage` prop
   By setting the `onMessage` prop on the webview, `window.ReactNative.postMessage` method will be injected onto the webview and can be used to send messages 📨 to the native side.

Not yet clear? let's see that in a table

|                                           | # times it can run | from rn to webview | from webview to rn |          when it runs          |
|-------------------------------------------|:------------------:|:------------------:|:------------------:|:------------------------------:|
| injectedJavaScript                        |          1        |          ✅         |          🚫         |        after page loads        |
| injectedJavaScriptBeforeContentLoaded     |         1        |          ✅         |          🚫         | before the page starts loading |
| injectJavaScript                          |          ∞         |          ✅         |          🚫         |        Whenever you want       |
| `postMessage` method and `onMessage` prop |          ∞         |          🚫         |          ✅         |        Whenever you want       |

# Display a loading spinner while the web page is loading

Of the first sight, this seems like a great use case for `postMessage` method ... The webview needs to tell react-native "Hey, i'd done loading", but we what if i am rendering an external webpage in my webview. How can i use the `postMessage`?

The answer is right there in our little table: `injectedJavascript`. As you see, it runs once after page loads. So we need to inject some JavaScript piece of code that tells react-native it should hide the spinner 🎉

Sample code:
```JavaScript
{this.state.isLoading ? (
    <ActivityIndicator size="large" style={{ alignSelf: 'center', marginTop: 150 }} />
) : null}
<WebView
    useWebKit
    style={{ width: '100%', display: !this.state.isLoading ? 'flex' : 'none' }}
    source={{ uri: 'https://google.com' }}
    injectedJavaScript={`
        window.ReactNativeWebView.postMessage('loaded')
    `}
    onMessage={event => {
        if (event.nativeEvent.data === 'loaded') {
            this.setState({
                isLoading: false,
            });
        }
    }}
/>
```
