---
title: Az ügyféladatok védelme az Azure-ban
description: Ismerje meg, hogyan védi az Azure az ügyféladatokat az adatelkülönítés, az adatredundancia és az adatmegsemmisítés révén.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2020
ms.author: terrylan
ms.openlocfilehash: 14589e4efe22d89468b069bf6ff7e3d9babcc714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87543792"
---
# <a name="azure-customer-data-protection"></a>Azure Customer-adatvédelem   
A Microsoft működési és támogatási személyzete alapértelmezés szerint nem férhet hozzá az ügyféladatok eléréséhez. Ha a támogatási esethez kapcsolódó adatokhoz fér hozzá, a rendszer csak egy igény szerinti (JIT) modellt kap, melyet a rendszer a megfelelőségi és adatvédelmi szabályzatok alapján auditált és ellenőrzött házirendeket használ.  A hozzáférés-vezérlésre vonatkozó követelményeket a következő Azure biztonsági szabályzat határozza meg:

- Alapértelmezés szerint nincs hozzáférése az ügyféladatok eléréséhez.
- Nincs felhasználói vagy rendszergazdai fiók az ügyfél virtuális gépei (VM-EK) számára.
- Adja meg a feladat végrehajtásához szükséges legalacsonyabb jogosultságot; a hozzáférési kérelmek naplózása és naplózása.

Az Azure-támogatási munkatársak a Microsoft egyedi vállalati Active Directory fiókokat rendelnek hozzá. Az Azure a Microsoft Information Technology (MSIT) által felügyelt Microsoft vállalati Active Directoryra támaszkodik a kulcsfontosságú információs rendszerekhez való hozzáférés szabályozása érdekében. A többtényezős hitelesítésre van szükség, és a hozzáférés csak biztonságos konzolokról adható meg.

A rendszer az összes hozzáférési kísérletet figyeli, és a jelentések alapkészlete segítségével jeleníthető meg.

## <a name="data-protection"></a>Adatvédelem
Az Azure alapértelmezés szerint és a vásárlói beállítások alapján erős adatbiztonságot biztosít az ügyfeleknek.

**Adatelkülönítés**: az Azure egy több-bérlős szolgáltatás, ami azt jelenti, hogy a több ügyfél-telepítés és virtuális gép ugyanazon a fizikai hardveren van tárolva. Az Azure logikai elkülönítést használ, hogy elkülönítse az egyes ügyfelek adatait a többitől. Az elkülönítés biztosítja a több-bérlős szolgáltatások skálázhatóságát és gazdasági előnyeit, miközben szigorúan megakadályozza, hogy az ügyfelek hozzáférjenek egymáshoz.

**Rest-** alapú adatvédelem: az ügyfelek felelősek annak biztosításáért, hogy az Azure-ban tárolt adatok a szabványoknak megfelelően titkosítva legyenek. Az Azure számos titkosítási képességet kínál, így az ügyfelek rugalmasan választhatják ki az igényeiknek leginkább megfelelő megoldást. Azure Key Vault segítségével az ügyfelek egyszerűen kezelhetik a felhőalapú alkalmazások és szolgáltatások által az adattitkosításhoz használt kulcsok felügyeletét. Azure Disk Encryption lehetővé teszi az ügyfeleknek a virtuális gépek titkosítását. Az Azure Storage Service Encryption lehetővé teszi az ügyfél Storage-fiókjába helyezett összes érték titkosítását.

Forgalomban **lévő adatvédelem**: a Microsoft számos olyan lehetőséget biztosít, amelyet az ügyfelek az Azure-hálózaton belül, az interneten kívülről a végfelhasználók felé irányuló adatforgalom biztonságossá tételére használhatnak.  Ezek közé tartoznak a virtuális magánhálózatok (IPsec/IKE-titkosítást használó), a Transport Layer Security (TLS) 1,2-es vagy újabb (az Azure-összetevők, például a Application Gateway vagy az Azure bejárati ajtó), a protokollok közvetlenül az Azure-beli virtuális gépeken (például Windows IPsec vagy SMB) keresztül történő kommunikáció. 

Emellett az Azure-adatközpontok közötti MACsec (az adatkapcsolati rétegben található IEEE standard) használatával az összes Azure-forgalom számára engedélyezve van az adatok titkosítása és integritásának biztosítása. 

**Adatredundancia**: a Microsoft segít biztosítani az adatvédelmet, ha az adatközpont cyberattack vagy fizikai károsodást okoz. Az ügyfelek a következőket dönthetik el:

- A megfelelőségi vagy késési megfontolásokhoz tartozó, országon belüli/régión belüli tárolás.
- Az országon kívüli vagy a régión kívüli tárterület biztonsági vagy vész-helyreállítási célokra.

