---
title: ITSMC kapcsolatos problémák elhárítása
description: Megtudhatja, Hogyan oldhatók meg a IT-szolgáltatásmenedzsmenti csatoló gyakori problémái.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: e8ae306a4900bc6e5815f6fc251dfa1b8b22964d
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492416"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>A IT-szolgáltatásmenedzsmenti csatoló kapcsolatos problémák elhárítása

Ez a cikk a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) gyakori problémáit és azok hibaelhárítását ismerteti.

Azure Monitor proaktívan értesíti Önt a riasztásokról, amikor fontos feltételeket talál a megfigyelési adataiban. Ezek a riasztások segítenek a problémák azonosításában és kezelésében a rendszerüzenetek felhasználói előtt.

Kiválaszthatja, hogyan szeretne riasztásokat kapni. Választhatja a levelezés, az SMS vagy a webhook lehetőséget, vagy akár automatizálhatja a megoldást is. 

Egy másik lehetőség, hogy értesítést kapjon a ITSMC-on keresztül. A ITSMC lehetőséget biztosít a riasztások küldésére egy külső jegyrendszer, például a ServiceNow számára.

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>Az incidens elemzése és a kérelmekre vonatkozó adatváltozás

A kapcsolatok beállításakor a ITSMC legfeljebb 120 napos incidenst tud szinkronizálni, és módosíthatja a kérelmek adatait. Az adatokhoz tartozó log Record-séma beszerzéséhez tekintse [meg a ITSM-termékkel szinkronizált adatok](./itsmc-synced-data.md) című cikket.

A ITSMC irányítópult használatával megjelenítheti az incidenst és módosíthatja a kérelmeket:

![A ITSMC irányítópultot megjelenítő képernyőkép.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Az irányítópult az összekötő állapotáról is tartalmaz információkat. Ezt az információt kiindulási pontként használhatja a kapcsolatok problémáinak elemzéséhez. További információ: [hibajelentés az irányítópult használatával](./itsmc-dashboard.md).

## <a name="use-service-map-to-visualize-incidents"></a>Az incidensek megjelenítésének Service Map használata

A Service Map érintett számítógépeken szinkronizált incidenseket is megjelenítheti.

Service Map automatikusan feltérképezi az alkalmazás összetevőit Windows-és Linux-rendszereken, és leképezi a szolgáltatások közötti kommunikációt. Lehetővé teszi, hogy a kiszolgálókat a következőképpen tekintheti meg: olyan összekapcsolt rendszerek, amelyek kritikus szolgáltatásokat biztosítanak. 

Service Map megjeleníti a kiszolgálók, a folyamatok és a portok közötti kapcsolatokat bármely TCP-kapcsolattal rendelkező architektúrán keresztül. Az ügynök telepítésén kívül nincs szükség konfigurációra. További információ: [a Service Map használata](../insights/service-map.md).

Service Map használata esetén megtekintheti az IT Service Management-(ITSM-) megoldásokban létrehozott ügyfélszolgálati elemeket az alábbi példában látható módon:

![Képernyőkép, amely a Log Analytics képernyőt jeleníti meg.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>Problémák megoldása

A következő fejezetek a gyakori tüneteket, a lehetséges okokat és a felbontásokat ismertetik. 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>A ITSM rendszerhez való csatlakozás meghiúsul, és "a kapcsolat mentésekor" hibaüzenet jelenik meg

**OK**: az ok a következő lehetőségek egyike lehet:

* A hitelesítő adatok helytelenek.
* A jogosultságok nem elegendőek.
* A webalkalmazás telepítése helytelen volt.

**Megoldás**:

* ServiceNow, Cherwell és elővance kapcsolatok esetén:
  * Győződjön meg arról, hogy minden kapcsolathoz helyesen adta meg a felhasználónevet, a jelszót, az ügyfél-azonosítót és az ügyfél-titkot.  
  * A ServiceNow győződjön meg arról, hogy megfelelő [jogosultságokkal](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role) rendelkezik a megfelelő ITSM-termékben.

* Service Manager kapcsolatok esetén:  
  * Győződjön meg arról, hogy a webalkalmazás üzembe helyezése sikeres volt, és hogy a hibrid kapcsolat létrejött. Annak ellenőrzéséhez, hogy a kapcsolat sikeresen létrejött-e a helyszíni Service Manager számítógéppel, lépjen a webalkalmazás URL-címére a [hibrid kapcsolat létrehozásához szükséges dokumentációban](./itsmc-connections-scsm.md#configure-the-hybrid-connection)leírtak szerint.  

### <a name="duplicate-work-items-are-created"></a>Ismétlődő munkaelemek jönnek létre

**OK**: az ok a következő két lehetőség egyike lehet:

* Több ITSM művelet van definiálva a riasztáshoz.
* A riasztás megoldódott.

**Megoldás**: két megoldás lehet:

* Győződjön meg arról, hogy a ITSM műveleti csoport egyetlen riasztással rendelkezik.
* A ITSMC nem támogatja a megfelelő munkahelyi elemek állapotának frissítését riasztás feloldásakor. Hozzon létre egy új megoldott munkaelemet.

### <a name="work-items-are-not-created"></a>A munkaelemek nincsenek létrehozva

**OK**: a tünet több oka is lehet:

* A kód a ServiceNow oldalon lett módosítva.
* Az engedélyek helytelenül vannak konfigurálva.
* A ServiceNow arányának korlátai túl magasak vagy túl alacsonyak.
* A frissítési jogkivonat lejárt.
* A ITSMC törölve lett.

**Megoldás**: Ellenőrizze az [irányítópultot](itsmc-dashboard.md) , és tekintse át az összekötő állapota című szakaszban található hibákat. Ezután tekintse át a [gyakori hibákat és azok](itsmc-dashboard-errors.md)megoldásait.

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>Műveleti csoporthoz nem hozható létre ITSM művelet.

**OK**: az újonnan létrehozott ITSMC-példány még befejezi a kezdeti szinkronizálást.

**Megoldás**: Tekintse át a [gyakori hibákat és azok felbontását](itsmc-dashboard-errors.md).