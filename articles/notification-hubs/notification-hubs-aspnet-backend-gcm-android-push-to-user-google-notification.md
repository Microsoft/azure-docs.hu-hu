---
title: Értesítések küldése adott Android-alkalmazásokba az Azure Notification Hubs
description: Megtudhatja, hogyan küldhet leküldéses értesítéseket adott Android-felhasználóknak az Azure Notification Hubs használatával.
documentationcenter: android
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: c137cbf0c2290bdbe228f7902e616e7f79cb459e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016981"
---
# <a name="tutorial-send-push-notification-to-specific-android-users-using-azure-notification-hubs-and-google-cloud-messaging-deprecated"></a>Oktatóanyag: leküldéses értesítés küldése adott Android-felhasználóknak az Azure Notification Hubs és Google Cloud Messaging (elavult) használatával

> [!WARNING]
> 2018. április 10-ig a Google elavult Google Cloud Messaging (GCM). A GCM-kiszolgáló és az ügyféloldali API-k elavultak, és a 2019. május 29-én lesznek eltávolítva. További információ: [GCM és FCM – gyakori kérdések](https://developers.google.com/cloud-messaging/faq).

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Az oktatóanyag bemutatja, hogy hogyan küldhetők leküldéses értesítések adott alkalmazásfelhasználónak, adott eszközre az Azure Notification Hubs használatával. Az ASP.NET WebAPI háttérrendszer lehetővé teszi az ügyfelek hitelesítését és az értesítések létrehozását, amint az a [Regisztráció az alkalmazás háttérrendszeréből](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) című útmutató cikkben olvasható. Ez az oktatóanyag arra az értesítési központra épül, amelyet létrehozott az [Oktatóanyag: Leküldéses értesítések küldése Android-eszközökre az Azure Notification Hubs és a Google Cloud Messaging használatával](notification-hubs-android-push-notification-google-gcm-get-started.md) című részben.

Ebben az oktatóanyagban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * A felhasználókat hitelesítő webes háttér-API-projekt létrehozása.  
> * Az Android-alkalmazás frissítése.
> * Az alkalmazás tesztelése

## <a name="prerequisites"></a>Előfeltételek

Ezen oktatóanyag elvégzése előtt végezze el az [Oktatóanyag: Leküldéses értesítések küldése Androidra az Azure Notification Hubs és a Google Cloud Messaging használatával](notification-hubs-android-push-notification-google-gcm-get-started.md) oktatóanyagot.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="create-the-android-project"></a>Az Android-projekt létrehozása

A következő lépés az [Oktatóanyag: Leküldéses értesítések küldése Android-eszközökre az Azure Notification Hubs és a Google Cloud Messaging használatával](notification-hubs-android-push-notification-google-gcm-get-started.md) elvégzése során létrehozott Android-alkalmazás frissítése.

1. Nyissa meg a `res/layout/activity_main.xml` fájlt, cserélje le a következő tartalmi definíciókat:

    Új EditText vezérlőket ad hozzá a felhasználóként való bejelentkezéshez. Egy felhasználónév-címke számára kijelölt mezőt is hozzáad, amely szerepelni fog az elküldött értesítésekben:

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity">

    <EditText
        android:id="@+id/usernameText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="@string/usernameHint"
        android:layout_above="@+id/passwordText"
        android:layout_alignParentEnd="true" />
    <EditText
        android:id="@+id/passwordText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="@string/passwordHint"
        android:inputType="textPassword"
        android:layout_above="@+id/buttonLogin"
        android:layout_alignParentEnd="true" />
    <Button
        android:id="@+id/buttonLogin"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/loginButton"
        android:onClick="login"
        android:layout_above="@+id/toggleButtonGCM"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="24dp" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="WNS on"
        android:textOff="WNS off"
        android:id="@+id/toggleButtonWNS"
        android:layout_toLeftOf="@id/toggleButtonGCM"
        android:layout_centerVertical="true" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="GCM on"
        android:textOff="GCM off"
        android:id="@+id/toggleButtonGCM"
        android:checked="true"
        android:layout_centerHorizontal="true"
        android:layout_centerVertical="true" />
    <ToggleButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textOn="APNS on"
        android:textOff="APNS off"
        android:id="@+id/toggleButtonAPNS"
        android:layout_toRightOf="@id/toggleButtonGCM"
        android:layout_centerVertical="true" />
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessageTag"
        android:layout_below="@id/toggleButtonGCM"
        android:layout_centerHorizontal="true"
        android:hint="@string/notification_message_tag_hint" />
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_below="@+id/editTextNotificationMessageTag"
        android:layout_centerHorizontal="true"
        android:hint="@string/notification_message_hint" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:onClick="sendNotificationButtonOnClick"
        android:layout_below="@+id/editTextNotificationMessage"
        android:layout_centerHorizontal="true" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        android:id="@+id/text_hello"
    />  
    </RelativeLayout>
    ```
2. Nyissa meg a `res/values/strings.xml` fájlt, és cserélje le a `send_button` definíciót a következő sorokra, amelyek újradefiniálják a karakterláncot a `send_button` és a más vezérlőkhöz tartozó karakterláncok hozzáadásához:

    ```xml
    <string name="usernameHint">Username</string>
    <string name="passwordHint">Password</string>
    <string name="loginButton">1. Sign in</string>
    <string name="send_button">2. Send Notification</string>
    <string name="notification_message_hint">Notification message</string>
    <string name="notification_message_tag_hint">Recipient username</string>
    ```

    A `main_activity.xml` grafikus elrendezésnek ekkor az alábbi képhez hasonlóan kell kinéznie:

    ![Képernyőkép az alkalmazásról a Felhasználónév, a jelszó, a címzett és az üzenet mezőivel, valamint a bejelentkezéshez és az értesítések küldéséhez szükséges gombokkal.][A1]
3. Hozzon létre egy nevű új osztályt `RegisterClient` ugyanabban a csomagban, mint az `MainActivity` osztályt. Használja az alábbi kódot az új osztályfájlhoz.

    ```java
    import java.io.IOException;
    import java.io.UnsupportedEncodingException;
    import java.util.Set;

    import org.apache.http.HttpResponse;
    import org.apache.http.HttpStatus;
    import org.apache.http.client.ClientProtocolException;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.client.methods.HttpPut;
    import org.apache.http.client.methods.HttpUriRequest;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONException;
    import org.json.JSONObject;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.util.Log;

    public class RegisterClient {
        private static final String PREFS_NAME = "ANHSettings";
        private static final String REGID_SETTING_NAME = "ANHRegistrationId";
        private String Backend_Endpoint;
        SharedPreferences settings;
        protected HttpClient httpClient;
        private String authorizationHeader;

        public RegisterClient(Context context, String backendEndpoint) {
            super();
            this.settings = context.getSharedPreferences(PREFS_NAME, 0);
            httpClient =  new DefaultHttpClient();
            Backend_Endpoint = backendEndpoint + "/api/register";
        }

        public String getAuthorizationHeader() {
            return authorizationHeader;
        }

        public void setAuthorizationHeader(String authorizationHeader) {
            this.authorizationHeader = authorizationHeader;
        }

        public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
            String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);

            JSONObject deviceInfo = new JSONObject();
            deviceInfo.put("Platform", "gcm");
            deviceInfo.put("Handle", handle);
            deviceInfo.put("Tags", new JSONArray(tags));

            int statusCode = upsertRegistration(registrationId, deviceInfo);

            if (statusCode == HttpStatus.SC_OK) {
                return;
            } else if (statusCode == HttpStatus.SC_GONE){
                settings.edit().remove(REGID_SETTING_NAME).commit();
                registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                statusCode = upsertRegistration(registrationId, deviceInfo);
                if (statusCode != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            } else {
                Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                throw new RuntimeException("Error upserting registration");
            }
        }

        private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                throws UnsupportedEncodingException, IOException,
                ClientProtocolException {
            HttpPut request = new HttpPut(Backend_Endpoint+"/"+registrationId);
            request.setEntity(new StringEntity(deviceInfo.toString()));
            request.addHeader("Authorization", "Basic "+authorizationHeader);
            request.addHeader("Content-Type", "application/json");
            HttpResponse response = httpClient.execute(request);
            int statusCode = response.getStatusLine().getStatusCode();
            return statusCode;
        }

        private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
            if (settings.contains(REGID_SETTING_NAME))
                return settings.getString(REGID_SETTING_NAME, null);

            HttpUriRequest request = new HttpPost(Backend_Endpoint+"?handle="+handle);
            request.addHeader("Authorization", "Basic "+authorizationHeader);
            HttpResponse response = httpClient.execute(request);
            if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                throw new RuntimeException("Error creating Notification Hubs registrationId");
            }
            String registrationId = EntityUtils.toString(response.getEntity());
            registrationId = registrationId.substring(1, registrationId.length()-1);

            settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();

            return registrationId;
        }
    }
    ```

    Ez az összetevő megvalósítja azon REST-hívásokat, amelyek az alkalmazás háttérrendszeréhez való kapcsolódáshoz szükségesek a leküldéses értesítésekre való regisztrálás érdekében. Emellett helyben tárolja az értesítési központ által a [Regisztráció az alkalmazás háttérrendszeréből](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) című szakaszban leírtak szerint létrehozott *registrationIds* fájlt. A **Bejelentkezés** gombra kattintva a helyi tárolóban tárolt engedélyezési jogkivonatot használja.
4. Az osztályban távolítsa el vagy tegye megjegyzésbe a `NotificationHub` privát mezőjét, és adjon hozzá egy mezőt a `RegisterClient` osztály számára, valamint az ASP.NET-háttérrendszer végpontjához tartozó sztringet. Ne felejtse el az `<Enter Your Backend Endpoint>` karakterláncot a saját háttérrendszere korábban beszerzett végpontjára cserélni. Például: `http://mybackend.azurewebsites.net`.

    ```java
    //private NotificationHub hub;
    private RegisterClient registerClient;
    private static final String BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```

