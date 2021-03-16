---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 3eadf556d467fb67be0b0fe3a83dfd4ed89e6463
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488105"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Egy központilag telepített kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- A a `User Access Token` hívási ügyfél engedélyezéséhez. További információ a [beszerzéséről `User Access Token` ](../../access-tokens.md)
- Opcionális: fejezze be a gyors üzembe helyezési útmutatót az [alkalmazáshoz való hívás hozzáadásával](../getting-started-with-calling.md)

## <a name="setting-up"></a>Beállítás

### <a name="install-the-package"></a>A csomag telepítése

> [!NOTE]
> Ez a dokumentum a hívó ügyféloldali kódtár verziójának 1.0.0-Beta. 8 verzióját használja.

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
Ezután a modul szintjén a Build. gradle adja hozzá a következő sorokat a függőségek szakaszhoz.

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}

```

## <a name="object-model"></a>Objektummodell

Az alábbi osztályok és felületek az Azure kommunikációs szolgáltatások ügyfél-függvénytárának főbb funkcióit kezelik:

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| A CallClient a hívó ügyféloldali függvénytár fő belépési pontja.|
| CallAgent | A CallAgent a hívások indításához és kezeléséhez használatos. |
| CommunicationTokenCredential | A rendszer a CommunicationTokenCredential használja jogkivonat-hitelesítő adatként a CallAgent létrehozásához.|
| CommunicationIdentifier | A CommunicationIdentifier olyan különböző típusú résztvevőként szolgál, amely egy hívás része lenne.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>Inicializálja a CallClient, hozzon létre egy CallAgent, és nyissa meg a DeviceManager

Egy példány létrehozásához meg `CallAgent` kell hívnia a `createCallAgent` metódust egy `CallClient` példányon. Ez aszinkron módon ad vissza egy `CallAgent` példány objektumot.
A `createCallAgent` metódus argumentumként fogadja el `CommunicationUserCredential` , amely egy [hozzáférési jogkivonatot](../../access-tokens.md)ágyaz be.
Az-hoz való hozzáféréshez `DeviceManager` először létre kell hozni egy callAgent-példányt, majd a metódussal kérheti le `CallClient.getDeviceManager` a DeviceManager.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent((appContext, tokenCredential).get();
DeviceManage deviceManager = callClient.getDeviceManager().get();
```
A hívó megjelenítendő nevének megadásához használja a következő alternatív módszert:

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
CallAgent callAgent = callClient.createCallAgent((appContext, tokenCredential, callAgentOptions).get();
DeviceManage deviceManager = callClient.getDeviceManager().get();
```


## <a name="place-an-outgoing-call-and-join-a-group-call"></a>Helyezzen el egy kimenő hívást, és csatlakozzon egy csoportos híváshoz

Hívás létrehozásához és elindításához meg kell hívnia a `CallAgent.startCall()` metódust, és meg kell adnia a `Identifier` hívott (k) t.
Egy csoportos híváshoz való csatlakozáshoz meg kell hívnia a `CallAgent.join()` metódust, és meg kell adnia a GroupID. A csoport azonosítóinak GUID vagy UUID formátumúnak kell lenniük.

A hívás létrehozása és a kezdés szinkronban van. A hívási példány lehetővé teszi a hívás összes eseményére való előfizetést.

### <a name="place-a-11-call-to-a-user"></a>Helyezzen 1:1-hívást egy felhasználónak
Egy másik kommunikációs szolgáltatás felhasználójának hívásához hívja `call` meg a metódust, `callAgent` és adjon át egy olyan objektumot, amelynek a `communicationUserId` kulcsa a kulcs.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>1: n hívás elhelyezése a felhasználók és a PSTN között
> [!WARNING]
> Jelenleg a PSTN-hívás nem érhető el

Egy 1: n hívás egy felhasználónak és egy PSTN-számnak a megadásához meg kell adnia a hívott telefonszámát.
A kommunikációs szolgáltatások erőforrását úgy kell konfigurálni, hogy engedélyezze a PSTN-hívást:
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>1:1-hívás elhelyezése videokameráról
> [!WARNING]
> Jelenleg csak egy kimenő helyi videós adatfolyam hívható meg a videóban, hogy a helyi kamerákat az API használatával kell enumerálni `deviceManager` `getCameras` .
Miután kiválasztotta a kívánt kamerát, a használatával létrehozhat egy `LocalVideoStream` példányt, és `videoOptions` a tömbben lévő elemként átadhatja azt egy `localVideoStream` `call` metódusnak.
Ha a hívást csatlakoztatja, a rendszer automatikusan elindít egy video streamet a kiválasztott kamerából más résztvevő (k) felé.

> [!NOTE]
> Adatvédelmi okokból a videók nem lesznek megosztva a hívásban, ha az nem tekinthető meg helyileg.
További részletekért lásd a [helyi kamera előzetes](#local-camera-preview) verzióját.
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameras().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));
// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUserIdentifier[] participants = new CommunicationUserIdentifier[]{ new CommunicationUserIdentifier("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.startCall(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>Csatlakozás csoportos híváshoz
Új csoportos hívás indításához vagy egy folyamatos csoportos híváshoz való csatlakozáshoz meg kell hívnia a "Join" metódust, és egy objektumot kell átadnia egy `groupId` tulajdonsággal. Az értéknek GUID azonosítónak kell lennie.
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

### <a name="accept-a-call"></a>Hívás elfogadása
Hívás fogadásához hívja meg az "elfogadás" metódust egy hívás objektumon.

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

Videohívás fogadása a következőn:

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

A bejövő hívást az objektumra való feliratkozással lehet lekérni `onIncomingCall` `callAgent` :

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```


## <a name="push-notifications"></a>Leküldéses értesítések

### <a name="overview"></a>Áttekintés
A mobil leküldéses értesítések a mobil eszközökön látható előugró értesítések. A híváshoz a VoIP (Internet Protocol) leküldéses értesítések küldésére fogunk összpontosítani. A leküldéses értesítések regisztrálása, a leküldéses értesítések kezelése, majd a leküldéses értesítések regisztrációjának megszüntetése.

### <a name="prerequisites"></a>Előfeltételek

A Firebase-fiók engedélyezve van a felhőalapú üzenetküldéssel (FCM), valamint az Azure Notification hub-példányhoz csatlakoztatott Firebase Cloud Messaging szolgáltatással. További információért lásd: [kommunikációs szolgáltatások értesítései](../../../concepts/notifications.md) .
Emellett az oktatóanyag azt feltételezi, hogy a Android Studio 3,6-es vagy újabb verzióját használja az alkalmazás létrehozásához.

Az Android-alkalmazáshoz engedélyekre van szükség ahhoz, hogy fogadni tudja az értesítési üzeneteket a Firebase Cloud Messaging szolgáltatásból. A `AndroidManifest.xml` fájlban adja hozzá a következő engedélyeket közvetlenül a *<manifest... >* vagy a címke alatt. *</application>*

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>Regisztráció leküldéses értesítésekhez

A leküldéses értesítések regisztrálásához az alkalmazásnak meg kell hívnia `registerPushNotification()` egy *CallAgent* -példányt egy eszköz regisztrációs jogkivonattal.

Az eszköz regisztrációs jogkivonatának beszerzéséhez adja hozzá a Firebase ügyféloldali függvénytárát az alkalmazás moduljának *Build. gradle* fájlhoz, ha a következő sorokat adja hozzá a `dependencies` szakaszhoz, ha még nem létezik:

```
    // Add the client library for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

A projekt szintjének *Build. gradle* fájljában adja hozzá a következőt a `dependencies` szakaszban, ha még nem létezik:

```
    classpath 'com.google.gms:google-services:4.3.3'
```

Ha még nem létezik, adja hozzá a következő beépülő modult a fájl elejéhez:

```
apply plugin: 'com.google.gms.google-services'
```

Kattintson a *szinkronizálás most* elemre az eszköztáron. Adja hozzá a következő kódrészletet a Firebase Cloud Messaging ügyféloldali kódtár által generált eszköz regisztrációs jogkivonat létrehozásához az ügyfélalkalmazás példányához. ügyeljen arra, hogy az alábbi importálásokat hozzáadja a példány fő tevékenységének fejlécébe. Szükségük van arra, hogy a kódrészlet beolvassa a jogkivonatot:

```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Adja hozzá ezt a kódrészletet a jogkivonat lekéréséhez:

```
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w("PushNotification", "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d("PushNotification", "Device Registration token retrieved successfully");
                    }
                });
```
Regisztrálja az eszköz regisztrációs tokenjét a Calling Services ügyféloldali függvénytárában a bejövő hívások leküldéses értesítéseihez:

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>Leküldéses értesítések kezelését

Ha fogadni szeretné a bejövő hívások leküldéses értesítéseit, hívja a *handlePushNotification ()* függvényt egy adattartalommal rendelkező *CallAgent* -példányra.

A Firebase Cloud Messaging szolgáltatásból származó hasznos adatok beszerzéséhez először hozzon létre egy új szolgáltatást (fájl > új > Service > Service), amely kibővíti a *FirebaseMessagingService* Firebase ügyféloldali függvénytár osztályát, és felülbírálja a `onMessageReceived` metódust. Ez a módszer az eseménykezelő, ha a Firebase Cloud Messaging kézbesíti a leküldéses értesítést az alkalmazásnak.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
Adja hozzá a következő szolgáltatási definíciót a `AndroidManifest.xml` fájlhoz a <application> címkén belül:

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- A hasznos adatok lekérése után a rendszer átadhatja a *kommunikációs szolgáltatások* ügyféloldali függvénytárának, hogy egy olyan belső *IncomingCallInformation* -objektumba elemezze, amelyet a *handlePushNotification* metódusnak a *CallAgent* -példányon való meghívásával fog kezelni. A `CallAgent` példány létrehozásához hívja meg a `createCallAgent(...)` metódust az `CallClient` osztályban.

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

Ha a leküldéses értesítési üzenet kezelése sikeres, és a minden eseménykezelő megfelelően van regisztrálva, akkor az alkalmazás cseng.

### <a name="unregister-push-notifications"></a>Leküldéses értesítések regisztrációjának törlése

Az alkalmazások bármikor megszüntetik a leküldéses értesítések regisztrációját. A regisztráció megszüntetéséhez hívja `unregisterPushNotification()` meg a metódust a callAgent.

```java
try {
    callAgent.unregisterPushNotifications().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>Híváskezelő
A hívási tulajdonságokat elérheti, és különböző műveleteket hajthat végre a videóval és hanggal kapcsolatos beállítások kezeléséhez.

### <a name="call-properties"></a>Hívás tulajdonságai

A hívás egyedi AZONOSÍTÓjának beolvasása:

```java
String callId = call.getId();
```

Ha szeretne többet megtudni a Call vizsgálatban részt vevő többi résztvevőről a `remoteParticipant` `call` példányon:

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

A hívó személyazonossága, ha a hívás beérkező:

```java
CommunicationIdentifier callerId = call.getCallerId();
```

A hívás állapotának beolvasása: 

```java
CallState callState = call.getState();
```

Egy hívás aktuális állapotát jelképező karakterláncot ad vissza:
* "None" – kezdeti hívás állapota
* "Connecting" – a kezdeti átmenet állapota a hívás elhelyezése vagy elfogadása után
* "Ringing" – kimenő hívás esetén – a hívás a távoli résztvevők csengését jelzi.
* "EarlyMedia" – egy olyan állapotot jelöl, amelyben a rendszer a hívás csatlakoztatása előtt bejelentést játszik
* Csatlakoztatott – a hívás csatlakoztatva van
* "LocalHold" – a hívást a helyi résztvevő tartja, és a helyi végpont és a távoli résztvevő (k) között nem áramlanak média.
* "RemoteHold" – a hívást egy távoli résztvevő helyezi üzembe, és a helyi végpont és a távoli résztvevő (k) között nem áramlik média.
* "Leválasztás" – az átmenet állapota, mielőtt a hívás megszakadt állapotba kerül
* "Leválasztott" – végső hívás állapota


Ha meg szeretné tudni, hogy a hívás miért fejeződött be, vizsgálja meg a `callEndReason` tulajdonságot. Kód/alkódt tartalmaz: 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

Ha meg szeretné tekinteni, hogy az aktuális hívás bejövő vagy kimenő hívás-e, vizsgálja meg a `callDirection` tulajdonságot:

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.Incoming for incoming call
// callDirection == CallDirection.Outgoing for outgoing call
```

Ha szeretné ellenőrizni, hogy az aktuális mikrofon el van-e némítva, tekintse meg a következő `muted` tulajdonságot:

```java
boolean muted = call.getIsMicrophoneMuted();
```

Annak ellenőrzéséhez, hogy az aktuális hívás rögzítve van-e, tekintse meg a következő `isRecordingActive` tulajdonságot:

```java
boolean recordinggActive = call.getIsRecordingActive();
```

Az aktív videó streamek vizsgálatához tekintse meg a `localVideoStreams` gyűjteményt:

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>Némítás és némítás feloldása

A helyi végpont némításához vagy a némítás feloldásához használhatja a `mute` és az `unmute` aszinkron API-kat:

```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>Helyi videó küldésének elindítása és leállítása

A videók elindításához a kamerák enumerálását az objektum API-jával kell használni `getCameraList` `deviceManager` . Ezután hozzon létre egy új példányt a `LocalVideoStream` kívánt kamera átadásához, és adja át az API-nak `startVideo` argumentumként:

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);
Future startVideoFuture = call.startVideo(currentLocalVideoStream);
startVideoFuture.get();
```

A videó küldésének sikeres megkezdése után a rendszer egy `LocalVideoStream` példányt ad hozzá a `localVideoStreams` gyűjteményhez a hívási példányon.

```java
currentLocalVideoStream == call.getLocalVideoStreams().get(0);
```

A helyi videó leállításához adja át a `LocalVideoStream` következő `localVideoStreams` gyűjteményben elérhető példányt:

```java
call.stopVideo(currentLocalVideoStream).get();
```

Átválthat egy másik kamera-eszközre, miközben a videó küldését egy példány meghívásával végezheti el `switchSource` `LocalVideoStream` :
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>Távoli résztvevők kezelése

Minden távoli résztvevőt típus jelöl `RemoteParticipant` , és a gyűjteményen keresztül érhetők el a `remoteParticipants` hívási példányon.

### <a name="list-participants-in-a-call"></a>Hívás résztvevőinek listázása
A `remoteParticipants` gyűjtemény az adott hívásban a távoli résztvevők listáját adja vissza:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>Távoli résztvevő tulajdonságai
Bármely adott távoli résztvevő rendelkezik a hozzá társított tulajdonságokkal és gyűjteményekkel:

* A távoli résztvevő azonosítójának beolvasása.
Az identitás az "azonosító" típusok egyike
```java
CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
```

* A távoli résztvevő állapotának beolvasása.
```java
ParticipantState state = remoteParticipant.getState();
```
Az állapot lehet az egyik
* "Tétlen" – kezdeti állapot
* "EarlyMedia" – a bejelentést csak akkor játssza le a rendszer, ha a résztvevő csatlakozik a híváshoz
* "Ringing" – a résztvevő hívása csengetés
* "Connecting" – átmeneti állapot, miközben a résztvevő csatlakozik a híváshoz
* "Csatlakoztatott" – a résztvevő csatlakozik a híváshoz
* "Hold" – a résztvevő tart
* "Inlobby" – a résztvevő az előcsarnokba való felvételre vár. Jelenleg csak a Teams-együttműködési forgatókönyvben használatos
* "Leválasztott" – végső állapot – a résztvevő nem kapcsolódik a híváshoz


* Ha meg szeretné tudni, hogy a résztvevő miért hagyta el a hívást, vizsgálja meg a `callEndReason` tulajdonságot:
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* Annak ellenőrzéséhez, hogy a távoli résztvevő el van-e némítva, vagy sem, vizsgálja meg a következő `isMuted` tulajdonságot:
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* Annak ellenőrzéséhez, hogy a távoli résztvevő beszél-e vagy sem, vizsgálja meg a következő `isSpeaking` tulajdonságot:
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* Az adott résztvevő által a hívásban küldött összes videó stream vizsgálatához ellenőrizze a `videoStreams` gyűjteményt:
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>Résztvevő hozzáadása egy híváshoz

Meghívhatja a résztvevőt egy híváshoz (egy felhasználóhoz vagy egy telefonszámhoz) `addParticipant` . Ez szinkron módon visszaküldi a távoli résztvevő példányát.

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>Résztvevő eltávolítása egy hívásból
Ha el szeretne távolítani egy résztvevőt a hívásból (vagy egy felhasználóról vagy egy telefonszámról), akkor meghívhatja `removeParticipant` .
Ez a megoldás aszinkron módon fog megjelenni, ha a résztvevő el lett távolítva a hívásból.
A résztvevő is el lesz távolítva a `remoteParticipants` gyűjteményből.
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

## <a name="render-remote-participant-video-streams"></a>A távoli résztvevő video streamek megjelenítése
A távoli résztvevők videó streamek és a képernyő-megosztási streamek listázásához vizsgálja meg a `videoStreams` gyűjteményeket:
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Ha egy `RemoteVideoStream` távoli résztvevőtől szeretne megjeleníteni egy eseményt, elő kell fizetnie egy `OnVideoStreamsUpdated` eseményre.

Az eseményen belül a tulajdonság értéke `isAvailable` true (igaz) érték azt jelzi, hogy a távoli résztvevő jelenleg streamet küld. Ha ez megtörténik, hozzon létre egy új példányt az a `Renderer` , majd hozzon létre egy új aszinkron API-t, `RendererView` `createView` és csatolja `view.target` az alkalmazás felhasználói felületén.

Ha a távoli adatfolyamok változásai elérhetők, eldöntheti, hogy az egész renderelő, egy adott `RendererView` vagy megtartható, de ez az üres videó keretének megjelenítését eredményezi.

```java
Renderer remoteVideoRenderer = new Renderer(remoteParticipantStream, appContext);
View uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>Távoli video stream tulajdonságai
A távoli video stream több tulajdonsággal rendelkezik

* `Id` -Egy távoli videó stream azonosítója
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` -Lehet "Video" vagy "ScreenSharing"
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` – Azt jelzi, hogy a távoli résztvevői végpont aktívan küld-e adatfolyamként
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>Megjelenítési metódusok és tulajdonságok
Renderelő objektum API-k következő

* Hozzon létre egy `RendererView` példányt, amely később csatolható az alkalmazás felhasználói felületén a távoli video stream megjelenítéséhez.
```java
// Create a view for a video stream
renderer.createView()
```
* A megjelenítő és a `RendererView` hozzá tartozó összes társítható. A rendszer akkor hívja meg, ha eltávolította az összes társított nézetet a felhasználói felületen.
```java
renderer.dispose()
```

* `StreamSize` – távoli video stream mérete (szélesség/magasság)
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>RendererView metódusok és tulajdonságok
A létrehozásakor `RendererView` megadhatja a `scalingMode` `mirrored` nézetre alkalmazni kívánt és tulajdonságokat: a skálázási mód a "stretch" lehet. "Crop" | Ha a értékre `mirrored` van állítva `true` , a megjelenített adatfolyam függőlegesen lesz tükrözve.

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

A létrehozott RendererView az alábbi kódrészlettel csatlakoztatható az alkalmazás felhasználói felületéhez:
```java
layout.addView(rendererView);
```

Később frissítheti a skálázási módot úgy, hogy `updateScalingMode` az API-t meghívja a RendererView objektumon egy ScalingMode. stretch használatával | ScalingMode. Crop | ScalingMode. Fit argumentumként.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>Eszközfelügyelet

`DeviceManager` lehetővé teszi olyan helyi eszközök enumerálását, amelyek a hang-és video-adatfolyamok továbbítására szolgáló hívásokban használhatók. Azt is lehetővé teszi, hogy engedélyt kérjen a felhasználótól, hogy a natív böngésző API használatával hozzáférjen a mikrofonhoz és a fényképezőgéphez.

`deviceManager`A hívási módszer használatával férhet hozzá `callClient.getDeviceManager()` .
> [!WARNING]
> A `callAgent` DeviceManager való hozzáféréshez jelenleg egy objektumot kell létrehozni először.

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>Helyi eszközök enumerálása

A helyi eszközökhöz való hozzáféréshez használhat enumerálási metódusokat a Eszközkezelő. A számbavétel egy szinkron művelet.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>Alapértelmezett mikrofon/hangszóró beállítása

Az Eszközkezelővel beállíthat egy alapértelmezett eszközt, amelyet a rendszer a hívás indításakor fog használni.
Ha az ügyfél alapértelmezései nincsenek beállítva, a kommunikációs szolgáltatások az operációs rendszer alapértelmezett értékeire kerülnek vissza.

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophones().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakers().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>Helyi kamera előzetes verziója

A `DeviceManager` és `Renderer` a segítségével megkezdheti a streamek megjelenítését a helyi kameráról. Ezt a streamet nem lehet elküldeni a többi résztvevőnek; Ez egy helyi előnézeti hírcsatorna. Ez egy aszinkron művelet.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
videoOptions = new VideoOptions(currentVideoStream);

Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="eventing-model"></a>Eseményvezérelt modell
Előfizethet a legtöbb tulajdonságra és gyűjteményre, hogy értesítést kapjon az értékek változásakor.

### <a name="properties"></a>Tulajdonságok
Előfizetés az `property changed` eseményekre:

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>Gyűjtemények
Előfizetés az `collection updated` eseményekre:

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
