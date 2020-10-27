..
  Be sure to make changes to the Unity Android docs as needed

.. toctree::
    :maxdepth: 1
    :titlesonly:
    :hidden:

    working-with-teak
    javadoc_link
    markup
    notification-icon
    third-party
    changelog


Integration
===========

Dependencies
------------

The following dependencies are required by Teak

* ``com.google.android.gms:play-services-ads-identifier:16+``
* ``com.google.android.gms:play-services-base:16+``
* ``com.google.android.gms:play-services-basement:16+``
* ``com.google.firebase:firebase-messaging:17+``
* ``com.firebase:firebase-jobdispatcher:0.8.5``

If you are using AndroidX, Teak requires

* ``androidx.core:core:1.0.+``

If you are *not* using AndroidX, Teak requires

* ``com.android.support:support-core-utils:26.1+``
* ``com.android.support:support-compat:26.1+``

The following dependencies are optional

* ``com.android.installreferrer:installreferrer:1+``

.. important:: ``firebase-messaging`` must be **20.1.0** or lower, and ``firebase-iid`` must be **20.0.2** or lower.

    Other versions will be supported in the future.

Tell Teak to Auto-Initialize
----------------------------
.. highlight:: xml

Teak can take advantage of Firebase's initialization of ContentProviders to auto-initialize. This is the easiest way to integrate Teak with your Android target.

All you need to do is add this to the main activity in your AndroidManifest.xml::

    <meta-data android:name="io.teak.sdk.initialize" android:value="true"/>

.. note:: This should work just fine for most games. You can go right to :ref:`skip-to-edit-teak-xml`

Otherwise Add Teak to your Main Activity
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. highlight:: java

If you don't want to use auto-initialization then you need to add the Teak initialization calls into your custom activity.

Import Teak into your main activity::

    import io.teak.sdk.Teak;

Call Teak.onCreate **before** the call to super.onCreate::

    protected void onCreate(Bundle savedInstanceState) {
        Teak.onCreate(this);
        super.onCreate(savedInstanceState);
        // ... etc
    }

Call setIntent()::

    protected void onNewIntent(Intent intent) {
        super.onNewIntent(intent);
        setIntent(intent); // << Add this line
    }


What This Does
^^^^^^^^^^^^^^
This lets Teak hook into the Android app lifecycle and configure itself, listen for Facebook logins, billing events, and begin sending information to the Teak Service.

Testing It
^^^^^^^^^^
Run your game on an Android device, and look at the Android debug log output.

You Should See
^^^^^^^^^^^^^^
.. highlight:: json

::

    {
      "event_type":"teak.state",
      "log_level":"INFO",
      "timestamp":"<timestamp>",
      "event_data": {
        "state":"Created",
        "old_state":"Allocated"
      },
      "event_id":1,
      "sdk_version": {
        "android":"<android-sdk-version>"
      },
      "run_id":"<some-guid>"
    }

And many other Teak log entries.

.. note:: If You Don't See Teak debug log messages, check to make sure your game is being built in debug mode.

If You See
^^^^^^^^^^
    java.lang.RuntimeException: Failed to find R.string.io_teak_api_key

It means that the ``res/values/teak.xml`` file was not found. See below...

.. _skip-to-edit-teak-xml:
.. _android-edit-teak-xml:

Edit res/values/teak.xml
------------------------
.. highlight:: xml

If your build environment needs to provide the file itself, this is what it should look like::

    <?xml version="1.0" encoding="utf-8"?>
    <resources>
        <string name="io_teak_app_id">YOUR_TEAK_APP_ID</string>
        <string name="io_teak_api_key">YOUR_TEAK_API_KEY</string>

        <!-- If you do not use the google-services.json, these parameters can be used instead. -->
        <string name="io_teak_gcm_sender_id">YOUR_GCM_SENDER_ID</string>
        <string name="io_teak_firebase_app_id">YOUR_FIREBASE_APPLICATION_ID</string>
        <string name="io_teak_firebase_api_key">YOUR_FIREBASE_API_KEY</string>
        <string name="io_teak_firebase_project_id">YOUR_FIREBASE_PROJECT_ID</string>
    </resources>

.. important:: If you have the ``google-services.json`` included with the Gradle Firebase plugin, you only need to specify ``io_teak_app_id`` and ``io_teak_api_key``.

.. note:: Replace ``YOUR_TEAK_APP_ID``, ``YOUR_TEAK_API_KEY``, ``YOUR_GCM_SENDER_ID``, ``YOUR_FIREBASE_APPLICATION_ID``, ``YOUR_FIREBASE_PROJECT_ID``, and ``YOUR_FIREBASE_API_KEY`` with your game's values.

