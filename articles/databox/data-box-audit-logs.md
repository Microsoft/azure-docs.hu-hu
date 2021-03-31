---
title: Naplók a Azure Data Boxhoz, Azure Data Box Heavy események | Microsoft Docs
description: A Azure Data Box és Azure Data Box Heavy sorrend különböző szakaszaiban összegyűjtött Data Box teljes naplófájljait ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86209682"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Naplófájlok a Azure Data Box és Azure Data Box Heavy

A naplók nem módosítható, az időközben megjelenő diszkrét események időbélyeggel ellátott rekordjai. A naplók diagnosztikai, naplózási és biztonsági adatokat tartalmaznak az eszközről.  

Egy Data Box vagy Data Box Heavy megrendelés a művelet során a következő lépéseken halad végig: megrendelés, beállítás, Adatmásolás, visszatérés, feltöltés az Azure-ba, ellenőrzés és adatok törlése. Minden egyes lépésnél az összes eseményt naplózza és naplózza.

Ez a cikk a Data Box naplók információit tartalmazza, beleértve a naplók típusát és a gyűjtött információkat, valamint a naplók helyét. 

A cikkben található információk a következőkre vonatkoznak: Data Box és Data Box Heavy. A következő szakaszban a Data Boxra való hivatkozás a Data Box Heavyra is vonatkozik. Az Azure-ban futó Data Box szolgáltatásból gyűjtött naplók ebben a cikkben nem szerepelnek. 


## <a name="about-audit-logs"></a>A naplók ismertetése 

A Data Box a következő naplókat gyűjti össze:

- **Rendszernaplók** – Data Box, hogy Windows-alapú eszköz, az összes hardver-, szoftver-és rendszeresemény naplózva lesz. Ezeket az eseményeket a rendszer a rendszernaplókban gyűjti be és jelenti. 

- **Biztonság** – Data Box egy Windows-alapú eszköz, az összes biztonsági eseményt naplózza a rendszer. Ezeknek az eseményeknek a készletét a rendszer a biztonsági naplókban gyűjti össze és jelenti. 

- **Alkalmazás** – ezek a naplók csak Data boxra vonatkoznak. Ezek a naplók az eszközön létrehozott összes eseményt tartalmazzák a Data Box szolgáltatásokra adott válaszként.

Ezeket a naplókat a következő szakaszban tárgyaljuk.

### <a name="system-logs"></a>Rendszernaplók

A rendszer a következő rendszernapló-eseménynaplókat gyűjti be rendszernaplóként a Data Boxján:

|Esemény szolgáltatójának neve     |Begyűjtött eseményazonosító   |Esemény leírása   |
|-------------------|----------|----------------|
|Microsoft-Windows-kernel-általános|12  |Az operációs rendszer újraindításakor megjelenő UTC-idő.   |
|                                |13  |Az operációs rendszer leállításakor UTC idő. |
|    |                              |
|Microsoft-Windows-kernel-Power  |41  |A rendszer újraindult tiszta leállítás nélkül.| 
|    |                              |
|Microsoft-Windows-BitLocker-Driver|Mind|    |

### <a name="security-logs"></a>Biztonsági naplók

A rendszer a következő biztonsági naplózási események azonosítóit gyűjti biztonsági naplókként a Data Boxján:

|Esemény szolgáltatójának neve                   |Begyűjtött eseményazonosító    |Esemény leírása       |
|--------------------------------------|------------|----------|
|Microsoft-Windows-Security-audit   |4624        |Sikeres bejelentkezés. |
|                                      |4625        |Egy fiók bejelentkezése sikertelen. Ismeretlen Felhasználónév vagy helytelen jelszó. |
|                                     

### <a name="application-logs"></a>Alkalmazás-naplók

A következő alkalmazásnapló-események azonosítóit a rendszer a csomag naplófájljainak részeként gyűjti a Data Box.     

- **Microsoft-Azure-DataBox-Oobe-audit** -a helyi felhasználói felületen előforduló eseményeket tartalmazza. 
- **Microsoft-Azure-DataBox – Újraépítés – naplózás** – a Data Box eszköz újralétesítésével kapcsolatos eseményeket tartalmazza. A Data Box újraépítése akkor következik be, amikor az eszköz alaphelyzetbe áll a helyi felhasználói felületen keresztül. Ezt a beállítást akkor válassza, ha a meglévő megosztások eltávolításával és a megosztások újbóli létrehozásával vagy az eszköz alaphelyzetbe állításával szeretné törölni a másolt adatmennyiséget.
- **Microsoft-Azure-DataBox-HcsMgmt-audit** -a csak a **szállítás előkészítése** lépéshez kapcsolódó eseményeket tartalmazza, mielőtt az eszközt visszaszállították az Azure-adatközpontba. 
- **Microsoft-Azure-DataBox-IfxAudit** -a termék különböző entitásai által naplózott üzeneteket tartalmazza a feladatokról, naplókat, amelyek további információkat jeleznek arról, hogy mi történik a folyamatokban.

