.. highlight:: java

Working with Teak on Android
============================

Identify User
-------------
This tells Teak how the user should be referenced in the Teak system.

All Teak events will be delayed until ``identifyUser`` is called.

.. important:: This should be the same way that you identify the user in your system, so that when you export data from Teak, it will be easy for you to associate with your own data.

``Teak.identifyUser(String userId)``

``Teak.identifyUser(String userId, String email)``

``Teak.identifyUser(String userId, String[] optOut)``

``Teak.identifyUser(String userId, String[] optOut, String email)``

Parameters
    :userId: User identifier, required.

    :email: Email address to associate with the user, optional.

    :optOut: An array of privacy-related data items that should not be collected, optional.

        :Teak.OPT_OUT_IDFA: Will not collect IDFA for the users. If this is not collected, you will not be able to add this user to Teak-created Ad Audiences.

        :Teak.OPT_OUT_FACEBOOK: Will not collect Facebook Access Token. If this is not collected, Teak will not be able to corilate users across multiple devices.

        :Teak.OPT_OUT_PUSH_KEY: Will not collect push key. If this is not collected, Teak will not be able to send push notifications to this user.

User Attributes
---------------
Teak allows you to add a limited number of attributes to users. A maximum of 16 string and 16 numeric attributes can be used.

``Teak.setNumericAttribute(String attributeName, double attributeValue)``

``Teak.setStringAttribute(String attributeName, String attributeValue)``

Parameters
    :attributeName: The name of the user attribute.

    :attributeValue: Value to assign to the attribute.
