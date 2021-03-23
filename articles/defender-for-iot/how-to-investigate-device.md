---
title: Gyanús eszköz vizsgálata
description: Ez az útmutató azt ismerteti, hogyan használható a Defender a IoT egy gyanús IoT-eszköz vizsgálatához a Log Analytics használatával.
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 32cc8d82a867ead533cbaa6802bffb4494398412
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782010"
---
# <a name="investigate-a-suspicious-iot-device"></a>Gyanús IoT-eszköz vizsgálata

A Defender for IoT szolgáltatás riasztásai egyértelmű jelzéseket biztosítanak, ha a IoT-eszközök gyanús tevékenységekben való részvétel gyanúja esetén jelentkeznek, vagy ha a jelzések egy eszköz sérülését fenyegetik.

Ebben az útmutatóban a szervezete lehetséges kockázatának meghatározásához, a probléma megoldásához, valamint a hasonló támadásoknak a jövőben való elkerüléséhez szükséges legjobb módszerek megállapításához használja a vizsgálati javaslatokat.

> [!div class="checklist"]
> * Az eszköz adatai megkeresése
> * Vizsgálat kql-lekérdezések használatával

## <a name="how-can-i-access-my-data"></a>Hogyan érhetik el az adataim?

Alapértelmezés szerint a IoT Defender a Log Analytics munkaterületen tárolja a biztonsági riasztásokat és javaslatokat. Azt is megteheti, hogy a nyers biztonsági adatait tárolja.

Az adattároláshoz Log Analytics munkaterület megkeresése:

1. Nyissa meg az IoT hubot,
1. A **Biztonság** területen válassza a **Beállítások**, majd **az adatgyűjtés** elemet.
1. Módosítsa Log Analytics munkaterület konfigurációjának részleteit.
1. Kattintson a **Mentés** gombra.

A következő beállításokkal érheti el a Log Analytics munkaterületen tárolt adatait:

1. Válassza ki és válassza ki a IoT riasztást a IoT Hubban.
1. Válassza a **további vizsgálat** lehetőséget.
1. Jelölje be **, ha szeretné látni, hogy mely eszközöknél van ilyen riasztás, kattintson ide, és tekintse meg a DeviceID oszlopot**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>A gyanús IoT-eszközök vizsgálatának lépései

A IoT-eszközökre vonatkozó elemzések és nyers információk megtekintéséhez lépjen a Log Analytics munkaterületre az [adatai eléréséhez](#how-can-i-access-my-data).

Tekintse meg az alábbi kql-lekérdezéseket a riasztások és a tevékenységek eszközön való kivizsgálásának megkezdéséhez.

### <a name="related-alerts"></a>Kapcsolódó riasztások

Megtudhatja, hogy a következő kql-lekérdezésen keresztül történtek-e más riasztások egy időben:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Hozzáféréssel rendelkező felhasználók

Annak megállapításához, hogy mely felhasználók férhetnek hozzá az eszközhöz, használja a következő kql-lekérdezést:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
Az alábbi adatfelderítési szolgáltatással derítheti fel:

- Mely felhasználók férhetnek hozzá az eszközhöz?
- A hozzáféréssel rendelkező felhasználók rendelkeznek a várt jogosultsági szinttel?

### <a name="open-ports"></a>Portok megnyitása

A következő kql-lekérdezéssel megállapíthatja, hogy az eszköz mely portjai jelenleg használatban vannak vagy használták őket:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

Az alábbi adatfelderítési szolgáltatással derítheti fel:

- Mely figyelő szoftvercsatornák aktívak az eszközön?
- A jelenleg aktív figyelő szoftvercsatornák engedélyezettek?
- Van-e gyanús távoli cím csatlakoztatva az eszközhöz?

### <a name="user-logins"></a>Felhasználói bejelentkezések

Az eszközre bejelentkezett felhasználók megkereséséhez használja a következő kql-lekérdezést:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

A lekérdezés eredményeinek felderítése:

- Mely felhasználók jelentkezett be az eszközre?
- A bejelentkezett felhasználók bejelentkeznek?
- A bejelentkezett felhasználók a várt vagy váratlan IP-címekről csatlakoznak?

### <a name="process-list"></a>Folyamatok listája

Ha szeretné megtudni, hogy a folyamatok listája a várt módon történik-e, használja a következő kql-lekérdezést:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

A lekérdezés eredményeinek felderítése:

- Voltak olyan gyanús folyamatok, amelyek az eszközön futnak?
- A megfelelő felhasználók hajtották végre a folyamatokat?
- A parancssori végrehajtások tartalmazzák a megfelelő és várt argumentumokat?

## <a name="next-steps"></a>Következő lépések

Az eszköz kivizsgálása és a kockázatok jobb megismerése érdekében érdemes lehet [Egyéni riasztásokat beállítani](quickstart-create-custom-alerts.md) a IoT-megoldás biztonsági helyzetének javítására. Ha még nem rendelkezik ügynökkel, érdemes lehet [biztonsági ügynököt telepíteni](how-to-deploy-agent.md) , vagy [egy meglévő ügynök konfigurációját módosítani](how-to-agent-configuration.md) az eredmények javítása érdekében.