5. A `MainActivity` osztályban, az `onCreate` metódusban távolítsa el vagy tegye megjegyzésbe a `hub` mező inicializálását és a `registerWithNotificationHubs` metódus hívását. Ezután adja hozzá a `RegisterClient` osztály egy példányát inicializáló kódot. A metódusnak a következő sorokat kell tartalmaznia:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mainActivity = this;
        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);

        //hub = new NotificationHub(HubName, HubListenConnectionString, this);
        //registerWithNotificationHubs();

        registerClient = new RegisterClient(this, BACKEND_ENDPOINT);

        setContentView(R.layout.activity_main);
    }
    ```
6. A `MainActivity` osztályban törölje vagy tegye megjegyzésbe a teljes `registerWithNotificationHubs` metódust. Ehhez az oktatóanyaghoz nem lesz rá szükség.
7. Adja hozzá a következő `import` utasításokat a `MainActivity.java` fájlhoz.

    ```java
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;

    import android.widget.Button;
    import android.widget.ToggleButton;
    import java.io.UnsupportedEncodingException;
    import android.content.Context;
    import java.util.HashSet;
    import android.widget.Toast;
    import org.apache.http.client.ClientProtocolException;
    import java.io.IOException;
    import org.apache.http.HttpStatus;

    import android.os.AsyncTask;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.DefaultHttpClient;

    import android.app.AlertDialog;
    import android.content.DialogInterface;
    ```
8. Cserélje le az onStart metódus kódját az alábbira:

    ```java
    super.onStart();
    Button sendPush = (Button) findViewById(R.id.sendbutton);
    sendPush.setEnabled(false);
    ```
9. Ezután adja hozzá a következő metódusokat a **Bejelentkezés** gomb kezeléséhez kattintson az esemény gombra, és küldjön le leküldéses értesítéseket.

    ```java
    public void login(View view) throws UnsupportedEncodingException {
        this.registerClient.setAuthorizationHeader(getAuthorizationHeader());

        final Context context = this;
        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    String regid = gcm.register(NotificationSettings.SenderId);
                    registerClient.register(regid, new HashSet<String>());
                } catch (Exception e) {
                    DialogNotify("MainActivity - Failed to register", e.getMessage());
                    return e;
                }
                return null;
            }

            protected void onPostExecute(Object result) {
                Button sendPush = (Button) findViewById(R.id.sendbutton);
                sendPush.setEnabled(true);
                Toast.makeText(context, "Logged in and registered.",
                        Toast.LENGTH_LONG).show();
            }
        }.execute(null, null, null);
    }

    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
        return basicAuthHeader;
    }

    /**
        * This method calls the ASP.NET WebAPI backend to send the notification message
        * to the platform notification service based on the pns parameter.
        *
        * @param pns     The platform notification service to send the notification message to. Must
        *                be one of the following ("wns", "gcm", "apns").
        * @param userTag The tag for the user who will receive the notification message. This string
        *                must not contain spaces or special characters.
        * @param message The notification message string. This string must include the double quotes
        *                to be used as JSON content.
        */
    public void sendPush(final String pns, final String userTag, final String message)
            throws ClientProtocolException, IOException {
        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {

                    String uri = BACKEND_ENDPOINT + "/api/notifications";
                    uri += "?pns=" + pns;
                    uri += "&to_tag=" + userTag;

                    HttpPost request = new HttpPost(uri);
                    request.addHeader("Authorization", "Basic "+ getAuthorizationHeader());
                    request.setEntity(new StringEntity(message));
                    request.addHeader("Content-Type", "application/json");

                    HttpResponse response = new DefaultHttpClient().execute(request);

                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        DialogNotify("MainActivity - Error sending " + pns + " notification",
                            response.getStatusLine().toString());
                        throw new RuntimeException("Error sending notification");
                    }
                } catch (Exception e) {
                    DialogNotify("MainActivity - Failed to send " + pns + " notification ", e.getMessage());
                    return e;
                }

                return null;
            }
        }.execute(null, null, null);
    }
    ```

    A `login` **bejelentkezési** gomb kezelője egy alapszintű hitelesítési tokent hoz létre a bemeneti Felhasználónév és jelszó használatával (ez a hitelesítési séma által használt bármely tokent jelöli), majd `RegisterClient` a használatával meghívja a háttérrendszer regisztrációját.

    A `sendPush` metódus a háttérrendszer meghívásával aktiválja egy biztonságos értesítés küldését a felhasználónak a felhasználói címke alapján. A `sendPush` által megcélzott platformértesítési szolgáltatás az átadott `pns` sztringtől függ.

10. Adja hozzá a következő `DialogNotify` metódust a `MainActivity` osztályhoz.

    ```java
    protected void DialogNotify(String title, String message)
    {
        AlertDialog alertDialog = new AlertDialog.Builder(MainActivity.this).create();
        alertDialog.setTitle(title);
        alertDialog.setMessage(message);
        alertDialog.setButton(AlertDialog.BUTTON_NEUTRAL, "OK",
                new DialogInterface.OnClickListener() {
                    public void onClick(DialogInterface dialog, int which) {
                        dialog.dismiss();
                    }
                });
        alertDialog.show();
    }
    ```
11. A `MainActivity` osztályban frissítse a `sendNotificationButtonOnClick` metódust úgy, hogy az a felhasználó által kiválasztott platformértesítési szolgáltatásokkal hívja meg a `sendPush` metódust, az alábbiak szerint.

    ```java
    /**
    * Send Notification button click handler. This method sends the push notification
    * message to each platform selected.
    *
    * @param v The view
    */
    public void sendNotificationButtonOnClick(View v)
            throws ClientProtocolException, IOException {

        String nhMessageTag = ((EditText) findViewById(R.id.editTextNotificationMessageTag))
                .getText().toString();
        String nhMessage = ((EditText) findViewById(R.id.editTextNotificationMessage))
                .getText().toString();

        // JSON String
        nhMessage = "\"" + nhMessage + "\"";

        if (((ToggleButton)findViewById(R.id.toggleButtonWNS)).isChecked())
        {
            sendPush("wns", nhMessageTag, nhMessage);
        }
        if (((ToggleButton)findViewById(R.id.toggleButtonGCM)).isChecked())
        {
            sendPush("gcm", nhMessageTag, nhMessage);
        }
        if (((ToggleButton)findViewById(R.id.toggleButtonAPNS)).isChecked())
        {
            sendPush("apns", nhMessageTag, nhMessage);
        }
    }
    ```
12. A `build.gradle` fájlban adja hozzá a következő sort a szakaszhoz a `android` szakasz után `buildTypes` .

    ```java
    useLibrary 'org.apache.http.legacy'
    ```
13. A projekt felépítése.

## <a name="test-the-app"></a>Az alkalmazás tesztelése

1. Futtassa az alkalmazást egy eszközön vagy az Android Studiót használó emulátoron.
2. Az Android-alkalmazásban adjon meg egy felhasználónevet és jelszót. Mindkettőnek ugyanazon sztringértéknek kell lennie, és nem tartalmazhatnak szóközt vagy különleges karaktert.
3. Az Android-alkalmazásban kattintson a **Bejelentkezés** elemre. Várjon, amíg megjelenik egy bejelentési üzenet a **Logged in and registered** (Bejelentkezve és regisztrálva) szöveggel. Ezzel elérhetővé válik a **Send Notification** (Értesítés küldése) gomb.

    ![Képernyőkép az alkalmazásról. Egy olyan Toast-üzenet, amely megerősíti, hogy a felhasználó be van jelentkezve és regisztrálva van, és megjelenik az értesítés küldése gomb.][A2]
4. Kattintson a váltógombokra az összes olyan platform engedélyezéséhez, amelyen futtatta az alkalmazást és regisztrált felhasználókat.
5. Adja meg az értesítési üzenet címzettjének felhasználónevét. Ennek feltétele, hogy a felhasználó fel legyen iratkozva az értesítésekre a céleszközökön.
6. Adjon meg egy üzenetet, amelyet a felhasználó leküldéses értesítés formájában fog megkapni.
7. Kattintson a **Send Notification** (Értesítés küldése) gombra.  A leküldéses értesítés minden olyan eszközre megérkezik, amely rendelkezik a megfelelő felhasználónév-címkével ellátott regisztrációval.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban elsajátította, hogy hogyan küldhet leküldéses értesítéseket olyan adott felhasználóknak, akik a regisztrációjukhoz társított címkével rendelkeznek. Ha szeretné megtudni, hogy hogyan küldhet helyalapú értesítéseket, lépjen tovább a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Helyalapú leküldéses értesítések küldése](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users.png
[A2]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users-enter-password.png
