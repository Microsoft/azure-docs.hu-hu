---
title: Mi az Azure Service Health Notifications?
description: A szolgáltatás állapotával kapcsolatos értesítések segítségével megtekintheti Microsoft Azure által közzétett szolgáltatás-egészségügyi üzeneteket.
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: 4f2b4a0779d775ed5be5bfa28831cccc68c33caf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86529012"
---
# <a name="use-the-azure-portal-to-view-service-health-notifications"></a>A szolgáltatás állapotával kapcsolatos értesítések megtekintése a Azure Portal használatával

A szolgáltatás állapotával kapcsolatos értesítéseket az Azure közzétette, és az előfizetésében található erőforrásokra vonatkozó információkat tartalmaz. Ezek az értesítések a műveletnapló eseményeinek alosztálya, és a tevékenység naplójában is megtalálhatók. A szolgáltatás állapotával kapcsolatos értesítések az osztálytól függően tájékoztató vagy végrehajthatóak lehetnek.

A Service Health értesítéseinek különböző osztályai vannak:  

- **Szükséges művelet:** Előfordulhat, hogy az Azure valami szokatlant észlelt a fiókjával kapcsolatban, és ezzel segít Önnek a megoldásában. Az Azure értesítést küld Önnek, vagy részletezi az Azure Engineering vagy support szolgáltatással való kapcsolatfelvételhez szükséges műveleteket.  
- **Incidens:** Egy esemény, amely hatással van a szolgáltatásra, jelenleg egy vagy több erőforrást érint az előfizetésében.  
- **Karbantartás:** Egy tervezett karbantartási tevékenység, amely hatással lehet egy vagy több erőforrásra az előfizetés alatt.  
- **Információ:** Lehetséges optimalizálások, amelyek segíthetnek az erőforrások használatának javításában. 
- **Biztonság:** Sürgős biztonsággal kapcsolatos információk az Azure-on futó megoldásokkal kapcsolatban.

Az egyes szolgáltatások állapotáról szóló értesítések a hatókörről és az erőforrások hatásáról tartalmaznak részleteket. A részletek a következők:

Tulajdonság neve | Description
-------- | -----------
csatornák | A következő értékek egyike: **rendszergazda** vagy **művelet**.
correlationId | Általában egy GUID formátumú karakterlánc. Az ugyanahhoz a művelethez tartozó események általában ugyanazt a correlationId használják.
eventDataId | Egy esemény egyedi azonosítója.
eventName | Egy esemény címe.
szint | Egy esemény szintje
resourceProviderName | Az érintett erőforráshoz tartozó erőforrás-szolgáltató neve.
resourceType| Az érintett erőforrás erőforrás-típusa.
Részállapot | Általában a megfelelő REST-hívás HTTP-állapotkód, de tartalmazhat más, alállapotot leíró karakterláncokat is. Például: OK (HTTP-állapotkód: 200), létrehozva (HTTP-állapotkód: 201), elfogadva (HTTP-állapotkód: 202), nincs tartalom (HTTP-állapotkód: 204), hibás kérés (HTTP-állapotkód: 400), nem található (HTTP-állapotkód: 404), ütközés (HTTP-állapotkód: 409), belső kiszolgálóhiba (HTTP-állapotkód: 500), a szolgáltatás nem érhető el (HTTP-állapotkód: 503) és az átjáró időtúllépése (http-állapotkód: 504).
eventTimestamp | Időbélyeg, amikor az Azure-szolgáltatás létrehozta az eseményt az eseménynek megfelelő kérelem feldolgozásakor.
submissionTimestamp | Időbélyeg, ha az esemény elérhetővé válik a lekérdezéshez.
subscriptionId | Az Azure-előfizetés, amelyben az esemény naplózva lett.
status | A művelet állapotát leíró karakterlánc. Néhány gyakori érték: **elindítva**, **folyamatban**, **sikeres**, **sikertelen**, **aktív** és **megoldott**.
operationName | A művelet neve.
category | Ez a tulajdonság mindig **ServiceHealth**.
resourceId | Az érintett erőforrás erőforrás-azonosítója.
Tulajdonságok. cím | A kommunikáció honosított címe. Az alapértelmezett érték az angol.
Tulajdonságok. kommunikáció | A HTML-jelöléssel folytatott kommunikáció honosított adatai. Az alapértelmezett érték az angol.
Properties. incidentType | A következő értékek egyike: **beavatkozás szükséges**, **tájékoztató**, **incidens**, **karbantartás** vagy **Biztonság**.
Properties. trackingId | Az az incidens, amellyel ez az esemény társítva van. Ezzel a művelettel korrelálhatja az incidensekhez kapcsolódó eseményeket.
Properties. impactedServices | Egy Escape-JSON-blob, amely leírja az incidens által érintett szolgáltatásokat és régiókat. A tulajdonság tartalmazza azon szolgáltatások listáját, amelyek mindegyike **szolgáltatásnév**, valamint az érintett régiók listája, amelyek mindegyike rendelkezik **RegionName**.
Properties. defaultLanguageTitle | Az angol nyelvű kommunikáció.
Properties. defaultLanguageContent | Az angol nyelvű kommunikáció HTML-kódban vagy egyszerű szövegként.
Tulajdonságok. fázis | Az **incidens** lehetséges értékei, valamint a **Biztonság** **aktív,** **megoldott** vagy **RCA**. A **beavatkozás szükséges** vagy a **tájékoztatási** szolgáltatás esetében az egyetlen érték **aktív.** A **karbantartáshoz** a következők tartoznak: **aktív**, **tervezett**, **befejezetlen**, **megszakított**, **újraütemezett**, **megoldott** vagy **befejezett**.
Properties. communicationId | Az a kommunikáció, amellyel ez az esemény társítva van.

### <a name="details-on-service-health-level-information"></a>A szolgáltatás állapotára vonatkozó információk részletei

**Szükséges művelet** (Properties. incidentType = = beavatkozás szükséges)
- Tájékoztatás – rendszergazdai beavatkozás szükséges a meglévő szolgáltatások hatásának megelőzése érdekében.
    
**Karbantartás** (Properties. incidentType = = karbantartás)
- Figyelmeztetés – vészhelyzeti karbantartás
- Tájékoztató – szabványos tervezett karbantartás

**Információ** (tulajdonságok. incidentType = = információ)
- Tájékoztatás – a rendszergazdának szükség lehet a meglévő szolgáltatások hatásának megelőzésére.

**Biztonság** (Properties. incidentType = = biztonság)
- Figyelmeztetés – biztonsági tanácsadó, amely hatással van a meglévő szolgáltatásokra, és rendszergazdai beavatkozást igényelhet.
- Tájékoztató – biztonsági tanácsadó, amely hatással van a meglévő szolgáltatásokra.

**Szolgáltatási problémák** (Properties. incidentType = = incidens)
- Hiba – a több régióban több szolgáltatáshoz való hozzáférés általános problémái nagy mennyiségű ügyfelet érintenek.
- Figyelmeztetés – az egyes szolgáltatásokhoz és/vagy meghatározott régióokhoz való hozzáférésre vonatkozó problémák az ügyfelek egy részhalmazát érintik.
- Tájékoztató – a szolgáltatás rendelkezésre állását nem befolyásoló felügyeleti műveleteket és/vagy késést érintő problémák.
