---
title: Az Azure Notification Hubs használata Javával
description: Ismerje meg, hogyan használhatja az Azure Notification Hubst Java háttérrendszer használatával.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-java
ms.openlocfilehash: 09553f587916e8204541b36f259a450d72c1b270
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87322941"
---
# <a name="how-to-use-notification-hubs-from-java"></a>A Notification Hubs használata a Java-ból

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Ez a témakör az új, teljes mértékben támogatott Azure Notification hub Java SDK főbb funkcióit ismerteti.
Ez a projekt egy nyílt forráskódú projekt, amely a teljes SDK-kódot a [Java SDK]-ban tekintheti meg.

Általánosságban elmondható, hogy egy Java/PHP/Python/Ruby háttérrendszer összes Notification Hubs funkcióját az értesítési központ REST felületén keresztül érheti el az MSDN témakör [Notification HUBS REST API](/previous-versions/azure/reference/dn223264(v=azure.100))-k című részében leírtak szerint. Ez a Java SDK egy vékony burkolót biztosít ezen REST-felületek javában.

Az SDK jelenleg a következőket támogatja:

* SZIFILISZ Notification Hubs
* SZIFILISZ a regisztrációnál
* Telepítés kezelése
* Importálási/exportálási regisztrációk
* Normál küldés
* Ütemezett küldés
* Aszinkron műveletek Java NIO-n keresztül
* Támogatott platformok: APNS (iOS), FCM (Android), WNS (Windows áruházbeli alkalmazások), MPNS (Windows Phone-telefon), ADM (Amazon Kindle Fire), Baidu (Android Google Services nélkül)

## <a name="sdk-usage"></a>SDK-használat

### <a name="compile-and-build"></a>Fordítás és létrehozás

[Maven] használata

Létrehozás:

```cmd
mvn package
```

## <a name="code"></a>Code

### <a name="notification-hub-cruds"></a>Értesítési központ – SZIFILISZek

**Hozzon létre egy NamespaceManager:**

```java
NamespaceManager namespaceManager = new NamespaceManager("connection string")
```

**Értesítési központ létrehozása:**

```java
NotificationHubDescription hub = new NotificationHubDescription("hubname");
hub.setWindowsCredential(new WindowsCredential("sid","key"));
hub = namespaceManager.createNotificationHub(hub);
```

 OR

```java
hub = new NotificationHub("connection string", "hubname");
```

**Értesítési központ beolvasása:**

```java
hub = namespaceManager.getNotificationHub("hubname");
```

**Értesítési központ frissítése:**

```java
hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
hub = namespaceManager.updateNotificationHub(hub);
```

**Értesítési központ törlése:**

```java
namespaceManager.deleteNotificationHub("hubname");
```

### <a name="registration-cruds"></a>Regisztrációs SZIFILISZek

**Értesítési központ ügyfelének létrehozása:**

```java
hub = new NotificationHub("connection string", "hubname");
```

**Windows-regisztráció létrehozása:**

```java
WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
reg.getTags().add("myTag");
reg.getTags().add("myOtherTag");
hub.createRegistration(reg);
```

**IOS-regisztráció létrehozása:**

```java
AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
reg.getTags().add("myTag");
reg.getTags().add("myOtherTag");
hub.createRegistration(reg);
```

Hasonlóképpen létrehozhatók az Android (FCM), a Windows Phone-telefon (MPNS) és a Kindle Fire (ADM) regisztrációi is.

**Sablon regisztrációjának létrehozása:**

```java
WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
reg.getHeaders().put("X-WNS-Type", "wns/toast");
hub.createRegistration(reg);
```

**Regisztrációk létrehozása a regisztrációs azonosító és a upsert minta használatával:**

Eltávolítja az összes elveszett válasz miatti ismétlődéseket, ha a regisztrációs azonosítókat az eszközön tárolja:

```java
String id = hub.createRegistrationId();
WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
hub.upsertRegistration(reg);
```

**Regisztrációk frissítése:**

```java
hub.updateRegistration(reg);
```

**Regisztrációk törlése:**

```java
hub.deleteRegistration(regid);
```

