---
title: Az adatvédelem áttekintése
titleSuffix: Azure Storage
description: Az Blob Storage számára elérhető adatvédelmi lehetőségek és Azure Data Lake Storage Gen2 adatok lehetővé teszik az adatok törlését vagy felülírását. Ha a törölt vagy felülírt adatokat kell helyreállítani, ez az útmutató segítséget nyújt a forgatókönyvhöz legmegfelelőbb helyreállítási lehetőség kiválasztásában.
services: storage
author: tamram
ms.service: storage
ms.date: 04/09/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: prishet
ms.subservice: common
ms.openlocfilehash: 90c83397089b77d30694041a37debc0731ea2a38
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304256"
---
# <a name="data-protection-overview"></a>Az adatvédelem áttekintése

Az Azure Storage Blob Storage és Azure Data Lake Storage Gen2 adatvédelmet biztosít az olyan forgatókönyvek előkészítéséhez, amelyekben törölt vagy felülírt adatokat kell helyreállítani. Fontos, hogy meggondolja, hogyan lehet a legjobban védelemmel ellátni az adatait, mielőtt olyan incidens következik be, amely veszélyeztetheti azt. Ez az útmutató segít eldönteni, hogy milyen adatvédelmi funkciókra van szükség a forgatókönyvben, és hogyan valósítható meg. Ha a törölt vagy felülírt adatokat kell helyreállítani, ez az Áttekintés útmutatást nyújt a folytatáshoz is, a forgatókönyv alapján.

