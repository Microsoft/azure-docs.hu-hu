---
title: Az Azure AD Connect Health használata a szinkronizálási szolgáltatással | Microsoft Docs
description: Ez az Azure AD Connect Health-oldal bemutatja az Azure AD Connect szinkronizálási szolgáltatás megfigyelését.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: e803614a02e76d179579a2258abd563b5c58e63a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016983"
---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>Az Azure AD Connect-szinkronizálás megfigyelése az Azure AD Connect Health szolgáltatással
Az alábbi dokumentáció az Azure AD Connect (szinkronizálási) szolgáltatás Azure AD Connect Health szolgáltatással végzett megfigyelésére vonatkozik.  Az AD FS az Azure AD Connect Health használatával történő megfigyelésére vonatkozó információkat lásd: [Az Azure AD Connect Health használata az AD FS szolgáltatással](how-to-connect-health-adfs.md). Az Active Directory tartományi szolgáltatások az Azure AD Connect Health használatával történő megfigyelésével kapcsolatos információkat pedig a [Using Azure AD Connect Health with AD DS](how-to-connect-health-adds.md) (Az Azure AD Connect Health használata az AD DS szolgáltatással) című témakörben találja.

![A szinkronizálási oldal Azure AD Connect Health képernyőképe.](./media/how-to-connect-health-sync/syncsnapshot.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Az Azure AD Connect Health szinkronizálási szolgáltatás riasztásai
Az Azure AD Connect Health szinkronizálási szolgáltatás riasztásai szakaszban az aktív riasztások listáját láthatja. Minden egyes riasztás tartalmazza a fontos információkat, a megoldás lépéseit, valamint a kapcsolódó dokumentációra mutató hivatkozásokat. Egy aktív vagy egy megoldott riasztás kiválasztása esetén egy új panel további információkat, a riasztás megoldását segítő lépéseket és további dokumentációkra mutató hivatkozásokat jelenít meg. A már megoldott riasztások esetében az előzményadatokat is megtekintheti.

Egy riasztás kiválasztása esetén további információk, a riasztás megoldását segítő lépések és további dokumentációkra mutató hivatkozások jelennek meg.

![Azure AD Connect szinkronizálási szolgáltatás – hiba](./media/how-to-connect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Riasztások korlátozott kiértékelése
Ha az Azure AD Connect NEM az alapértelmezett konfigurációt használja (például ha az attribútumszűrés az alapértelmezett konfigurációról egyéni konfigurációra változott), akkor az Azure AD Connect Health-ügynök nem tölti fel az Azure AD Connecttel kapcsolatos hibaeseményeket.

Ez korlátozza a riasztások kiértékelését a szolgáltatás által. Az Azure Portalban a szolgáltatás alatt ilyenkor megjelenik egy szalagcím, amely ezt az állapotot jelzi.

![Képernyőfelvétel: a riasztások kiértékelését jelző riasztási szalagcím. Frissítse a beállításokat az összes riasztás engedélyezéséhez.](./media/how-to-connect-health-sync/banner.png)

Ezt úgy módosíthatja, ha a „Beállítások” elemre kattint, és engedélyezi, hogy az Azure AD Connect Health-ügynök minden hibanaplót feltöltsön.

![Képernyőkép: a beállítások lehetőség, valamint a beállítások szakasz a Mentés lehetőséggel és a bekapcsolt lehetőséggel.](./media/how-to-connect-health-sync/banner2.png)

## <a name="sync-insight"></a>Szinkronizálási elemzés
A rendszergazdák általában tudni szeretnék, mennyi időt vesz igénybe a változások Azure AD-be való szinkronizálása, és hogy milyen mennyiségű változásokról van szó. Ezzel a funkcióval mindez az információ egyszerűen megjeleníthető az alábbi grafikonokon:   

* Szinkronizálási műveletek késleltetése
* Objektummódosítási trend

### <a name="sync-latency"></a>Szinkronizálási késések
Ez a funkció egy tendenciagrafikont hoz létre az összekötőkhöz kapcsolódó szinkronizálási műveletek (importálás, exportálás stb.) késéseiről.  Így egyrészt könnyen és gyorsan megjeleníthető a műveletek késési időmennyisége (ez nagy mennyiségű változás esetén nagyobb), másrészt lehetővé válik a késéseknél előforduló, esetlegesen további vizsgálatot igénylő rendellenességek észlelése is.

![Képernyőkép a futtatási profil késéséről az elmúlt 3 napban gráfból.](./media/how-to-connect-health-sync/synclatency02.png)

Alapértelmezés szerint csak az Azure AD-összekötő „Exportálás” műveletének késései jelennek meg.  Az összekötő további műveleteinek és más összekötők műveleteinek megtekintéséhez kattintson a jobb egérgombbal a diagramra, válassza a Diagram szerkesztése lehetőséget vagy kattintson a „Késési diagram szerkesztése” gombra, és válassza ki a kívánt műveletet és összekötőt.

### <a name="sync-object-changes"></a>Szinkronizálási objektumok módosításai
Ez a funkció egy tendenciagrafikonon jeleníti meg a kiértékelt és az Azure AD-ba exportált módosítások számát.  Napjainkban az ilyen jellegű információkat nehéz a szinkronizálási naplófájlokból összegyűjteni.  A diagram nemcsak leegyszerűsíti a környezetében bekövetkező változások számának követését, de megjeleníti az előforduló hibákat is.

![Képernyőkép az exportálási statisztikákról az Azure AD-re az elmúlt 3 napban gráfból.](./media/how-to-connect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report"></a>Objektum szintű szinkronizációs hibajelentés
Ez a funkció olyan szinkronizálási hibákról készít jelentést, amelyek a Windows Server AD és az Azure AD közötti, Azure AD Connect használatával történő identitásszinkronizálás közben jelentkeznek.

* A jelentésben a szinkronizáló ügyfél (az Azure AD Connect 1.1.281.0 vagy újabb verziói) által rögzített hibák szerepelnek
* Azokat a hibákat tartalmazza, amelyek a szinkronizálási motor legutóbbi szinkronizálási művelete során jelentkeztek. („Exportálás” az Azure AD Connectoron.)
* Ahhoz, hogy a jelentés a legfrissebb adatokat tartalmazza, az Azure AD Connect Health szinkronizálási ügynöknek a megfelelő végpontokra irányuló kimenő kapcsolattal kell rendelkeznie.
* A jelentés **30 percenként frissül** a Azure ad Connect Health-ügynök által a szinkronizáláshoz feltöltött adatszolgáltatások használatával. A következő főbb képességeket biztosítja

  * Hibák kategorizálása
  * Objektumlista kategóriánként összesített hibákkal
  * A hibákkal kapcsolatos minden adat egy helyen látható
  * Objektumok összehasonlítása ütközési hibák alapján
  * Hibajelentés letöltése CVS-ként

### <a name="categorization-of-errors"></a>Hibák kategorizálása
A jelentés az alábbi kategóriák szerint csoportosítja a jelentkező szinkronizálási hibákat:

| Kategória | Leírás |
| --- | --- |
| Duplikált attribútum |Olyan hibák, amelyek akkor jelentkeznek, amikor az Azure AD Connect megpróbál olyan objektumokat létrehozni vagy frissíteni az Azure AD-ben, amelyek egy vagy több duplikált attribútummal rendelkeznek, noha az attribútumnak (például proxyAddresses vagy UserPrincipalName) egyedinek kell lennie a bérlőben. |
| Eltérés az adatokban |Olyan hibák, amelyek akkor jelentkeznek, ha közeli egyezéssel nem azonosíthatók be a szinkronizálási hibát kiváltó objektumok. |
| Sikertelen adatérvényesítés |Érvénytelen adatok miatt jelentkező hibák, például nem támogatott karakterek használata kritikus fontosságú attribútumnevekben (például UserPrincipalName), vagy formátumbeli hibák, amelyek sikertelen érvényesítést eredményeznek az Azure AD-be való írás előtt. |
| Összevont tartomány módosítása | Hiba lép fel, ha a fiókok különböző összevont tartományt használnak. |
| Túl nagy attribútum |A hiba akkor jelentkezik, ha egy vagy több attribútum mérete, hosszúsága vagy száma meghaladja a megengedett mértéket. |
| Egyéb |A fenti kategóriákba nem illő bármely más hiba. A visszajelzések függvényében ez a kategória további alkategóriákra lesz bontva. |

![Szinkronizálási hibajelentés – összefoglalás](./media/how-to-connect-health-sync/errorreport01.png)
![Szinkronizálási hiba – kategóriák](./media/how-to-connect-health-sync/SyncErrorByTypes.PNG)

### <a name="list-of-objects-with-error-per-category"></a>Objektumlista kategóriánként összesített hibákkal
Az egyes kategóriák részletes nézetével megjeleníthető azoknak az objektumoknak a listája, amelyeket az adott hiba érint.
![Szinkronizálási hibajelentés – lista](./media/how-to-connect-health-sync/errorreport03.png)

### <a name="error-details"></a>A hiba adatai
Az alábbi adatok az egyes hibák részletes nézetében érhetőek el

* Kiemelt ütköző attribútum
* Az érintett *AD-objektum* azonosítói
* Az érintett *Azure AD-objektum* azonosítói (ha létezik)
* Hiba leírása és az elhárítás módja

![Szinkronizálási hibajelentés – részletes nézet](./media/how-to-connect-health-sync/duplicateAttributeSyncError.png)

### <a name="download-the-error-report-as-csv"></a>Hibajelentés letöltése CSV-fájlként
Az Exportálás gomb kiválasztásával letölthet egy CSV-fájlt, amely tartalmazza az összes hiba részleteit.

### <a name="diagnose-and-remediate-sync-errors"></a>Szinkronizálási hibák diagnosztizálása és javítása 
Bizonyos duplikált attribútumok felhasználói forráshorgony-frissítést is érintő szinkronizálási hibái esetén a hibákat javíthatja közvetlenül a portálról. További információ a [duplikált attribútumok szinkronizálási hibáinak diagnosztizálásáról és javításáról](how-to-connect-health-diagnose-sync-errors.md)

## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Hibaelhárítási hibák a szinkronizálás során](tshoot-connect-sync-errors.md)
* [Duplikált attribútummal kapcsolatos rugalmasság](how-to-connect-syncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Az Azure AD Connect Health-ügynök telepítése](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health Operations (Az Azure AD Connect Health műveletei)](how-to-connect-health-operations.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](how-to-connect-health-adfs.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](how-to-connect-health-adds.md)
* [Azure AD Connect Health FAQ (Azure AD Connect Health – gyakori kérdések)](reference-connect-health-faq.md)
* [Azure AD Connect Health korábbi verziók](reference-connect-health-version-history.md)