**Regisztrációk lekérdezése:**

* **Egyszeri regisztráció beolvasása:**

```java
hub.getRegistration(regid);
```

* **Az összes regisztráció beolvasása a központban:**

```java
hub.getRegistrations();
```

* **Regisztrációk beszerzése a címkével:**

```java
hub.getRegistrationsByTag("myTag");
```

* **Regisztrációk beolvasása csatornán keresztül:**

```java
hub.getRegistrationsByChannel("devicetoken");
```

A gyűjtemény összes lekérdezése támogatja a $top és a folytatási jogkivonatokat.

### <a name="installation-api-usage"></a>Telepítési API-használat

A telepítési API egy alternatív mechanizmus a regisztrációs felügyelethez. Ahelyett, hogy több regisztrációt kellene fenntartania, amelyek nem triviálisak, és könnyen, helytelenül vagy nem hatékonyan végezhetők el, mostantól egyetlen telepítési objektumot is használhat.

A telepítés mindent tartalmaz, amire szüksége van: push Channel (eszköz token), címkék, sablonok, másodlagos csempék (WNS és APNS). Nem kell meghívnia a szolgáltatást az azonosító többé-csak a GUID-azonosító vagy bármely más azonosító beszerzéséhez, tartsa azt az eszközön, és küldje el a háttérnek a leküldéses csatornával (eszköz token) együtt.

A háttérben csak egyetlen hívást kell végrehajtania, hogy `CreateOrUpdateInstallation` teljesen idempotens, ezért szükség esetén próbálkozzon újra.

Példa az Amazon Kindle Fire:

```java
Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
hub.createOrUpdateInstallation(installation);
```

Ha frissíteni kívánja:

```java
installation.addTag("foo");
installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
hub.createOrUpdateInstallation(installation);
```

Speciális forgatókönyvek esetén használja a részleges frissítési képességet, amely lehetővé teszi a telepítési objektum csak bizonyos tulajdonságainak módosítását. A részleges frissítés a telepítési objektumon futtatható JSON-javítási műveletek részhalmaza.

```java
PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
```

Telepítés törlése:

```java
hub.deleteInstallation(installation.getInstallationId());
```

`CreateOrUpdate`, `Patch` és `Delete` végül konzisztensek a-val `Get` . A kért művelet csak a rendszer várólistára kerül a hívás során, és a háttérben kerül végrehajtásra. A Get nem a fő futtatókörnyezeti forgatókönyvhöz készült, hanem csak hibakeresési és hibaelhárítási célból, ezért a szolgáltatás szigorúan szabályozza.

A küldési folyamat ugyanaz, mint a regisztrációk esetében. Értesítés küldése az adott telepítéshez – csak a "telepítésazonosító: {desired-id}" címkét használja. Ebben az esetben a kód a következő:

```java
Notification n = Notification.createWindowsNotification("WNS body");
hub.sendNotification(n, "InstallationId:{installation-id}");
```

Több sablon közül:

```java
Map<String, String> prop =  new HashMap<String, String>();
prop.put("value3", "some value");
Notification n = Notification.createTemplateNotification(prop);
hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
```

### <a name="schedule-notifications-available-for-standard-tier"></a>Ütemezett értesítések (a STANDARD szintű csomaghoz érhető el)

Ugyanaz, mint a normál küldés, de egy további paraméter-scheduledTime, amely azt mondja, hogy mikor kell elküldeni az értesítést. A szolgáltatás minden olyan időpontot elfogad, amely már 5 perc és most 7 nap között van.

**Windows natív értesítésének beütemezett időpontja:**

```java
Calendar c = Calendar.getInstance();
c.add(Calendar.DATE, 1);
Notification n = Notification.createWindowsNotification("WNS body");
hub.scheduleNotification(n, c.getTime());
```

### <a name="importexport-available-for-standard-tier"></a>Importálás/Exportálás (STANDARD szintű csomag esetén érhető el)

