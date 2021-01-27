---
title: Hibaelhárítás az ITSM-összekötőben
description: Hibaelhárítási problémák a IT-szolgáltatásmenedzsmenti csatoló
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: fce20626d5e000c08b8a057671c06a3084534187
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896036"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Hibaelhárítás az ITSM-összekötőben

Ez a cikk a ITSM-csatoló és a hibaelhárítással kapcsolatos gyakori problémákat ismerteti.

Azure Monitor riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a problémák azonosítását és megcímzését, mielőtt a felhasználók a rendszerértesítéseket.
Az ügyfél kiválaszthatja, hogyan szeretnének értesítést kapni a riasztásról, függetlenül attól, hogy e-mailben, SMS-ben vagy webhookban, vagy akár egy megoldás automatizálásában is. Egy másik lehetőség, hogy értesítést kapjon a ITSM használatával.
A ITSM lehetővé teszi a riasztások küldését a külső jegyrendszer, például a ServiceNow számára.

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Az incidens megjelenítése és elemzése és a kérelmekre vonatkozó adatváltozás

A kapcsolatok beállításakor a ITSMC legfeljebb 120 napos incidenst tud szinkronizálni, és módosíthatja a kérelmek adatait. Az adatok naplózási rekordjainak sémája a jelen cikk [További információk szakaszában](./itsmc-synced-data.md) található.

A ITSMC irányítópult használatával megjelenítheti az incidenst és módosíthatja a kérelmeket:

![A ITSMC irányítópultot megjelenítő képernyőkép.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Az irányítópult az összekötő állapotáról is tartalmaz információkat, amelyeket kiindulási pontként használhat a kapcsolatokkal kapcsolatos problémák elemzéséhez.

Az irányítópult vizsgálatával kapcsolatos további információkért lásd: [hibajelentés az irányítópult használatával](./itsmc-dashboard.md).

### <a name="service-map"></a>Szolgáltatás térképe

A Service Map érintett számítógépeken szinkronizált incidenseket is megjelenítheti.

Service Map automatikusan feltérképezi az alkalmazás összetevőit Windows-és Linux-rendszereken, és leképezi a szolgáltatások közötti kommunikációt. Lehetővé teszi, hogy a kiszolgálókat a következőképpen tekintheti meg: olyan összekapcsolt rendszerek, amelyek kritikus szolgáltatásokat biztosítanak. Service Map megjeleníti a kiszolgálók, a folyamatok és a portok közötti kapcsolatokat bármely TCP-kapcsolattal rendelkező architektúrán keresztül. Az ügynök telepítésén kívül nincs szükség konfigurációra. További információ: [a Service Map használata](../insights/service-map.md).

Ha Service Map használ, megtekintheti a ITSM-megoldásokban létrehozott ügyfélszolgálati elemeket az itt látható módon:

![Képernyőkép, amely a Log Analytics képernyőt jeleníti meg.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="common-symptoms---how-should-it-be-resolved"></a>Gyakori tünetek – Hogyan oldható fel?

Az alábbi lista általános tüneteket tartalmaz, és hogyan oldható meg a megoldás:

* **Tünet**: Ha egy kapcsolat nem tud csatlakozni a ITSM rendszerhez, és hibaüzenet jelenik meg a **kapcsolati üzenet mentésekor** .

    **OK**: az ok a lehetőségek egyike lehet:
    * Helytelen hitelesítő adatok
     * Nem megfelelő jogosultságok
     * A webalkalmazást helyesen kell telepíteni

    **Megoldás**:
    * ServiceNow, Cherwell és elővance kapcsolatok esetén:
        * Győződjön meg arról, hogy minden kapcsolathoz helyesen adta meg a felhasználónevet, a jelszót, az ügyfél-azonosítót és az ügyfél-titkot.  
        * ServiceNow esetén: Győződjön meg arról, hogy megfelelő jogosultságokkal rendelkezik a megfelelő ITSM-termékben a [](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)kapcsolódás megadásához.
  * Service Manager kapcsolatok esetén:  
      * Győződjön meg arról, hogy a webalkalmazás üzembe helyezése sikeres volt, és hogy a hibrid kapcsolat létrejött. Annak ellenőrzéséhez, hogy a kapcsolat sikeresen létrejött-e a helyszíni Service Manager számítógéppel, lépjen a webalkalmazás URL-címére a [hibrid kapcsolat](./itsmc-connections-scsm.md#configure-the-hybrid-connection)létrehozásához szükséges dokumentációban leírtak szerint.  
* **Tünet**: ismétlődő munkaelemek jönnek létre

    **OK**: az ok a két lehetőség egyike lehet:
    * A riasztáshoz egynél több ITSM művelet van definiálva.
    * A riasztás megoldódott.

    **Megoldás**: két megoldás lehet:
    * Győződjön meg arról, hogy a ITSM műveleti csoport egyetlen riasztással rendelkezik.
    * ITSM-csatoló a riasztás feloldásakor nem támogatja a munkaelemek állapotának egyeztetését. Létrejön egy új megoldott munkaelem.
* **Tünet**: a munkaelemek nincsenek létrehozva

    **OK**: a hibajelenség néhány oka lehet:
    * Kód módosítása a ServiceNow oldalon
    * Helytelen konfigurálási engedélyek
    * A ServiceNow arányának korlátai túl magasak/alacsonyak
    * A frissítési jogkivonat lejárt
    * ITSM-csatoló törölve

    **Megoldás**: megtekintheti az [irányítópultot](itsmc-dashboard.md) , és áttekintheti a hibákat az összekötő állapota szakaszban. Tekintse át a [gyakori hibákat](itsmc-dashboard-errors.md) , és Ismerje meg, hogyan oldja meg a hibát.

* **Tünet**: nem sikerült létrehozni a műveleti csoport ITSM műveletét

    **OK**: az újonnan létrehozott ITSM-csatoló még befejezte a kezdeti szinkronizálást.

    **Megoldás**: áttekintheti a [gyakori felhasználói felületi hibákat](itsmc-dashboard-errors.md#ui-common-errors) , és megtudhatja, hogyan oldja meg a hibát.