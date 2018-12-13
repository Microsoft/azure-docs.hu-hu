---
title: A Notification Hubs használata javával
description: Útmutató az Azure Notification hubs szolgáltatás használata egy Java-háttérrendszert.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 3251e2ecc9171081c5128dd0782eecdf83064114
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312255"
---
# <a name="how-to-use-notification-hubs-from-java"></a>A Notification Hubs javából használata
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Ez a témakör ismerteti az új teljes mértékben támogatott hivatalos Azure Notification Hub Java SDK fő funkcióira.
Ez a projekt egy nyílt forráskódú projekt, és megtekintheti a teljes SDK kódot [Java SDK].

Általánosságban elmondható, elérheti az összes Notification Hubs szolgáltatás háttérrendszerből egy Java/PHP vagy Python vagy Ruby használatával a Notification Hub REST-felület, az MSDN-témakörben leírtak szerint [Notification Hubs – REST API-k](https://msdn.microsoft.com/library/dn223264.aspx). A Java SDK-val dinamikusan burkolót biztosít a REST-felületeihez Java-környezetben keresztül.

Az SDK jelenleg támogatja:

* CRUD-MŰVELETEKKEL, a Notification hubs szolgáltatásban
* Regisztrációs CRUD-MŰVELETEKKEL
* Felügyeleti telepítés
* Importálási/exportálási regisztrációk
* Rendszeresen küld
* Ütemezett küldése
* Az aszinkron műveletek Java NIO keresztül
* Támogatott platformok: Az APNS (iOS), a GCM (Android), a WNS (Windows Store apps), az MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android Google services nélkül)

## <a name="sdk-usage"></a>SDK-használata
### <a name="compile-and-build"></a>Fordítás és felépítés
Használat [Maven 3]

Hozhat létre:

    mvn package

## <a name="code"></a>Kód
### <a name="notification-hub-cruds"></a>Notification Hub CRUDs
**Hozzon létre egy NamespaceManager:**

    NamespaceManager namespaceManager = new NamespaceManager("connection string")

**Értesítési központ létrehozása:**

    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);

 VAGY

    hub = new NotificationHub("connection string", "hubname");

**Értesítési központ beolvasása:**

    hub = namespaceManager.getNotificationHub("hubname");

**Értesítési központ frissítése:**

    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);

**Értesítési központ törlése:**

    namespaceManager.deleteNotificationHub("hubname");

### <a name="registration-cruds"></a>Regisztrációs CRUDs
**Hozzon létre ügyfél-értesítési központ:**

    hub = new NotificationHub("connection string", "hubname");

**Windows regisztrációs létrehozása:**

    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);

**IOS-es regisztrációs létrehozása:**

    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);

Hasonlóképpen regisztrációk hozhat létre Android (GCM), a Windows Phone (MPNS) és a Kindle Fire (ADM).

**Sablonregisztrációk létrehozása:**

    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);

**Hozzon létre regisztrációk használatával hozzon létre regisztrációs Azonosítót + upsert minta**

Eltávolítja az ismétlődéseket miatt elveszett válaszokat, ha az eszköz regisztrációs azonosítókkal tárolására:

    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);

**Regisztrációk frissítése:**

    hub.updateRegistration(reg);

**Odstranit registrace:**

    hub.deleteRegistration(regid);

**Lekérdezés regisztrációk:**

* **Egyetlen regisztrációs lekérése:**

        hub.getRegistration(regid);

* **Kérje le az összes regisztrációk hub:**

        hub.getRegistrations();

* **Kérje le a regisztrációk címkével:**

        hub.getRegistrationsByTag("myTag");

* **Csatorna regisztrációk lekéréséhez:**

        hub.getRegistrationsByChannel("devicetoken");


Minden gyűjtemény lekérdezések $top és a folytatási token támogatja.

### <a name="installation-api-usage"></a>Telepítés API-használat
Telepítés API regisztrációkezelés alternatív módszereket is. Több regisztrációk, amely nem triviális, és előfordulhat, hogy könnyedén elvégezheti tévesen vagy töredezetté, karbantartása helyett, most már lehetőség egyetlen telepítés objektummal. Telepítés tartalmaz minden szükséges: channel (eszköztoken), a címkéket, a sablonok, a másodlagos csempék leküldéses (a WNS és APNS). Nem kell többé Azonosítójának lekéréséhez – csak hoznak létre GUID Azonosítót vagy bármely más azonosító, legyen az eszközön és küldeni a háttérrendszerhez leküldéses channel (eszköztoken) együtt a szolgáltatás hívásához.
A háttérrendszer csak egyetlen hívással kell tennie: CreateOrUpdateInstallation, teljes körűen idempotens, így nyugodtan szükség esetén ismételje meg.

Az Amazon Kindle Fire példaként:

    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);

Ha frissíteni szeretne:

    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);

Speciális forgatókönyvek esetén használja a részleges frissítési funkció, amely lehetővé teszi, hogy a telepítési objektum csak adott tulajdonságainak módosítása. Részleges frissítést futtathatja a telepítési objektum JSON-javítás műveletek részhalmazát.

    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);

