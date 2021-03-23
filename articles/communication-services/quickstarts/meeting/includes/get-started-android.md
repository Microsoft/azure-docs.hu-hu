---
title: Gyors útmutató – az Azure kommunikációs szolgáltatásokkal egy Android-alkalmazáshoz való csatlakozás hozzáadása egy csapathoz
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure kommunikációs szolgáltatások csapatának beágyazási könyvtárát Android rendszerre.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e9069b5d43044ef0d0341717a12fcce7c4a72dc7
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104804004"
---
Ebből a rövid útmutatóból megtudhatja, hogyan csatlakozhat a Teams Meeting szolgáltatáshoz az Azure kommunikációs szolgáltatások csapatának az Androidhoz készült beágyazási könyvtárával.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio)az Android-alkalmazás létrehozásához.
- Egy központilag telepített kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- Az Azure kommunikációs szolgáltatás [felhasználói hozzáférési jogkivonata](../../access-tokens.md) .

## <a name="setting-up"></a>Beállítás

### <a name="create-an-android-app-with-an-empty-activity"></a>Üres tevékenységgel rendelkező Android-alkalmazás létrehozása

Android Studio válassza az új Android Studio projekt indítása lehetőséget.

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Képernyőfelvétel: az &quot;új Android Studio projekt indítása&quot; gomb, amely a Android Studioban van kiválasztva.":::

Válassza a "üres tevékenység" projekt sablont a "telefon és a tábla" alatt.

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="Képernyőfelvétel: a Project Template (üres tevékenység) lehetőség van kiválasztva.":::

Nevezze el a projektet `TeamsEmbedAndroidGettingStarted` , állítsa a nyelvet a javára, és válassza az "API 21: Android 5,0 (nyalóka)" vagy újabb verziójának minimális ügyféloldali függvénytárát.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Képernyőfelvétel: az &quot;üres tevékenység&quot; lehetőség van kiválasztva a Project template 2. képernyőn.":::


### <a name="install-the-azure-package"></a>Az Azure-csomag telepítése

Az alkalmazás szintjének Build. gradle adja hozzá a következő sorokat a függőségek és az Android-szakaszban

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
}
```

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-common:1.0.0-beta.6'
    ...
}
```

### <a name="install-the-teams-embed-package"></a>A Teams beágyazási csomag telepítése

Töltse le a `MicrosoftTeamsSDK` csomagot.

Ezután bontsa ki a MicrosoftTeamsSDK mappát a projektek alkalmazás mappájába. Például: `TeamsEmbedAndroidGettingStarted/app/MicrosoftTeamsSDK`.

### <a name="add-teams-embed-package-to-your-buildgradle"></a>Teams-beágyazási csomag hozzáadása a Build. gradle

Az alkalmazás szintjén `build.gradle` adja hozzá a következő sort a fájl végéhez:

```groovy
apply from: 'MicrosoftTeamsSDK/MicrosoftTeamsSDK.gradle'
```

Projekt szinkronizálása gradle-fájlokkal.

### <a name="create-application-class"></a>Alkalmazás osztályának létrehozása

