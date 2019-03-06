---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-28"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:tip: .tip}
{:note: .note}
{:pre: .pre}
{:codeblock: .codeblock}
{:screen: .screen}
{:java: .ph data-hd-programlang='java'}
{:ruby: .ph data-hd-programlang='ruby'}
{:c#: .ph data-hd-programlang='c#'}
{:objectc: .ph data-hd-programlang='Objective C'}
{:python: .ph data-hd-programlang='python'}
{:javascript: .ph data-hd-programlang='javascript'}
{:php: .ph data-hd-programlang='PHP'}
{:swift: .ph data-hd-programlang='swift'}
{:reactnative: .ph data-hd-programlang='React Native'}
{:csharp: .ph data-hd-programlang='csharp'}
{:ios: .ph data-hd-programlang='iOS'}
{:android: .ph data-hd-programlang='Android'}
{:cordova: .ph data-hd-programlang='Cordova'}
{:xml: .ph data-hd-programlang='xml'}


# Send Push Notifications
{: #send_push_notifications}

Push notifications can be sent either from the {{ site.data.keyword.mfp_oc_short_notm }} or via REST APIs.
{: shortdesc}

* With the {{ site.data.keyword.mfp_oc_short_notm }}, two types of notifications can be sent: tag and broadcast.
* With the REST APIs, all forms of notifications can be sent: tag, broadcast and authenticated.

## Sending Push Notifications from {{ site.data.keyword.mfp_oc_short_notm }}
{: #sending-push-notification-from-mobilefirst-operations-console }

Notifications can be sent to a single Device ID, a single or several User IDs, only iOS devices or only Android devices, or to devices subscribed to tags.

### Tag notifications
{: #tag-notifications }

Tag notifications are notification messages that are targeted to all the devices that are subscribed to a particular tag. Tags represent topics of interest to the user and provide the ability to receive notifications according to the chosen interest.

In the {{ site.data.keyword.mfp_oc_short_notm }} → **[your application] → Push → Send Notifications tab**, select **Devices By Tags** from the **Send To** tab and provide the **Notification Text**. Then, click **Send**.

<img class="gifplayer" alt="Sending by tag" src="images/sending-by-tag.png"/>

### Broadcast notifications
{: #broadcast-notifications }

Broadcast notifications are a form of tag push notifications that are targeted to all subscribed devices. Broadcast notifications are enabled by default for any push-enabled {{ site.data.keyword.mobilefirst_notm }} application by a subscription to a reserved `Push.all` tag (auto-created for every device). The `Push.all` tag can be programmatically unsubscribed.

In the {{ site.data.keyword.mfp_oc_short_notm }} → **[your application] → Push → Send Notifications tab**, select **All** from the **Send To** tab and provide the **Notification Text**. Then, click **Send**.

<img class="gifplayer" alt="Sending to all" src="images/sending-to-all.png"/>

## Sending Push Notifications using REST APIs
{: #sending-push-notifications-using-rest-apis }

When using the REST APIs to send notifications, all forms of notifications can be sent: tag &amp; broadcast notifications, and authenticated notifications.

To send a notification, a request is made using POST to the REST endpoint: `imfpush/v1/apps/<application-identifier>/messages`.  
Example URL:

```
https://myserver.com:443/imfpush/v1/apps/com.sample.PinCodeSwift/messages
```

> To review all Push Notifications REST APIs, see the [REST API Runtime Services topic](https://www.ibm.com/support/knowledgecenter/SSHS8R_8.0.0/com.ibm.worklight.apiref.doc/rest_runtime/c_restapi_runtime.html) in the user documentation.

### Notification payload
{: #notification-payload }

The request can contain the following payload properties:

Payload Properties| Definition
--- | ---
message | The alert message to be sent
settings | The settings are the different attributes of the notification.
target | Set of targets can be consumer Ids, devices, platforms, or tags. Only one of the targets can be set.
deviceIds | An array of the devices represented by the device identifiers. Devices with these ids receive the notification. This is a unicast notification.
notificationType | Integer value to indicate the channel (Push/SMS) used to send message. Allowed values are 1 (only Push), 2 (only SMS) and 3 (Push and SMS)
platforms | An array of device platforms. Devices running on these platforms receive the notification. Supported values are A (Apple/iOS), G (Google/Android) and M (Microsoft/Windows).
tagNames | An array of tags specified as tagNames. Devices that are subscribed to these tags receive the notification. Use this type of target for tag based notifications.
userIds | An array of users represented by their userIds to send the notification. This is a unicast notification.
phoneNumber | The phone number used for registering the device and receiving notifications. This is a unicast notification.

**Push Notifications Payload JSON Example**

```json
{
    "message" : {
    "alert" : "Test message",
  },
  "settings" : {
    "apns" : {
      "badge" : 1,
      "iosActionKey" : "Ok",
      "payload" : "",
      "sound" : "song.mp3",
      "type" : "SILENT",
    },
    "gcm" : {
      "delayWhileIdle" : ,
      "payload" : "",
      "sound" : "song.mp3",
      "timeToLive" : ,
    },
  },
  "target" : {
    // The list below is for demonstration purposes only - per the documentation only 1 target is allowed to be used at a time.
    "deviceIds" : [ "MyDeviceId1", ... ],
    "platforms" : [ "A,G", ... ],
    "tagNames" : [ "Gold", ... ],
    "userIds" : [ "MyUserId", ... ],
  },
}
```

**SMS Notification Payload JSON Example**

```json
{
  "message": {
    "alert": "Hello World from an SMS message"
  },
  "notificationType":3,
   "target" : {
     "deviceIds" : ["38cc1c62-03bb-36d8-be8e-af165e671cf4"]
   }
}
```

## Sending the notification
{: #sending-the-notification }

The notification can be sent using different tools. For testing purposes, Postman is used as described below:

1. [Configure a Confidential Client](https://mobilefirstplatform.ibmcloud.com/tutorials/en/foundation/8.0/authentication-and-security/confidential-clients/).
  Sending a Push Notification via the REST API uses the space-separated scope elements `messages.write` and `push.application.<applicationId>.`
  <img class="gifplayer" alt="Configure a confidential client" src="images/push-confidential-client.png"/>
2. [Create an access token](https://mobilefirstplatform.ibmcloud.com/tutorials/en/foundation/8.0/authentication-and-security/confidential-clients#obtaining-an-access-token).  
3. Make a **POST** request to **http://localhost:9080/imfpush/v1/apps/com.sample.PushNotificationsAndroid/messages**
  - If using a remote {{ site.data.keyword.mobilefirst_notm }}, replace the `hostname` and `port` values with your own.
  - Update the application identifier value with your own.
4. Set a Header:
    - **Authorization**: `Bearer eyJhbGciOiJSUzI1NiIsImp ...`
    - Replace the value after "Bearer" with the value of your access token from step (1) above.
    ![authorization header](images/postman_authorization_header.png)
5. Set a Body:
  - Update its properties as described in [Notification payload](#notification-payload) above.
  - For example, by adding the **target** property with the **userIds** attribute, you can send a notification to specific registered users.
    ```json
    {
         "message" : {
             "alert" : "Hello World!"
         }
    }
    ```

    ![authorization header](images/postman_json.png)

    After clicking on the **Send** button, the device should have now received a notification:

    ![Image of the sample application](images/notifications-app.png)

## Customizing Notifications
{: #customizing-notifications }

Before sending the notification message, you can also customize the following notification attributes.  

In the {{ site.data.keyword.mfp_oc_short_notm }} → **[your application] → Push → Tags → Send Notifications tab**, expend the **iOS/Android Custom Settings** section to change notification attributes.

### Android
{: #android }

* Notification sound, how long a notification can be stored in the FCM storage, custom payload and more.
* If you want to change the notification title, then add `push_notification_tile` in the Android project's **strings.xml** file.

### iOS
{: #ios }

* Notification sound, custom payload, action key title, notification type and badge number.

  ![customizing push notifications](images/customizing-push-notifications.png)

## HTTP/2 Support for APNs Push Notifications
{: #http2-support-for-apns-push-notifications}

Apple Push Notification service (APNs) supports a new API based on HTTP/2 network protocol. Support for HTTP/2 provides many  benefits, including those listed below:

* Message length increased from 2 KB to 4 KB, which enables to add extra content to notifications.
* Eliminates the need for multiple connections between client and server, this improves the throughput.
* Universal Push Notification Client SSL Certificate support.

>Push Notifications in {{ site.data.keyword.mobilefirst_notm }} now supports the HTTP/2 based APNs Push Notifications along with the legacy TCP Socket based notifications.

### Enabling HTTP/2
{: #enabling-http2}

HTTP/2 based notifications can be enabled using a JNDI Property.
```xml
<jndiEntry jndiName="imfpush/mfp.push.apns.http2.enabled" value= "true"/>
```

>**Note:** If the above JNDI property is added, legacy TCP Socket based notifications will not be used and only the HTTP/2 based notifications will be enabled.
{: note}

### Proxy Support for HTTP/2
{: #proxy-support-for-http2}

HTTP/2 based notifications can be sent via a HTTP Proxy. To enable routing of the notifications via a proxy, see [here](#proxy-support).

## Proxy Support
{: #proxy-support }
You can make use proxy settings to set the optional proxy through which notifications are sent to Android and iOS devices. You can set the proxy by using the **push.apns.proxy.** and **push.gcm.proxy.** configuration properties. For more information, see [List of JNDI properties for {{ site.data.keyword.mfserver_short_notm }} push service](https://mobilefirstplatform.ibmcloud.com/tutorials/en/foundation/8.0/installation-configuration/production/server-configuration/#list-of-jndi-properties-for-mobilefirst-server-push-service).

## Tutorials to follow next
{: #tutorials-to-follow-next }
With the server-side now set-up, setup the client-side and handle received notifications.

* [Handling push notifications in Client applications](/docs/services/mobilefoundation?topic=mobilefoundation-handling_push_notifications_in_client_applications#handling_push_notifications_in_client_applications)
    * [Silent Notifications](/docs/services/mobilefoundation?topic=mobilefoundation-silent_notifications#silent_notifications)
    * [Interactive Notifications](/docs/services/mobilefoundation?topic=mobilefoundation-interactive_notifications#interactive_notifications)
    * [Handling push notifications in Cordova applications](/docs/services/mobilefoundation?topic=mobilefoundation-handling_push_notifications_in_cordova#handling_push_notifications_in_cordova)
    * [Handling push notifications in iOS applications](/docs/services/mobilefoundation?topic=mobilefoundation-handling_push_notifications_in_ios#handling_push_notifications_in_ios)
    * [Handling push notifications in Android applications](/docs/services/mobilefoundation?topic=mobilefoundation-handling_push_notifications_in_android#handling_push_notifications_in_android)
    * [Handling push notifications in Windows applications](/docs/services/mobilefoundation?topic=mobilefoundation-handling_push_notifications_in_windows#handling_push_notifications_in_windows)