Your Teak App Id and API Key can be found in the Settings for your app on the Teak dashboard.

Your Firebase Application Id, Project Id, API Key and GCM Sender Id can be found in your Firebase dashboard.

`How to find your GCM Sender Id <https://teak.readthedocs.io/en/latest/firebase-gcm.html>`_
`How to find your Firebase App Id Project Id and API Key <https://teak.readthedocs.io/en/latest/firebase-app-id.html>`_

What This Does
^^^^^^^^^^^^^^
This provides Teak with the credentials needed to send information to the Teak Service.

More about Firebase Credentials
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The Firebase App Id (in ``io_teak_firebase_app_id``) is structured as follows::

    [index]:[app id]:android:[?]

The value of ``io_teak_gcm_sender_id`` must match the second element of the value in ``io_teak_firebase_app_id``. For example, if your ``io_teak_firebase_app_id`` is ``1:12126273415:android:10329156b15bf0c`` then your ``io_teak_gcm_sender_id`` should be ``12126273415``.

If these values do not match, then an ``java.io.IOException`` will be thrown with the value ``FIS_AUTH_ERROR`` will be thrown.

The value of ``io_teak_firebase_api_key`` must be correct for the specified ``io_teak_firebase_app_id`` as well, otherwise an ``java.io.IOException`` will be thrown with the value ``FIS_AUTH_ERROR`` will be thrown.

Android does not provide any additional information at runtime for these issues.

Teak will log this exception with an error similar to this::

    E/Teak: {
      "client_app_version_name":"1.0",
      "run_id":"4c449d429c564fa2986d99fe329540eb",
      "event_id":43,
      "event_type":"exception",
      "device_id":"6a8b1cd8-245b-38e6-9319-31523ace3baf",
      "bundle_id":"io.teak.app.unity.dev",
      "sdk_version":{
        "android":"3.2.0"
      },
      "log_level":"ERROR",
      "client_app_version":1,
      "event_data":{
        "stacktrace":{
          "frames":[
            {
              "in_app":false,
              "filename":"Thread.java",
              "lineno":764,
              "function":"run",
              "module":"java.lang.Thread"
            },
            {
              "in_app":true,
              "lineno":6,
              "function":"run",
              "module":"com.google.android.gms.common.util.concurrent.zza"
            },
            {
              "in_app":false,
              "filename":"ThreadPoolExecutor.java",
              "lineno":641,
              "function":"run",
              "module":"java.util.concurrent.ThreadPoolExecutor$Worker"
            },
            {
              "in_app":false,
              "filename":"ThreadPoolExecutor.java",
              "lineno":1167,
              "function":"runWorker",
              "module":"java.util.concurrent.ThreadPoolExecutor"
            },
            {
              "in_app":true,
              "lineno":5,
              "function":"run",
              "module":"com.google.android.gms.tasks.zzd"
            },
            {
              "in_app":true,
              "filename":"com.google.firebase:firebase-iid@@20.1.5",
              "lineno":16,
              "function":"then",
              "module":"com.google.firebase.iid.zzu"
            }
          ]
        },
        "type":"IOException",
        "value":"FIS_AUTH_ERROR",
        "module":"java.io"
      },
      "app_id":"613659812345256",
      "timestamp":1586896331
    }

Set Notification Icons for your Game
------------------------------------
To specify the icon displayed in the system tray, and at the top of the notification, specify these resources.

You will need two versions of this file. One located in ``values`` and the other located in ``values-v21``::

    <?xml version="1.0" encoding="utf-8"?>
    <resources>
        <!-- The tint-color for your silouette icon, format is: 0xAARRGGBB -->
        <integer name="io_teak_notification_accent_color">0xfff15a29</integer>

        <!-- Icons should be white and transparent, and processed with Android Asset Studio -->
        <drawable name="io_teak_small_notification_icon">@drawable/YOUR_ICON_FILE_NAME</drawable>
    </resources>

The file in ``values`` should point to a full-color icon, for devices running less than Android 5, and the file in ``values-v21`` should point to a white and transparent PNG for Android 5 and above.

.. important:: To make sure that your white and transparent PNG shows up properly, use :doc:`Android Asset Studio's Notification icon generator <android/notification-icon>`.


.. _android-set-up-deep-linking:

Setting Up Deep Linking
-----------------------
.. highlight:: xml