Előfordulhat, hogy tömeges műveletet kell végrehajtania a regisztrációk ellen. Általában egy másik rendszerrel való integráció vagy egy nagy javítás a címkék frissítéséhez. Nem javasoljuk a Get/Update folyamat használatát, ha több ezer regisztrációt vesznek igénybe. A rendszer importálási/exportálási funkciója a forgatókönyvnek megfelelően lett kialakítva. A Storage-fiókhoz tartozó blob-tárolóhoz hozzáférést biztosíthat a bejövő adat forrásaként, illetve a kimenet helyét.

**Exportálási feladatok elküldése:**

```java
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);
```

**Importálási feladatok elküldése:**

```java
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
job.setImportFileUri("input file uri with SAS signature");
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);
```

**Várakozás a feladatok elvégzésére:**

```java
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}
```

**Az összes feladat beolvasása:**

```java
List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
```

**URI SAS-aláírással:**

 Ez az URL-cím a blob-fájl vagy a blob-tároló URL-címe, valamint a paraméterek, például az engedélyek és a lejárati idő, valamint a fiók SAS-kulcsával létrehozott összes művelet aláírása. Az Azure Storage Java SDK sokoldalú képességekkel rendelkezik, beleértve az URI-k létrehozását is. Az egyszerű Alternatív megoldásként tekintse meg a `ImportExportE2E` teszt osztályt (a GitHub helyéről), amely az aláírási algoritmus alapszintű és kompakt implementációját használja.

### <a name="send-notifications"></a>Értesítések küldése

Az értesítési objektum egyszerűen egy fejléceket tartalmazó törzs, néhány segédprogram-módszer segíti a natív és sablonos értesítések objektumainak összeállítását.

* **Windows áruház és Windows Phone-telefon 8,1 (nem Silverlight)**

```java
String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
Notification n = Notification.createWindowsNotification(toast);
hub.sendNotification(n);
```

* **iOS**

```java
String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
Notification n = Notification.createAppleNotification(alert);
hub.sendNotification(n);
```

* **Android**

```java
String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
Notification n = Notification.createFcmNotification(message);
hub.sendNotification(n);
```

* **Windows Phone-telefon 8,0 és 8,1 Silverlight**

```java
String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
            "<wp:Notification xmlns:wp=\"WPNotification\">" +
                "<wp:Toast>" +
                    "<wp:Text1>Hello from Java!</wp:Text1>" +
                "</wp:Toast> " +
            "</wp:Notification>";
Notification n = Notification.createMpnsNotification(toast);
hub.sendNotification(n);
```

* **Kindle-Tűz**

```java
String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
Notification n = Notification.createAdmNotification(message);
hub.sendNotification(n);
```

* **Küldés címkékre**
  
```java
Set<String> tags = new HashSet<String>();
tags.add("boo");
tags.add("foo");
hub.sendNotification(n, tags);
```

* **Küldés a címkére kifejezés**

```java
hub.sendNotification(n, "foo && ! bar");
```

* **Sablon küldése – értesítés**

```java
Map<String, String> prop =  new HashMap<String, String>();
prop.put("prop1", "v1");
prop.put("prop2", "v2");
Notification n = Notification.createTemplateNotification(prop);
hub.sendNotification(n);
```

A Java-kód futtatásának most egy értesítést kell létrehoznia a céleszköz.

## <a name="next-steps"></a><a name="next-steps"></a>További lépések

Ez a témakör bemutatja, hogyan hozhat létre egy egyszerű Java REST-ügyfelet Notification Hubs számára. Ebből a menüből:

* Töltse le a teljes [Java SDK]-t, amely tartalmazza a teljes SDK-kódot.
* Játsszon a mintákkal:
  * [Ismerkedés a Notification Hubs]
  * [Legfrissebb hírek küldése]
  * [Honosított feltörési Hírek küldése]
  * [Értesítések küldése hitelesített felhasználóknak]
  * [Platformfüggetlen értesítések küldése hitelesített felhasználóknak]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Ismerkedés a Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Legfrissebb hírek küldése]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Honosított feltörési Hírek küldése]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Értesítések küldése hitelesített felhasználóknak]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Platformfüggetlen értesítések küldése hitelesített felhasználóknak]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