Az Azure Storage dokumentációjában az *Adatvédelem* olyan stratégiákra utal, amelyek a Storage-fiók és az adatok törlését vagy módosítását, illetve az adatok törlését vagy módosítását követően az adatok visszaállítását végzik. Az Azure Storage a vész- *helyreállítási* lehetőségeket is kínálja, beleértve többek között a redundancia lehetőségét is, hogy a szolgáltatásból származó adatok védelme a hardverhiba vagy a természeti katasztrófák miatt, valamint az ügyfél által felügyelt feladatátvétel abban az esetben, ha az elsődleges régióban lévő adatközpont elérhetetlenné válik. További információ arról, hogy az adatok hogyan vannak védve a szolgáltatás KIMARADÁSÁVAL kapcsolatban: vész- [helyreállítás](#disaster-recovery).

## <a name="recommendations-for-basic-data-protection"></a>Javaslatok az alapszintű adatvédelem terén

Ha a Storage-fiók és a benne található adatok alapszintű adatvédelmi lefedettségét keresi, a Microsoft a következő lépések elvégzését javasolja a kezdéshez:

- Konfiguráljon egy Azure Resource Manager zárolást a Storage-fiókon, hogy megvédje a fiókot a törlés vagy a konfiguráció változásairól. [tudj meg többet...](../common/lock-account-resource.md)
- A tárolók törlésének engedélyezése a Storage-fiók számára a törölt tárolók és annak tartalmának helyreállításához. [tudj meg többet...](soft-delete-container-enable.md)
- BLOB állapotának mentése rendszeres időközönként:
  - Blob Storage számítási feladatokhoz engedélyezze a blob verziószámozását, hogy minden alkalommal automatikusan mentse az adatai állapotát, amikor egy blob felülíródik. [tudj meg többet...](versioning-enable.md)
  - Azure Data Lake Storage munkaterhelések esetén manuális pillanatképeket készíthet az adatok állapotának mentéséhez egy adott időpontban. [tudj meg többet...](snapshots-overview.md)

A következő szakasz részletesebben ismerteti ezeket a beállításokat, valamint az egyéb forgatókönyvekhez kapcsolódó további adatvédelmi lehetőségeket is.

A funkciókkal kapcsolatos költségek áttekintését lásd: a [költségekkel kapcsolatos megfontolások összefoglalása](#summary-of-cost-considerations).

## <a name="overview-of-data-protection-options"></a>Adatvédelmi beállítások áttekintése

A következő táblázat összefoglalja az Azure Storage-ban elérhető lehetőségeket a gyakori adatvédelmi forgatókönyvek esetében. Válassza ki a helyzetekre vonatkozó forgatókönyveket, hogy többet tudjon meg az Ön számára elérhető lehetőségekről. Vegye figyelembe, hogy jelenleg nem minden szolgáltatás érhető el, ha a Storage-fiókok esetében engedélyezve van a hierarchikus névtér.

| Eset | Adatvédelmi beállítás | Javaslatok | Védelmi juttatás | Elérhető Data Lake Storage |
|--|--|--|--|--|
| A Storage-fiók törlésének vagy módosításának megakadályozása. | Azure Resource Manager zárolása<br />[tudj meg többet...](../common/lock-account-resource.md) | Zárolja az összes Storage-fiókot egy Azure Resource Manager zárolással, hogy megakadályozza a Storage-fiók törlését. | Védi a Storage-fiókot a törlés vagy a konfigurációs változások ellen.<br /><br />A nem biztosítja a fiókban lévő tárolók vagy Blobok törlését vagy felülírását. | Yes |
| A tárolók és a Blobok törlésének és módosításának megakadályozása egy Ön által vezérelt intervallumban. | Módosíthatatlansági szabályzata egy tárolón<br />[tudj meg többet...](storage-blob-immutable-storage.md) | Állítson be egy módosíthatatlansági házirendet egy tárolón az üzleti szempontból fontos dokumentumok biztosításához, például a jogi vagy szabályozási megfelelőségi követelmények kielégítése érdekében. | Védelmet biztosít a tárolók és a Blobok számára az összes törlési és felülírási műveletből.<br /><br />Ha jogi vagy zárolt időalapú adatmegőrzési házirend van érvényben, a Storage-fiók is védelmet biztosít a törléstől. Azok a tárolók, amelyekhez nincs beállítva módosíthatatlansági házirend, nem védettek a törléssel. | Igen, előzetes verzióban |
| Törölt tároló visszaállítása egy megadott intervallumon belül. | Tároló – Soft Delete (előzetes verzió)<br />[tudj meg többet...](soft-delete-container-overview.md) | Az összes Storage-fiók esetében engedélyezze a tárolók törlését, a minimális megőrzési időtartam pedig 7 nap.<br /><br />A Blobok verziószámozásának és a Blobok törlésének engedélyezése a tárolók törlésével együtt a tárolóban lévő egyes Blobok számára.<br /><br />Tárolja a különböző megőrzési időszakokat igénylő tárolókat külön Storage-fiókokban. | A törölt tárolók és annak tartalma visszaállítható a megőrzési időtartamon belül.<br /><br />Csak a tároló szintű műveletek (például a [törlési tároló](/rest/api/storageservices/delete-container)) állíthatók vissza. A tároló-helyreállító törlés nem teszi lehetővé, hogy a tárolóban lévő egyéni blobokat ne állítsa vissza, ha a blob törölve lett. | Igen, előzetes verzióban |
| Egy blob állapotának automatikus mentése egy korábbi verzióban, ha a fájl felül van írva. | BLOB verziószámozása<br />[tudj meg többet...](versioning-overview.md) | A Blobok verziószámozásának engedélyezése, a tárolók helyreállított törlésével és a Blobok törlésével együtt, olyan tárolási fiókok esetében, amelyeknél a blob-adatvédelemhez optimális védelmet kell biztosítani.<br /><br />A költségek korlátozása érdekében tárolja azokat a blob-adattárakat, amelyek nem igényelnek verziószámozást külön fiókban. | Minden blob írási művelet létrehoz egy új verziót. Előfordulhat, hogy a blob aktuális verziója visszaállítható egy korábbi verzióról, ha az aktuális verziót törölték vagy felülírják. | No |
| Törölt blob vagy blob-verzió visszaállítása egy megadott intervallumon belül. | Blobos törlés<br />[tudj meg többet...](soft-delete-blob-overview.md) | Az összes Storage-fiók esetében engedélyezze a Blobok törlését, a minimális megőrzési időtartam pedig 7 nap.<br /><br />Engedélyezze a Blobok verziószámozását és a tárolók helyreállított törlését a blob-adattörlési szolgáltatással a Blobok optimális védelméhez.<br /><br />Tárolja a különböző megőrzési időszakokat igénylő blobokat különálló Storage-fiókokban. | Lehet, hogy a törölt blob vagy blob verziója visszaállítható a megőrzési időn belül. | No |
| Blokkos Blobok egy készletének visszaállítása egy korábbi időpontra. | Adott időpontnak megfelelő helyreállítás<br />[tudj meg többet...](point-in-time-restore-overview.md) | Ha az időponthoz tartozó visszaállítást egy korábbi állapotra kívánja használni, tervezze meg az alkalmazást, hogy törölje az egyes blokk-blobokat a tárolók törlése helyett. | A blokkos Blobok egy készlete a múltban megadott időpontban visszaállítható az állapotukra.<br /><br />Csak a blokk blobokon végrehajtott műveletek visszaállítása történik meg. A tárolók, a lapok Blobok vagy a hozzáfűzési Blobok által végrehajtott műveletek nem állnak újra. | No |
| Egy blob állapotának manuális mentése egy adott időpontban. | BLOB pillanatképe<br />[tudj meg többet...](snapshots-overview.md) | A blob verziószámozás alternatívájaként ajánlott, ha a verziószámozás nem megfelelő a forgatókönyvhöz, a költségeket vagy egyéb szempontokat, illetve ha a Storage-fiókhoz engedélyezve van egy hierarchikus névtér. | Előfordulhat, hogy egy blobot egy pillanatképből lehet visszaállítani, ha a blob felülíródik. Ha a blob törölve lett, a pillanatképek is törlődnek. | Igen, előzetes verzióban |
| A Blobok törölhetők vagy írhatók, de a rendszer rendszeresen átmásolja azokat egy második Storage-fiókba. | Az Azure Storage objektum-replikációval vagy a AzCopy vagy a Azure Data Factory eszközzel az adatok egy második fiókba való másolásához a saját megoldását használhatja. | A nem várt szándékos műveletekkel, illetve előre nem látható forgatókönyvekkel szembeni béketámogató védelemhez ajánlott.<br /><br />Hozza létre a második Storage-fiókot ugyanabban a régióban, mint az elsődleges fiókot, hogy elkerülje a kimenő forgalom díját. | Az adatok a második Storage-fiókból is visszaállíthatók, ha az elsődleges fiókot bármilyen módon feltörték. | A AzCopy és a Azure Data Factory támogatottak.<br /><br />Az objektum-replikáció nem támogatott. |

## <a name="data-protection-by-resource-type"></a>Adatvédelem erőforrás típusa szerint

A következő táblázat összefoglalja az Azure Storage adatvédelmi lehetőségeit az általuk védett erőforrások alapján.

| Adatvédelmi beállítás | Megvédi a fiókot a törléstől | Védi a tárolót a törléstől | Védi a blobot a törléstől | Védi a blobot a felülírások ellen |
|--|--|--|--|--|
| Azure Resource Manager zárolása | Yes | Nem<sup>1</sup> | Nem | Nem |
| Módosíthatatlansági szabályzata egy tárolón | Igen<sup>2</sup> | Igen | Igen | Yes |
| Tároló – Soft delete | Nem | Igen | Nem | Nem |
| A blob verziószámozása<sup>3</sup> | Nem | Nem | Igen | Yes |
| BLOB – Soft delete<sup>3</sup> | Nem | Nem | Igen | Yes |
| Időponthoz tartozó visszaállítás<sup>3</sup> | Nem | Nem | Igen | Yes |
| BLOB pillanatképe | Nem | Nem | Nem | Igen |
| Saját megoldás az adatok egy második fiókba való másolásához<sup>4</sup> | Nem | Igen | Igen | Yes |

<sup>1</sup> a Azure Resource Manager zárolások nem védik a tárolók törlését.<br />
<sup>2</sup> amíg egy jogi vagy zárolt időalapú adatmegőrzési szabályzat érvényes egy tárolóra, a Storage-fiók is védelmet biztosít a törléstől.<br />
<sup>3</sup> Data Lake Storage munkaterhelések esetében jelenleg nem támogatott.<br />
<sup>4</sup> a AzCopy és a Azure Data Factory olyan beállítások, amelyek Blob Storage és Data Lake Storage számítási feladatokhoz egyaránt támogatottak. Az objektum-replikálás csak Blob Storage munkaterhelések esetén támogatott.<br />

## <a name="recover-deleted-or-overwritten-data"></a>Törölt vagy felülírt adatok helyreállítása

Ha olyan adatokat kell helyreállítani, amelyek felül lettek írva vagy törölve lettek, a továbblépés módja attól függ, hogy melyik adatvédelmi lehetőség van engedélyezve, és milyen erőforrást érintettek. Az alábbi táblázat azokat a műveleteket ismerteti, amelyeket az adatok helyreállításához végezhet.

| Törölt vagy felülírt erőforrás | Lehetséges helyreállítási műveletek | A helyreállításra vonatkozó követelmények |
|--|--|--|
| Tárfiók | Kísérlet a törölt Storage-fiók helyreállítására<br />[tudj meg többet...](../common/storage-account-recover.md) | A Storage-fiók eredetileg a Azure Resource Manager üzembehelyezési modellel lett létrehozva, és az elmúlt 14 napban törölve lett. Nem jött létre új Storage-fiók ugyanazzal a névvel, mert az eredeti fiókot törölték. |
| Tároló | Helyreállíthatja a puha törlésű tárolót és annak tartalmát<br />[tudj meg többet...](soft-delete-container-enable.md) | A tároló-helyreállító törlés engedélyezve van, és a tároló helyreállítható törlésének megőrzési időtartama még nem járt le. |
| Tárolók és blobok | Adatok visszaállítása egy második Storage-fiókból | Minden tároló-és blob-műveletet ténylegesen replikáltak egy második Storage-fiókba. |
| BLOB (bármilyen típus) | BLOB visszaállítása egy korábbi<sup>1</sup> . verzióból<br />[tudj meg többet...](versioning-enable.md) | A blob verziószámozása engedélyezve van, és a blobnak legalább egy korábbi verziója van. |
| BLOB (bármilyen típus) | Helyreállítható, törölt blob<sup>1</sup><br />[tudj meg többet...](soft-delete-blob-enable.md) | A blob-alapú törlés engedélyezve van, és a helyreállítható törlés megőrzési időköze nem járt le. |
| BLOB (bármilyen típus) | BLOB visszaállítása pillanatképből<br />[tudj meg többet...](snapshots-manage-dotnet.md) | A blob egy vagy több pillanatképet tartalmaz. |
| Blokk-Blobok készlete | Blokkos Blobok készletének helyreállítása az állapotukra egy korábbi időpontban<sup>1</sup><br />[tudj meg többet...](point-in-time-restore-manage.md) | Az időponthoz való visszaállítás engedélyezve van, és a visszaállítási pont a megőrzési időtartamon belül van. A Storage-fiók nem sérült vagy sérült. |
| BLOB verziója | Helyreállítható<sup>1</sup> . verzió visszaállítása<br />[tudj meg többet...](soft-delete-blob-enable.md) | A blob-alapú törlés engedélyezve van, és a helyreállítható törlés megőrzési időköze nem járt le. |

<sup>1</sup> jelenleg nem támogatott Data Lake Storage munkaterhelések esetén.

## <a name="summary-of-cost-considerations"></a>A díjszabási megfontolások összefoglalása

A következő táblázat összefoglalja a jelen útmutatóban ismertetett különböző adatvédelmi beállításokkal kapcsolatos költségeket.

| Adatvédelmi beállítás | Költségekkel kapcsolatos szempontok |
|-|-|
| Storage-fiók Azure Resource Manager zárolása | Nem számítunk fel díjat a Storage-fiókok zárolásának konfigurálásához. |
| Módosíthatatlansági szabályzata egy tárolón | Nem számítunk fel díjat a módosíthatatlansági szabályzatok egy tárolóra való konfigurálásához. |
| Tároló – Soft delete | Nem számítunk fel díjat a Storage-fiókhoz tartozó tárolók törlésének engedélyezéséhez. A helyreállított tárolóban tárolt adatforgalom ugyanolyan sebességgel történik, mint az aktív adatként, amíg a rendszer véglegesen törli a helyreállított tárolót. |
| BLOB verziószámozása | A blob verziószámozásának engedélyezése a Storage-fiók esetében díjmentes. A blob verziószámozásának engedélyezése után a fiókban található Blobok összes írási vagy törlési művelete új verziót hoz létre, ami növelheti a kapacitási költségeket.<br /><br />A Blobok verziója egyedi blokkok vagy lapok alapján történik. A költségek tehát növekednek, mivel az alap blob eltér egy adott verziótól. A Blobok vagy Blobok verziójának módosítása a számlázási hatással lehet. További információkért tekintse meg a [díjszabást és a számlázást](versioning-overview.md#pricing-and-billing)ismertető témakört.<br /><br />Az életciklus-kezelés használatával a költségek szabályozása érdekében szükség szerint törölheti a régebbi verziókat. További információkért lásd: [a költségek optimalizálása az Azure Blob Storage hozzáférési szintjeinek automatizálásával](storage-lifecycle-management-concepts.md). |
| Blobos törlés | Egy Storage-fiók esetében díjmentesen engedélyezhető a blob-törlés. A helyreállított blobokban tárolt adatforgalom ugyanolyan sebességgel történik, mint az aktív adatforgalom, amíg a rendszer véglegesen törli a helyreállított blobot. |
| Adott időpontnak megfelelő helyreállítás | A tárolási fiókok időponthoz való visszaállításának engedélyezése díjmentes. az időponthoz való visszaállítás engedélyezése azonban lehetővé teszi a blob verziószámozását, a helyreállítható törlést és a hírcsatorna módosítását is, amelyek mindegyike további díjakat eredményezhet.<br /><br />A visszaállítási művelet végrehajtásakor a rendszer az időponthoz tartozó visszaállítást számlázza. A visszaállítási művelet díja a visszaállított adatok mennyiségétől függ. További információkért tekintse meg a [díjszabást és a számlázást](point-in-time-restore-overview.md#pricing-and-billing)ismertető témakört. |
| BLOB-Pillanatképek | A pillanatképekben tárolt adatokért egyedi blokkok vagy lapok alapján kell fizetni. A költségek tehát növekednek, mivel az alap blob eltér a pillanatképtől. A Blobok vagy Pillanatképek rétegének módosítása számlázási hatással lehet. További információkért tekintse meg a [díjszabást és a számlázást](snapshots-overview.md#pricing-and-billing)ismertető témakört.<br /><br />Az életciklus-kezelés használatával a költségek szabályozása érdekében szükség szerint törölheti a régebbi pillanatképeket. További információkért lásd: [a költségek optimalizálása az Azure Blob Storage hozzáférési szintjeinek automatizálásával](storage-lifecycle-management-concepts.md). |
| Az Adatmásolás egy második Storage-fiókba | A második Storage-fiókban tárolt adatszolgáltatások kapacitása és tranzakciós költségekkel járnak. Ha a második Storage-fiók a forrásoldali fióktól eltérő régióban található, akkor az adatoknak a második fiókba való másolása emellett a kimenő forgalom díját is figyelembe veszi. |

## <a name="disaster-recovery"></a>Vészhelyreállítás

Az Azure Storage mindig több példányban tárolja az adatait, így védve van a tervezett és nem tervezett eseményektől, beleértve az átmeneti hardver meghibásodását, a hálózati vagy áramkimaradást, valamint a súlyos természeti katasztrófákat. A redundancia biztosítja, hogy a Storage-fiók a meghibásodások előtt is kielégítse a rendelkezésre állási és tartóssági célokat. A Storage-fiók magas rendelkezésre állásra való konfigurálásával kapcsolatos további információkért lásd: [Azure Storage redundancia](../common/storage-redundancy.md).

Abban az esetben, ha egy adatközpontban hiba történik, ha a Storage-fiók két földrajzi régióban (Geo-redundáns) keresztül redundáns, akkor lehetősége van arra, hogy a fiókját az elsődleges régióból a másodlagos régióba adja át. További információkért lásd a vész [-helyreállítási és a Storage-fiók feladatátvételét](../common/storage-disaster-recovery-guidance.md)ismertető témakört.

Az ügyfél által felügyelt feladatátvétel jelenleg nem támogatott olyan Storage-fiókok esetében, amelyeken engedélyezve van a hierarchikus névtér. További információ: [Azure Data Lake Storage Gen2 elérhető blob Storage-funkciók](data-lake-storage-supported-blob-storage-features.md).

## <a name="next-steps"></a>Következő lépések

- [Redundancia az Azure Storage szolgáltatásban](../common/storage-redundancy.md)
- [Vészhelyreállítás és tárfiók feladatátvétele](../common/storage-disaster-recovery-guidance.md)