Telepítés törlése:

    hub.deleteInstallation(installation.getInstallationId());

CreateOrUpdate, Patch és Delete idővel konzisztenssé váljanak a Get. A kért művelet csak a hívás során a rendszer várólistára kerül, és a háttérben hajtja végre. Get nem alkalmas a futtatókörnyezet fő forgatókönyv, de csak a hibakeresési és hibaelhárítási célokra, szigorúan szabályozott a szolgáltatás által.

Küldési telepítés folyamatábrája Registrace azonos. Értesítés az adott telepítési cél – címke használja "InstallationId: {desired-id}". Ebben az esetben a kódja:

    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");

Egy vagy több sablont:

    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");

### <a name="schedule-notifications-available-for-standard-tier"></a>Ütemezett értesítések (STANDARD szinten érhető el)
A ugyanaz, mint a rendszeres küldési, de egy további paraméter - scheduledTime, amikor értesítési kézbesítési alkalmazásszabályzatban. A szolgáltatás bármikor now + 5 perc közötti elfogadja és now + 7 nap.

**A Windows natív értesítési ütemezése:**

    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());

### <a name="importexport-available-for-standard-tier"></a>Import/Export (STANDARD szinten érhető el)
Egyes esetekben ez szükséges regisztrációk tömeges művelet végrehajtásához. Általában egy másik rendszer-integráció a, vagy csak a nagy tegyük fel, hogy javítást frissítése a címkéket. Használja a Get/frissítési folyamat, ha ezer résztvevő nem ajánlott. Importálási/exportálási funkciót azért hoztuk, hogy biztosítsák a forgatókönyvet. Alapvetően egy hozzáférést szeretne biztosítani néhány blob-tároló a tárfiók a bejövő adatok és a kimeneti helyet forrásként.

**Exportálási feladat elküldése:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);


**Importálási feladat elküldése:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);

**Várjon, amíg a feladat történik:**

    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }

**Minden feladat beolvasása:**

    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();

**Az SAS-aláírás URI:** Az URL-cím néhány blob, fájl vagy a blob-tároló URL-CÍMÉT, valamint paramétereknek például engedélyek és a lejárati idő plusz aláírását, ezeket a módosításokat végzett a fiók SAS-kulcs használatával. Az Azure Storage Java SDK-t többek között az ilyen típusú URI-k létrehozását, a széles körű lehetőségekkel rendelkezik. Egyszerű helyett egy pillantást ImportExportE2E teszt osztályt (a GitHub helye) rendelkező, alap- és kompakt aláíró algoritmus végrehajtása is igénybe vehet.

### <a name="send-notifications"></a>Értesítések küldése
Értesítési objektum egyszerűen csak a fejlécek törzséhez, a natív és a sablon értesítések objektumok épületben érdekében bizonyos segédprogram-metódusokat.

* **Windows Store és a Windows Phone 8.1-es (nem Silverlight)**

        String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
        Notification n = Notification.createWindowsNotification(toast);
        hub.sendNotification(n);
* **iOS**

        String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
        Notification n = Notification.createAppleNotification(alert);
        hub.sendNotification(n);
* **Android**

        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createGcmNotification(message);
        hub.sendNotification(n);
* **Windows Phone 8.0-s és 8.1 Silverlight**

        String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>Hello from Java!</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
        Notification n = Notification.createMpnsNotification(toast);
        hub.sendNotification(n);
* **A kindle Fire**

        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createAdmNotification(message);
        hub.sendNotification(n);
* **A címkék küldése**
  
        Set<String> tags = new HashSet<String>();
        tags.add("boo");
        tags.add("foo");
        hub.sendNotification(n, tags);
* **Címkét alkotó kifejezés küldése**

        hub.sendNotification(n, "foo && ! bar");
* **Sablon-értesítés küldése**

        Map<String, String> prop =  new HashMap<String, String>();
        prop.put("prop1", "v1");
        prop.put("prop2", "v2");
        Notification n = Notification.createTemplateNotification(prop);
        hub.sendNotification(n);

A Java-kód futtatása most kell előállítania egy értesítés jelenik meg a céleszközön.

## <a name="next-steps"></a>Következő lépések
Ez a témakör láthatta, hogyan hozhat létre egy egyszerű Java REST-ügyfél a Notification hubs szolgáltatásra. Itt a következőket teheti:

* Töltse le a teljes [Java SDK], amely tartalmazza a teljes SDK kódot.
* Próbálja ki a mintákat:
  * [Notification Hubs használatának első lépései]
  * [Legfrissebb hírek elküldése]
  * [Honosított legfrissebb hírek elküldése]
  * [Értesítések küldése hitelesített felhasználóknak]
  * [Platformfüggetlen értesítések küldése hitelesített felhasználóknak]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Notification Hubs használatának első lépései]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Legfrissebb hírek elküldése]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Honosított legfrissebb hírek elküldése]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Értesítések küldése hitelesített felhasználóknak]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Platformfüggetlen értesítések küldése hitelesített felhasználóknak]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven 3]: http://maven.apache.org/