Az adatok a kijelölt földrajzi területen a redundancia érdekében replikálhatók, de nem továbbíthatók rajta kívülre. Az ügyfelek több lehetőséget is biztosítanak az adatreplikálásra, beleértve a példányszámot és a replikációs adatközpontok számát és helyét.

A Storage-fiók létrehozásakor válasszon a következő replikációs lehetőségek közül:

- **Helyileg redundáns tárolás (LRS)**: a helyileg redundáns tárolás az adatai három másolatát tárolja. A rendszer egy régió egyetlen létesítményén belül háromszor replikálja az LRS-t. A LRS megvédi adatait a normál hardveres hibáktól, de egyetlen létesítmény meghibásodása esetén sem.
- **Zone-redundáns tárolás (ZRS)**: a Zone-redundáns tárterület az adatai három másolatát tárolja. A ZRS három alkalommal replikálódik két-három létesítményre, így magasabb tartósságot biztosítanak, mint a LRS. A replikáció egyetlen régióban vagy két régión belül történik. A ZRS segítségével biztosítható, hogy az adatai tartósak legyenek egyetlen régión belül.
- **Geo-redundáns tárolás (GRS)**: a Geo-redundáns tárterület alapértelmezés szerint engedélyezve van a Storage-fiókhoz a létrehozásakor. A GRS hat másolatot tart fenn adatairól. A GRS esetében az adatai háromszor replikálódnak az elsődleges régión belül. Az adatok a legmagasabb szintű tartósságot biztosító másodlagos régióban háromszor is replikálódnak az elsődleges régióból származó több száz kilométerre. Az elsődleges régió meghibásodása esetén az Azure Storage feladatátvételt hajt végre a másodlagos régióba. A GRS segítségével biztosítható, hogy az adatai két külön régióban legyenek tartósak.

**Adatmegsemmisítés**: Ha az ügyfelek az adattörlést vagy az Azure-t hagyják el, a Microsoft szigorú szabványokat követ a tárolási erőforrások újbóli újraírása előtt, valamint a leszerelt hardverek fizikai megsemmisítését. A Microsoft az ügyfelek kérelmére és a szerződés megszűnésére vonatkozó teljes törlést hajt végre.

## <a name="customer-data-ownership"></a>Vásárlói adattulajdonos
A Microsoft nem ellenőrzi, hagyja jóvá vagy figyeli az ügyfelek által az Azure-ba üzembe helyezett alkalmazásokat. Emellett a Microsoft nem tudja, hogy milyen típusú ügyfeleket szeretne tárolni az Azure-ban. A Microsoft nem követeli meg az adatok tulajdonjogát az Azure-ban megadott vásárlói adatok felett.

## <a name="records-management"></a>Rekordok kezelése
Az Azure belső rekordokat létesített – a háttérbeli adatok megőrzési követelményeit. Az ügyfelek feladata, hogy azonosítsák a saját rekordok megőrzésének követelményeit. Az Azure-ban tárolt rekordok esetében az ügyfelek felelősek az adatok kinyeréséhez és az Azure-on kívüli tartalmak megőrzéséhez az ügyfél által megadott megőrzési időszakra vonatkozóan.

Az Azure lehetővé teszi, hogy az ügyfelek exportálják az adatok és a naplózási jelentéseket a termékből. Az exportálások helyileg tárolódnak, hogy megőrizze az adatokat egy ügyfél által meghatározott megőrzési időtartamra vonatkozóan.

## <a name="electronic-discovery-e-discovery"></a>Elektronikus felderítés (e-Discovery)
Az Azure-ügyfelek feladata az e-felderítési követelmények teljesítése az Azure-szolgáltatások használata során. Ha az Azure-ügyfeleknek meg kell őrizniük az ügyféladatokat, akkor helyileg is exportálhatunk és menthetik az adatfájlokat. Emellett az ügyfelek az Azure ügyfélszolgálati részlegében is kérhetik az adatok exportálását. Amellett, hogy lehetővé teszi az ügyfelek számára az adatok exportálását, az Azure kiterjedt naplózást és figyelést végez belsőleg.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni arról, hogy a Microsoft Hogyan védi az Azure-infrastruktúrát, olvassa el a következő témakört:

- [Azure-létesítmények,-telephelyek és fizikai biztonság](physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Azure Information System-összetevők és-határok](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure-beli üzemi hálózat](production-network.md)
- [Azure SQL Database biztonsági funkciók](infrastructure-sql.md)
- [Azure-beli üzemi műveletek és felügyelet](infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](infrastructure-integrity.md)