Itt látható egy táblázat, amely összegzi a különböző esemény-szolgáltatókat és az egyes esetekben gyűjtött megfelelő eseményazonosítóket.

|Esemény szolgáltatójának neve    |Eseményazonosító    | Jegyzetek |
|-----------------|-----------------|-------------------|
|Microsoft-Azure-DataBox-OOBE-audit |4624        |Sikeres bejelentkezés.|
|                                      |4625        |Egy fiók bejelentkezése sikertelen. Ismeretlen Felhasználónév vagy helytelen jelszó.|
|                                     |4634        |Kijelentkezési esemény.|
|                                   |  | |
|Microsoft-Azure-DataBox – Újraépítés – naplózás    |65001       |Sikeres újralétesítési esemény.|
|                                                  |65002       |Nem sikerült újraépíteni az eseményt.|
|                                                  |                 |         |
|Microsoft-Azure-DataBox-HcsMgmt-audit        |65003       |Szállításra való előkészítés állapot eseményének NotStarted, Inprogress, sikertelen, megszakított, sikeres, ScanCompletedWithIssues, SucceededWithWarnings          |
|                                                  |                 |     |
|Microsoft-Azure-DataBox-IfxAudit    |Mind |A rendszer minden eseményt naplóz a napló API-val a kódban |

Íme egy példa a Instrumentation Framework (IFX) naplóra:

|     Feladat/feladat/API                              |     Naplózott események                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     Felesleges tartalmak törlése                                   |     A rendszer naplózza a karbantartási feladatok indításával, befejezésével vagy meghibásodásával kapcsolatos eseményeket. |                                              
|     Eszköz előkészítése az ügyfél-szállítólevélhez    |     Az eszköz szállításra való előkészítéséhez szükséges indítási, befejezési vagy meghibásodási események naplózása történik. |
|     Üzembe helyezés                                 |     A rendszer naplózza az eszköz kiépítési feladatának indításával, befejezésével vagy meghibásodásával kapcsolatos eseményeket.|
|     Felügyeleti csomag feladata                       |     A naplózási naplók létrehozásával, befejezésével vagy meghibásodásával kapcsolatos események naplózva vannak.|
|     Lemez felülírása                          |     A lemez felülírásának meghiúsulása naplózva.|
|     A távoli PowerShell engedélyezése vagy letiltása     |     Az eszközön a távoli PowerShell engedélyezésével vagy letiltásával kapcsolatos események naplózása történik. |
|     Telepítési fázis részleteinek beolvasása               |     Az eszközön a szoftvernek a fázisokban való telepítésével kapcsolatos események az Azure-adatközpontban lesznek naplózva.|
|     BitLocker-kötet zárolásának feloldása vagy zárolása           |     A rendszer naplózza az eseményeket a *basevolume* és a *hcsdata* kötet BitLocker-állapotának jelzéséhez.|
|     Lemez tisztítása                              |     A nem törölhető fizikai lemezek meghibásodásával kapcsolatos események, valamint az eszközön lévő összes fizikai lemez sikeres törléséhez szükséges események naplózása történik. |
|     Helyi felhasználó engedélyezése vagy letiltása               |     A rendszer naplózza a StorSimpleAdmin és a PodSupportAdminUser helyi felhasználói fiókjainak engedélyezésével vagy letiltásával kapcsolatos eseményeket.| 
|     Jelszó alaphelyzetbe állítása                          |     A rendszer a helyi StorSimpleAdmin-felhasználó sikeres vagy sikertelen jelszavas visszaállításával kapcsolatos eseményeket naplózza. |


A IFX-naplók mellett a felügyeleti naplók lánca is bekerül Data Box. Ezeket a naplókat nem lehet valós időben megtekinteni, de csak a feladatok befejeződése után, és az adatok törlődnek a Data Box lemezekről. Ezek a naplók a IFX-naplókban található információk egy részhalmazát tartalmazzák.

További információ a felügyeleti naplók láncáról: [adattörlési lánc beolvasása az adatok törlése után](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure).

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>Hozzáférés az auditnaplókhoz

Ezek a naplók az Azure-ban tárolódnak, és nem érhetők el közvetlenül. Ha el kell érnie ezeket a naplókat, a támogatási jegyet. További információ: [Contact Microsoft ügyfélszolgálata](data-box-disk-contact-microsoft-support.md). 

A támogatási jegy benyújtása után a Microsoft letölti és megadja a hozzáférést ezekhez a naplókhoz.


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogy miként lehet [elhárítani a Data Box és Data Box Heavy kapcsolatos problémákat](data-box-troubleshoot.md).
