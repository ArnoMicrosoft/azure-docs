---
title: Azure Mobile Engagement Android SDK Integration
description: Latest updates and procedures for Android SDK for Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: ''

ms.assetid: a7d719ec-67b3-4be3-9d7f-0b61a57fe978
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo

---
# How to Integrate ADM with Engagement
> [!IMPORTANT]
> You must follow the integration procedure described in the How to Integrate Engagement on Android document before following this guide.
> 
> This document is useful only if you already integrated the Reach module and plan to push Amazon devices. To integrate Reach campaigns in your application, please read first How to Integrate Engagement Reach on Android.
> 
> 

## Introduction
Integrating ADM allows your application to be pushed when targeting Amazon Android devices.

ADM payloads pushed to the SDK always contain the `azme` key in the data object. Thus if you use ADM for another purpose in your application, you can filter pushes based on that key.

> [!IMPORTANT]
> Only Amazon Kindle devices running Android 4.0.3 or above are supported by Amazon Device Messaging; however, you can integrate this code safely on other devices.
> 
> 

## Sign up to ADM
If not already done, you must enable ADM on your Amazon account.

The procedure is detailed at: [<https://developer.amazon.com/sdk/adm/credentials.html>].

Upon completing the procedure, you get:

* OAuth credentials (a Client ID and a Client Secret) for Engagement to be able to push your devices.
* An API Key that must be integrated into your application.

## SDK integration
### Managing device registrations
Each device must send a registration command to the ADM servers, otherwise they can't be reached.

If you already use the [ADM client library], and already have [integrated ADM] you can directly go to android-sdk-adm-receive.

If you have not integrated ADM yet, Engagement has a simpler way to enable it in your application:

Edit your `AndroidManifest.xml` file:

* Add the Amazon namespace, the file should begin like this:
  
      <?xml version="1.0" encoding="utf-8"?>
      <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:amazon="http://schemas.amazon.com/apk/res/android"
* Inside the `<application/>` tag, add this section:
  
      <amazon:enable-feature
         android:name="com.amazon.device.messaging"
         android:required="false"/>
  
      <meta-data android:name="engagement:adm:register" android:value="true" />
* After adding the amazon tag, you may have a build error if your Project Build Target is below Android 2.1. You have to use an **Android 2.1+** build target (don't worry, you can still have a `minSdkVersion` set to 4).
* Integrate the ADM API Key as an asset by following [this procedure].

Then follow the instructions of the next sections.

### Communicate registration id to the Engagement Push service and receive notifications
In order to communicate the registration id of the device to the Engagement Push service and receive its notifications, add the following to your `AndroidManifest.xml` file, inside the `<application/>` tag (even if you use ADM without Engagement):

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Ensure you have the following permissions in your `AndroidManifest.xml` (before the `</application>` tag).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

## Grant Engagement OAuth credentials
Submit your OAuth Credentials (Client ID and Client Secret) in Engagement Portal.

[<https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[ADM client library]:https://developer.amazon.com/sdk/adm/setup.html
[integrated ADM]:https://developer.amazon.com/sdk/adm/integrating-app.html
[this procedure]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
