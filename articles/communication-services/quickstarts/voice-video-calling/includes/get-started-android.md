---
title: Gyors útmutató – VOIP-hívás hozzáadása Android-alkalmazáshoz az Azure Communication Services használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure kommunikációs szolgáltatásokat az ügyféloldali kódtár meghívásához az Androidhoz
author: matthewrobertson
ms.author: marobert
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: a387261b392ea6718941f5eabe889e0c1a41fd5a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750384"
---
Ebből a rövid útmutatóból megtudhatja, hogyan indíthat el hívást az Android rendszerhez készült ügyféloldali kódtárat hívó Azure kommunikációs szolgáltatással.

> [!NOTE]
> Ez a dokumentum a hívó ügyféloldali kódtár verziójának 1.0.0-Beta. 8 verzióját használja.

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

Válassza ki az "API 26: Android 8,0 (Oreo)" minimális ügyféloldali kódtárat.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Képernyőfelvétel: az &quot;üres tevékenység&quot; lehetőség van kiválasztva a Project template 2. képernyőn.":::


### <a name="install-the-package"></a>A csomag telepítése

<!-- TODO: update with instructions on how to download, install and add package to project -->
Keresse meg a projekt szintjének Build. gradle, és ügyeljen arra, hogy `mavenCentral()` a `buildscript` és a `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Ezután a modul szintű Build. gradle adja hozzá a következő sorokat a függőségek és az Android-szakaszban

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}
```

### <a name="add-permissions-to-application-manifest"></a>Engedélyek hozzáadása az alkalmazás jegyzékfájlhoz

A híváshoz szükséges engedélyek kéréséhez először deklarálni kell őket az Application manifest () alkalmazásban `app/src/main/AndroidManifest.xml` . Cserélje le a fájl tartalmát a következőre:

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our calling client library depends on the Apache HTTP client library.
When targeting Android client library 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```

### <a name="set-up-the-layout-for-the-app"></a>Az alkalmazás elrendezésének beállítása

Két bemenetre van szükség: egy szövegbevitelt a hívott AZONOSÍTÓhoz, valamint egy gombot a hívás elhelyezéséhez. Ezek hozzáadhatók a tervezőben vagy az elrendezési XML-fájl szerkesztésével. Hozzon létre egy gombot egy AZONOSÍTÓval `call_button` és egy szövegbevitelsel `callee_id` . Navigáljon a ( `app/src/main/res/layout/activity_main.xml` ) elemre, és cserélje le a fájl tartalmát a következőre:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <EditText
        android:id="@+id/callee_id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Callee Id"
        android:inputType="textPersonName"
        app:layout_constraintBottom_toTopOf="@+id/call_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>A fő tevékenységhez tartozó állványzat és kötések létrehozása

Az elrendezés létrehozásával a kötések hozzáadhatók, valamint a tevékenység alapszintű állványzata is. A tevékenység kezeli a futásidejű engedélyeket, létrehozza a hívási ügynököt, és a gomb megnyomásakor a hívást helyezi el. Mindegyiket a saját szakasza fogja tartalmazni. A `onCreate` rendszer felülbírálja a metódust, `getAllPermissions` és `createAgent` hozzáadja a hívás gomb kötéseit is. Ez csak egyszer fog történni a tevékenység létrehozásakor. További információ `onCreate` : útmutató a [tevékenység életciklusának megismeréséhez](https://developer.android.com/guide/components/activities/activity-lifecycle).

Lépjen a **MainActivity. Java** fájlra, és cserélje le a tartalmat a következő kódra:

```java
package com.contoso.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.StartCallOptions;


import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    
    private CallAgent callAgent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();
        
        // Bind call button to call `startCall`
        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
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
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> Az alkalmazás tervezésekor vegye figyelembe, hogy mikor kell kérni ezeket az engedélyeket. Az engedélyeket szükség esetén meg kell kérni, nem pedig az idő előtt. További információ: Android- [engedélyek útmutató.](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>Objektummodell

Az alábbi osztályok és felületek az Azure kommunikációs szolgáltatások ügyfél-függvénytárának főbb funkcióit kezelik:

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| A CallClient a hívó ügyféloldali függvénytár fő belépési pontja.|
| CallAgent | A CallAgent a hívások indításához és kezeléséhez használatos. |
| CommunicationUserCredential | A rendszer a CommunicationUserCredential használja jogkivonat-hitelesítő adatként a CallAgent létrehozásához.|
| CommunicationIdentifier | A CommunicationIdentifier olyan különböző típusú résztvevőként szolgál, amely egy hívás része lenne.|

## <a name="create-an-agent-from-the-user-access-token"></a>Ügynök létrehozása a felhasználói hozzáférési tokenből

A felhasználói jogkivonat használatával a hitelesített hívási ügynök hozható létre. Általában ez a token az alkalmazáshoz tartozó hitelesítéssel generált szolgáltatásból jön létre. A felhasználói hozzáférési jogkivonatokkal kapcsolatos további információkért tekintse meg a [felhasználói hozzáférési tokenek](../../access-tokens.md) útmutatóját. A gyors üzembe helyezéshez cserélje le az `<User_Access_Token>` elemet az Azure kommunikációs szolgáltatás erőforrásához generált felhasználói hozzáférési jogkivonatra.

```java

/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    String userToken = "<User_Access_Token>";

    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(userToken);
        callAgent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}

```

## <a name="start-a-call-using-the-call-agent"></a>Hívás indítása a hívási ügynök használatával

A hívás elhelyezése a hívási ügynökön keresztül végezhető el, és csak a hívott azonosítók és a hívási beállítások listájának megadását igényli. A gyors útmutatóhoz a rendszer az alapértelmezett hívási beállításokat videó nélkül, a szöveges bemenetből pedig egyetlen meghívó azonosítót fog használni.

```java
/**
 * Place a call to the callee id provided in `callee_id` text input.
 */
private void startCall() {
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    
    StartCallOptions options = new StartCallOptions();

    callAgent.call(
        getApplicationContext(),
        new CommunicationUserIdentifier[] {new CommunicationUserIdentifier(calleeId)},
        options);
}
```


## <a name="launch-the-app-and-call-the-echo-bot"></a>Indítsa el az alkalmazást, és hívja meg az ECHO robotot

Az alkalmazás mostantól az eszköztár Run app (alkalmazás futtatása) gombjával (Shift + F10) indítható el. Ellenőrizze, hogy meghívja-e a hívásokat `8:echo123` . Egy előre rögzített üzenet jelenik meg, majd újra megismétli az üzenetet.

:::image type="content" source="../media/android/quickstart-android-call-echobot.png" alt-text="A befejezett alkalmazást bemutató képernyőkép.":::

## <a name="sample-code"></a>Példakód

A minta alkalmazást letöltheti a [githubról](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add%20Voice%20Calling)