Add the following to the ``<activity>`` section of your ``Assets/Plugins/Android/AndroidManifest.xml``::

    <intent-filter android:autoVerify="true">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="http" android:host="YOUR_SUBDOMAIN.jckpt.me" />
        <data android:scheme="https" android:host="YOUR_SUBDOMAIN.jckpt.me" />
    </intent-filter>
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="teakYOUR_TEAK_APP_ID" android:host="*" />
    </intent-filter>

.. note:: Replace ``YOUR_TEAK_APP_ID`` with your Teak App Id and ``YOUR_SUBDOMAIN`` with your Teak Subdomain.

Your Teak App Id and Teak Subdomain can be found in the Settings for your app on the Teak dashboard.

What This Does
^^^^^^^^^^^^^^
This tells Android to look for deep link URLs created by Teak.

.. _android-set-up-event-listeners:

Setting Up Event Listeners
--------------------------
.. highlight:: java

Teak sends several events using the ``LocalBroadcastManager``.

Events
    :Teak.LAUNCHED_FROM_NOTIFICATION_INTENT: The app was launched from a notification.

    :Teak.REWARD_CLAIM_ATTEMPT: A reward claim has happened.

    :Teak.FOREGROUND_NOTIFICATION_INTENT: A notification was recieved while the app was in the foreground.

    :Teak.ADDITIONAL_DATA_INTENT: A runtime JSON blob was provided from the Teak server.

    :Teak.LAUNCHED_FROM_LINK_INTENT: The app was launched from a URL created on the Teak dashboard.

Here is an example of registering a broadcast receiver::

    IntentFilter filter = new IntentFilter();
    filter.addAction(Teak.LAUNCHED_FROM_NOTIFICATION_INTENT);
    LocalBroadcastManager.getInstance(context).registerReceiver(yourBroadcastListener, filter);

As a more complicated example, this is the code from the Unity/Cocos2dx wrapper which turns these broadcasts into an internal event type::

    private BroadcastReceiver broadcastReceiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            final String action = intent.getAction();
            final Bundle bundle = intent.getExtras();
            if (bundle == null) return;

            if (Teak.LAUNCHED_FROM_NOTIFICATION_INTENT.equals(action)) {
                String eventData = "{}";
                try {
                    @SuppressWarnings("unchecked")
                    HashMap<String, Object> eventDataDict = (HashMap<String, Object>) bundle.getSerializable("eventData");
                    eventData = new JSONObject(eventDataDict).toString();
                } catch (Exception e) {
                    Teak.log.exception(e);
                } finally {
                    sdkWrapper.sdkSendMessage(ISDKWrapper.EventType.NotificationLaunch, eventData);
                }
            } else if (Teak.REWARD_CLAIM_ATTEMPT.equals(action)) {
                try {
                    @SuppressWarnings("unchecked")
                    HashMap<String, Object> reward = (HashMap<String, Object>) bundle.getSerializable("reward");

                    String eventData = new JSONObject(reward).toString();
                    sdkWrapper.sdkSendMessage(ISDKWrapper.EventType.RewardClaim, eventData);
                } catch (Exception e) {
                    Teak.log.exception(e);
                }
            } else if (Teak.FOREGROUND_NOTIFICATION_INTENT.equals(action)) {
                String eventData = "{}";
                try {
                    @SuppressWarnings("unchecked")
                    HashMap<String, Object> eventDataDict = (HashMap<String, Object>) bundle.getSerializable("eventData");
                    eventData = new JSONObject(eventDataDict).toString();
                } catch (Exception e) {
                    Teak.log.exception(e);
                } finally {
                    sdkWrapper.sdkSendMessage(ISDKWrapper.EventType.ForegroundNotification, eventData);
                }
            } else if (Teak.ADDITIONAL_DATA_INTENT.equals(action)) {
                String eventData = "{}";
                try {
                    eventData = bundle.getString("additional_data");
                } catch (Exception e) {
                    Teak.log.exception(e);
                } finally {
                    sdkWrapper.sdkSendMessage(ISDKWrapper.EventType.AdditionalData, eventData);
                }
            } else if (Teak.LAUNCHED_FROM_LINK_INTENT.equals(action)) {
                String eventData = "{}";
                try {
                    eventData = bundle.getString("linkInfo");
                } catch (Exception e) {
                    Teak.log.exception(e);
                } finally {
                    sdkWrapper.sdkSendMessage(ISDKWrapper.EventType.LaunchedFromLink, eventData);
                }
            }
        }
    };