Hozzon létre egy új Java-osztály nevű fájlt `TeamsEmbedAndroidGettingStarted` . Ez lesz az alkalmazás osztálya, amelynek ki kell terjesztenie `TeamsSDKApplication` . [Android-dokumentáció](https://developer.android.com/reference/android/app/Application)

:::image type="content" source="../media/android/application-class-location.png" alt-text="Képernyőfelvétel: az alkalmazás osztályának létrehozásának helye Android Studio":::

```java
package com.microsoft.teamsembedandroidgettingstarted;

import com.microsoft.teamssdk.app.TeamsSDKApplication;

public class TeamsEmbedAndroidGettingStarted extends TeamsSDKApplication {
}
```

### <a name="update-themes"></a>Témák frissítése

Adja meg a stílus nevét a `AppTheme` és a `theme.xml` fájlban is `theme.xml (night)` .

:::image type="content" source="../media/android/theme-settings.png" alt-text="A Android Studio theme.xml fájljait ábrázoló képernyőfelvétel":::

```xml
<style name="AppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
```

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
```

### <a name="add-permissions-to-application-manifest"></a>Engedélyek hozzáadása az alkalmazás jegyzékfájlhoz

Adja hozzá az `RECORD_AUDIO` engedélyt az alkalmazás jegyzékfájlhoz ( `app/src/main/AndroidManifest.xml` ):  


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <application
```

### <a name="add-app-name-and-theme-to-application-manifest"></a>Alkalmazásnév és téma hozzáadása az alkalmazás jegyzékfájlhoz

Adja hozzá a "xmlns: Tools =" http://schemas.android.com/tools "kifejezést a jegyzékfájlhoz.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
```

Hozzáadás a következőhöz `.TeamsEmbedAndroidGettingStarted` `android:name` , `android:name` `tools:replace` `android:theme``@style/AppTheme`

```xml
<application
    android:name=".TeamsEmbedAndroidGettingStarted"
    tools:replace="android:name"
    android:theme="@style/AppTheme"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true">
```

### <a name="set-up-the-layout-for-the-app"></a>Az alkalmazás elrendezésének beállítása

Hozzon létre egy gombot egy AZONOSÍTÓval `join_meeting` . Navigáljon a ( `app/src/main/res/layout/activity_main.xml` ) elemre, és cserélje le a fájl tartalmát a következőre:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_meeting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Meeting"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>A fő tevékenységhez tartozó állványzat és kötések létrehozása

A létrehozott elrendezéssel a tevékenység alapszintű állványzata is felvehető a szükséges kötésekkel együtt. A tevékenység kezeli a futásidejű engedélyeket, létrehozza az értekezlet-ügyfelet, és csatlakozik egy értekezlethez a gomb megnyomásakor. Mindegyiket a saját szakasza fogja tartalmazni. 

A `onCreate` rendszer felülbírálja a metódust, `getAllPermissions` és `createAgent` hozzáadja a gomb kötéseit is `Join Meeting` . Ez csak egyszer fog történni a tevékenység létrehozásakor. További információ `onCreate` : útmutató a [tevékenység életciklusának megismeréséhez](https://developer.android.com/guide/components/activities/activity-lifecycle).

Lépjen a **MainActivity. Java** fájlra, és cserélje le a tartalmat a következő kódra:

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;

import java.util.ArrayList;
import java.util.concurrent.Callable;

public class MainActivity extends AppCompatActivity {

    private final String displayName = "John Smith";

    private MeetingUIClient meetingUIClient;
    private MeetingJoinOptions meetingJoinOptions;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        meetingJoinOptions = new MeetingJoinOptions(displayName);
        
        getAllPermissions();
        createMeetingClient();

        Button joinMeeting = findViewById(R.id.join_meeting);
        joinMeeting.setOnClickListener(l -> joinMeeting());
    }

    private void createMeetingClient() {
        // See section on creating meeting client
    }

    private void joinMeeting() {
        // See section on joining a meeting
    }

    private void getAllPermissions() {
        // See section on getting permissions
    }
}
```

### <a name="request-permissions-at-runtime"></a>Engedély kérése futásidőben

Az Android 6,0 és újabb (API-szint 23) és `targetSdkVersion` 23 vagy újabb verziója esetén az engedélyek az alkalmazás telepítése helyett futtatókörnyezetben érhetők el. Ennek támogatásához `getAllPermissions` megvalósítható az `ActivityCompat.checkSelfPermission` egyes szükséges engedélyek meghívása és használata `ActivityCompat.requestPermissions` .

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 202);
    }
}
```

> [!NOTE]
> Az alkalmazás tervezésekor vegye figyelembe, hogy mikor kell kérni ezeket az engedélyeket. Az engedélyeket szükség esetén meg kell kérni, nem pedig az idő előtt. További információ: Android- [engedélyek útmutató.](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek az Azure kommunikációs szolgáltatások csapatának beágyazási könyvtárának főbb funkcióit kezelik:

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient| A MeetingUIClient a csapat beágyazási könyvtárának fő belépési pontja. |
| MeetingJoinOptions | A MeetingJoinOptions konfigurálható beállítások, például a megjelenítendő név használatával használhatók. |
| CallState | A CallState a hívási állapot változásainak jelentésére szolgál. A lehetőségek a következők: `connecting` ,, `waitingInLobby` , `connected` és `ended` . |

## <a name="create-a-meetingclient-from-the-user-access-token"></a>MeetingClient létrehozása a felhasználói hozzáférési tokenből

A hitelesített értekezlet-ügyfelek felhasználói hozzáférési jogkivonattal hozhatók létre. Ezt a jogkivonatot általában egy olyan szolgáltatás hozza létre, amely az alkalmazásra vonatkozó hitelesítéssel rendelkezik. A felhasználói hozzáférési jogkivonatokkal kapcsolatos további tudnivalókért tekintse meg a [felhasználói hozzáférési tokenek](../../access-tokens.md) útmutatóját. A gyors üzembe helyezéshez cserélje le az `<USER_ACCESS_TOKEN>` elemet az Azure kommunikációs szolgáltatás erőforrásához generált felhasználói hozzáférési jogkivonatra.

```java
private void createMeetingClient() {
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        meetingUIClient = new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="setup-token-refreshing"></a>Beállítási jogkivonat frissítése

Hozzon létre egy hívható `tokenRefresher` metódust. Ezután hozzon létre egy `fetchToken` metódust a felhasználói jogkivonat beszerzéséhez. [Az itt megjelenő utasításokat itt találja](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-java)

```java
Callable<String> tokenRefresher = () -> {
    return fetchToken();
};

public String fetchToken() {
    // Get token
    return USER_ACCESS_TOKEN;
}
```

## <a name="get-the-teams-meeting-link"></a>A Teams Meeting-hivatkozás beszerzése

A Teams Meeting hivatkozás a Graph API-k használatával kérhető le. Ez részletesen szerepel a [Graph dokumentációjában](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
A kommunikációs szolgáltatások meghívójának ügyféloldali könyvtára fogadja a teljes Teams Meeting-hivatkozást. Ez a hivatkozás az erőforrás részeként `onlineMeeting` érhető el, a [ `joinWebUrl` tulajdonság](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) alatt elérhető módon, a Teams Meeting Meeting (összevont **értekezlet** ) URL-címéhez tartozó szükséges értekezlet-információkat is lekérheti.

## <a name="start-a-meeting-using-the-meeting-client"></a>Értekezlet elindítása az értekezlet-ügyfél használatával

Egy értekezlethez való csatlakozás a használatával végezhető el `MeetingClient` , és csak a `meetingURL` és a szükséges `JoinOptions` . Cserélje le a `<MEETING_URL>` -t egy csapat értekezlet URL-címére.

```java
/**
 * Join a meeting with a meetingURL.
 */
private void joinMeeting() {
    try {
        meetingUIClient.join("<MEETING_URL>", meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="launch-the-app-and-join-a-meeting"></a>Indítsa el az alkalmazást, és csatlakozzon egy értekezlethez

Az alkalmazás mostantól az eszköztár Run app (alkalmazás futtatása) gombjával (Shift + F10) indítható el. 

:::image type="content" source="../media/android/quickstart-android-join-meeting.png" alt-text="A befejezett alkalmazást bemutató képernyőkép.":::

:::image type="content" source="../media/android/quickstart-android-joined-meeting.png" alt-text="Képernyőfelvétel: a befejezett alkalmazás a találkozó csatlakoztatása után.":::

## <a name="sample-code"></a>Példakód

A minta alkalmazást letöltheti a [githubról](https://github.com/Azure-Samples/teams-embed-android-getting-started)
