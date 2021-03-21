---
title: Adatredundancia
titleSuffix: Azure Storage
description: Az adatredundancia megismerése az Azure Storage-ban. A rendszer a Microsoft Azure Storage fiókjában lévő adatait a tartósság és a magas rendelkezésre állás érdekében replikálja.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 955d3330d3f08d7e7f024ec2c36941d02244d9ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726836"
---
# <a name="azure-storage-redundancy"></a>Redundancia az Azure Storage szolgáltatásban

Az Azure Storage mindig több példányban tárolja az adatait, így védve van a tervezett és nem tervezett eseményektől, beleértve az átmeneti hardver meghibásodását, a hálózati vagy áramkimaradást, valamint a súlyos természeti katasztrófákat. A redundancia biztosítja, hogy a Storage-fiók a meghibásodások előtt is kielégítse a rendelkezésre állási és tartóssági célokat.

Annak eldöntéséhez, hogy melyik redundancia-lehetőség a legmegfelelőbb a forgatókönyvhöz, vegye figyelembe az alacsonyabb költségek és a magasabb rendelkezésre állás közötti kompromisszumokat. Az alábbi tényezők segítenek meghatározni, hogy melyik redundancia-beállítást kell választania:  

- Hogyan replikálódnak az adatai az elsődleges régióban
- Azt jelzi, hogy a rendszer replikálja-e az adatait egy második régióba, amely földrajzilag távol van az elsődleges régiótól, hogy védelmet nyújtson a regionális katasztrófák ellen
- Azt jelzi, hogy az alkalmazásnak van-e olvasási hozzáférése a másodlagos régióban lévő replikált értékekhez, ha az elsődleges régió bármilyen okból elérhetetlenné válik

## <a name="redundancy-in-the-primary-region"></a>Redundancia az elsődleges régióban

Az Azure Storage-fiókban lévő adategységek mindig háromszor replikálódnak az elsődleges régióba. Az Azure Storage két lehetőséget ajánl fel az adatok replikálási módjaként az elsődleges régióban.

- A **helyileg redundáns tárolás (LRS)** az adatfeldolgozást az elsődleges régióban lévő egyetlen fizikai helyen belül háromszor másolja. A LRS a legkevésbé költséges replikálási lehetőség, de nem ajánlott magas rendelkezésre állást igénylő alkalmazásokhoz.
- A **Zone-redundáns tárolás (ZRS)** az elsődleges régió három Azure-beli rendelkezésre állási zónájában szinkron módon másolja az adatait. A magas rendelkezésre állást igénylő alkalmazások esetében a Microsoft javasolja a ZRS használatát az elsődleges régióban, valamint egy másodlagos régióba való replikálást is.

### <a name="locally-redundant-storage"></a>Helyileg redundáns tárolás

A helyileg redundáns tárolás (LRS) háromszor replikálja az adatait az elsődleges régió egyetlen adatközpontjában. A LRS legalább 99,999999999%-os (11 kilenc) tartósságot biztosít az objektumok számára egy adott évben.

A LRS a legalacsonyabb árú redundancia-beállítás, és a lehető legkevesebb tartósságot kínálja a többi lehetőséghez képest. A LRS megvédi adatait a Server rack és a meghajtó meghibásodása ellen. Ha azonban az adatközponton belül egy sérülés, például a tűz vagy az árvíz történik, a LRS használó Storage-fiókok összes replikája elveszhet vagy helyreállíthatatlan lehet. A kockázat enyhítése érdekében a [Microsoft a ZRS](#zone-redundant-storage) -t, a [geo-redundáns tárolást](#geo-redundant-storage) (GRS) vagy a [geo-zóna redundáns tárolást](#geo-zone-redundant-storage) (GZRS) javasolja.

A LRS-t használó Storage-fiókra vonatkozó írási kérelem szinkron módon történik. Az írási művelet csak akkor tér vissza sikeresen, ha az adatok mindhárom replikára íródnak.

Az alábbi ábra bemutatja, hogyan replikálódnak az adatai egyetlen adatközpontban a LRS:

:::image type="content" source="media/storage-redundancy/locally-redundant-storage.png" alt-text="Ábra, amely bemutatja, hogyan replikálódnak az egyes adatközpontokban lévő adategységek az LRS":::

A LRS jó választás a következő esetekben:

- Ha az alkalmazás adatvesztés esetén könnyen újraépíthető adatait tárolja, a LRS is dönthet.
- Ha az alkalmazás az adatirányítási követelmények miatt csak az országon vagy régión belül replikálja az adatreplikálást, a LRS is dönthet. Bizonyos esetekben előfordulhat, hogy a párosított régiók, amelyeken az adatföldrajzi hely replikálása megtörténik, egy másik országban vagy régióban lehet. A párosított régiókkal kapcsolatos további információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Zónaredundáns tárolás

A Zone-redundáns tárolás (ZRS) az Azure Storage-adatait szinkron módon replikálja az elsődleges régió három Azure-beli rendelkezésre állási zónáján belül. Minden rendelkezésreállási zóna egy fizikailag elkülönített, független áramforrással, hűtéssel és hálózatkezelési megoldással rendelkező hely. A ZRS az Azure Storage-adatobjektumok tartósságát kínálja legalább 99,9999999999%-ban (12 9) az adott évben.

A ZRS esetében az adatok továbbra is elérhetők olvasási és írási műveletekhez, még akkor is, ha egy zóna elérhetetlenné válik. Ha egy zóna elérhetetlenné válik, az Azure hálózati frissítéseket végez, például a DNS-re. Ezek a frissítések hatással lehetnek az alkalmazásra, ha a frissítések befejeződése előtt hozzáférnek az adataihoz. A ZRS alkalmazások tervezésekor kövesse az átmeneti hibák kezelésének eljárásait, beleértve az újrapróbálkozási szabályzatok az exponenciális visszatartással történő megvalósítását.

A ZRS-t használó Storage-fiókra vonatkozó írási kérelem szinkron módon történik. Az írási művelet csak akkor ad eredményül, ha az adatok a három rendelkezésre állási zónában lévő összes replikára íródnak.

A Microsoft a ZRS használatát javasolja az elsődleges régióban olyan forgatókönyvek esetében, amelyek következetességet, tartósságot és magas rendelkezésre állást igényelnek. A ZRS azt is javasoljuk, hogy az adatkezelési követelmények teljesítése érdekében korlátozza az adatreplikációt egy adott országon vagy régióban.

Az alábbi ábra bemutatja, hogyan replikálódnak az adatai a rendelkezésre állási zónák között az elsődleges régió ZRS:

:::image type="content" source="media/storage-redundancy/zone-redundant-storage.png" alt-text="Ábra, amely bemutatja, hogyan replikálódnak az adatreplikálás az elsődleges régióban a ZRS":::

A ZRS kiváló teljesítményt, kis késleltetést és rugalmasságot biztosít az adatai számára, ha átmenetileg elérhetetlenné válik. A ZRS önmagában azonban nem nyújt védelmet az adatainak olyan regionális katasztrófák ellenében, ahol a rendszer több zónát tartósan érint. A regionális katasztrófák elleni védelem érdekében a Microsoft azt javasolja, hogy a [geo-Zone-redundáns tárolást](#geo-zone-redundant-storage) (GZRS) használja, amely az elsődleges régióban lévő ZRS használja, és földrajzilag replikálja az adatait egy másodlagos régióba.

A következő táblázat azt mutatja be, hogy milyen típusú Storage-fiókok támogatják a ZRS, amelyekben a régiók:

| Tárfiók típusa | Támogatott régiók | Támogatott szolgáltatások |
|--|--|--|
| Általános célú v2<sup>1</sup> | Délkelet-Ázsia<br /> Kelet-Ausztrália<br /> Észak-Európa<br />  Nyugat-Európa<br /> Közép-Franciaország<br /> Kelet-Japán<br /> Dél-Afrika északi régiója<br /> Az Egyesült Királyság déli régiója<br /> USA középső régiója<br /> USA keleti régiója<br /> USA 2. keleti régiója<br /> USA 2. nyugati régiója | Blokkblobok<br /> <sup>2</sup> . oldal Blobok<br /> Fájlmegosztás (standard)<br /> Táblázatok<br /> Üzenetsorok<br /> |
| <sup>1</sup> . BlockBlobStorage | Délkelet-Ázsia<br /> Kelet-Ausztrália<br /> Észak-Európa<br /> Nyugat-Európa<br /> Közép-Franciaország <br /> Kelet-Japán<br /> Az Egyesült Királyság déli régiója <br /> USA keleti régiója <br /> USA 2. keleti régiója <br /> USA 2. nyugati régiója| Csak Premium blokk Blobok |
| FileStorage | Délkelet-Ázsia<br /> Kelet-Ausztrália<br /> Észak-Európa<br /> Nyugat-Európa<br /> Közép-Franciaország <br /> Kelet-Japán<br /> Az Egyesült Királyság déli régiója <br /> USA keleti régiója <br /> USA 2. keleti régiója <br /> USA 2. nyugati régiója | Csak a prémium szintű fájlok megosztása |

<sup>1</sup> az archiválási szint jelenleg nem támogatott a ZRS-fiókok esetében.<br />
<sup>2</sup> a virtuális gépekhez készült Azure Managed Disks-t tartalmazó Storage-fiókok mindig a LRS-t használják. Az Azure Unmanaged Disks szolgáltatásnak a LRS is használnia kell. Létrehozhat egy Storage-fiókot az Azure nem felügyelt, GRS használó lemezek számára, de az aszinkron geo-replikációval kapcsolatos lehetséges problémák miatt nem ajánlott. Sem a felügyelt, sem a nem felügyelt lemezek támogatják a ZRS vagy a GZRS. A felügyelt lemezekkel kapcsolatos további információkért lásd: [Az Azure Managed Disks díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/).

További információ arról, hogy mely régiók támogatják a ZRS-t: a **szolgáltatások támogatása régiók szerint**  a [Mi a Azure Availability Zones?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Redundancia egy másodlagos régióban

A magas rendelkezésre állást igénylő alkalmazások esetében választhatja azt is, hogy a Storage-fiókban lévő adatok egy olyan másodlagos régióba másolható, amely több száz kilométerre van az elsődleges régiótól. Ha a Storage-fiókot egy másodlagos régióba másolják, akkor az adatai tartósak maradnak, még akkor is, ha egy teljes regionális leállás vagy egy olyan katasztrófa következik be, amelyben az elsődleges régió nem helyreállítható.

A Storage-fiók létrehozásakor ki kell választania a fiók elsődleges régióját. A párosított másodlagos régió az elsődleges régió alapján van meghatározva, és nem módosítható. Az Azure által támogatott régiókkal kapcsolatos további információkért lásd: [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/).

Az Azure Storage két lehetőséget kínál az adatok másodlagos régióba történő másolására:

- A **Georedundáns tárolás (GRS)** az adatokat szinkron módon, az LRS használatával háromszor másolja le az elsődleges régió egy fizikai helyére. Ezután aszinkron módon másolja át az adatokat a másodlagos régió egy fizikai helyére. A másodlagos régión belül az adatait a LRS használatával egyidejűleg háromszor másolja a rendszer.
- A **geo-Zone-redundáns tárolás (GZRS)** az összes Azure-beli rendelkezésre állási zónában szinkron módon másolja az adatait az elsődleges régióban az ZRS használatával. Ezután aszinkron módon másolja át az adatokat a másodlagos régió egy fizikai helyére. A másodlagos régión belül az adatait a LRS használatával egyidejűleg háromszor másolja a rendszer.

> [!NOTE]
> A GRS és a GZRS közötti elsődleges különbség az, hogy az elsődleges régióban hogyan replikálódnak az adathalmazok. A másodlagos régión belül a LRS használatával a rendszer mindig szinkron módon replikálja az adatátvitelt. A másodlagos régióban lévő LRS megvédi adatait a hardver meghibásodása ellen.

A GRS vagy a GZRS esetében a másodlagos régióban lévő információk nem érhetők el olvasási vagy írási hozzáférés esetén, kivéve, ha feladatátvétel történik a másodlagos régióval. Ha olvasási hozzáférést szeretne adni a másodlagos régióhoz, konfigurálja a Storage-fiókot az olvasási hozzáférésű geo-redundáns tárolás (RA-GRS) vagy az olvasási hozzáférésű földrajzi zóna – redundáns tárolás (RA-GZRS) használatára. További információ: [olvasási hozzáférés az adatokhoz a másodlagos régióban](#read-access-to-data-in-the-secondary-region).

Ha az elsődleges régió elérhetetlenné válik, dönthet úgy, hogy átadja a feladatátvételt a másodlagos régiónak. A feladatátvétel befejeződése után a másodlagos régió lesz az elsődleges régió, és újra elolvashatja és írhatja az adataikat. A vész-helyreállítással és a másodlagos régióba való feladatátvételsel kapcsolatos további információkért tekintse meg a vész [-helyreállítási és a Storage-fiók feladatátvételét](storage-disaster-recovery-guidance.md)ismertető témakört.

> [!IMPORTANT]
> Mivel az adatreplikáció a másodlagos régióba aszinkron módon történik, az elsődleges régiót érintő hibák adatvesztést okozhatnak, ha az elsődleges régió nem állítható helyre. Az elsődleges régióba irányuló legutóbbi írások és a másodlagos régió utolsó írása közötti intervallum a helyreállítási pont célkitűzése (RPO). A RPO azt az időpontot jelöli, ameddig az Adathelyreállítás visszaállítható. Az Azure Storage szolgáltatásnak jellemzően 15 percnél rövidebb RPO van, bár jelenleg nem áll rendelkezésre SLA, hogy mennyi ideig tart az adatreplikálás a másodlagos régióba.

### <a name="geo-redundant-storage"></a>Georedundáns tárolás

A Georedundáns tárolás (GRS) az adatokat szinkron módon, az LRS használatával háromszor másolja le az elsődleges régió egy fizikai helyére. Ezután aszinkron módon másolja az adatait egy olyan másodlagos régióba, amely több száz mérföld távolságra van az elsődleges régiótól. A GRS az Azure Storage-adatobjektumok tartósságát kínálja legalább 99.99999999999999%-ban (16 9) az adott évben.

A rendszer először egy írási műveletet véglegesít az elsődleges helyen, és replikálja a LRS használatával. A rendszer ezután aszinkron módon replikálja a frissítést a másodlagos régióba. Ha az adatírás a másodlagos helyre történik, a rendszer a LRS használatával is replikálja az adott helyen belül.

Az alábbi ábra bemutatja, hogyan replikálódnak az adatai GRS vagy RA-GRS:

:::image type="content" source="media/storage-redundancy/geo-redundant-storage.png" alt-text="Az GRS vagy RA-GRS által replikált adatreplikációt bemutató ábra":::

### <a name="geo-zone-redundant-storage"></a>Zóna- és georedundáns tárolás

A Geo-Zone-redundáns tárolás (GZRS) a rendelkezésre állási zónákon keresztüli redundancia által biztosított magas rendelkezésre állást ötvözi a földrajzi replikálás által biztosított regionális kimaradások elleni védelemmel. A GZRS-fiókban lévő adatok az elsődleges régióban három Azure-beli [rendelkezésre állási zónába](../../availability-zones/az-overview.md) másolódnak át, és a regionális katasztrófák elleni védelem érdekében a másodlagos földrajzi régióba is replikálódnak. A Microsoft a GZRS használatát javasolja a maximális következetességet, tartósságot és rendelkezésre állást, a kiváló teljesítményt és a vész-helyreállítási rugalmasságot igénylő alkalmazások számára.

GZRS-fiókkal folytathatja az adatok olvasását és írását, ha a rendelkezésre állási zónák elérhetetlenné válnak, vagy nem állíthatók helyre. Emellett az adatai tartósak is maradnak a teljes regionális leállás vagy egy olyan katasztrófa esetén, amelyben az elsődleges régió nem helyreállítható. A GZRS úgy lett kialakítva, hogy legalább 99.99999999999999%-os (16 9) tartósságot biztosítson az objektumok számára egy adott évben.

Az alábbi ábra bemutatja, hogyan replikálódnak az adatai GZRS vagy RA-GZRS:

:::image type="content" source="media/storage-redundancy/geo-zone-redundant-storage.png" alt-text="Az GZRS vagy RA-GZRS által replikált adatreplikációt bemutató ábra":::

Csak az általános célú v2 tároló-fiókok támogatják a GZRS és az RA-GZRS. További információ a Storage-fiókok típusairól: az [Azure Storage-fiók áttekintése](storage-account-overview.md). A GZRS és RA-GZRS támogatja a blokk blobokat, az oldal blobokat (kivéve a VHD-lemezeket), a fájlokat, a táblákat és a várólistákat.

A GZRS és az RA-GZRS a következő régiókban támogatott:

- Délkelet-Ázsia
- Észak-Európa
- Nyugat-Európa
- Kelet-Japán
- Az Egyesült Királyság déli régiója
- USA középső régiója
- USA keleti régiója
- USA 2. keleti régiója
- USA 2. nyugati régiója

A díjszabással kapcsolatos információkért tekintse meg a [Blobok](https://azure.microsoft.com/pricing/details/storage/blobs), [fájlok](https://azure.microsoft.com/pricing/details/storage/files/), [várólisták](https://azure.microsoft.com/pricing/details/storage/queues/)és [táblák](https://azure.microsoft.com/pricing/details/storage/tables/)díjszabását.

## <a name="read-access-to-data-in-the-secondary-region"></a>Olvasási hozzáférés a másodlagos régióban lévő adatelérési ponthoz

A Geo-redundáns tárolás (GRS vagy GZRS) replikálja az adatait a másodlagos régió egy másik fizikai helyére a regionális kimaradások elleni védelem érdekében. Ezek az adatok azonban csak olvashatók, ha az ügyfél vagy a Microsoft feladatátvételt kezdeményez az elsődlegesről a másodlagos régióba. Ha engedélyezi az olvasási hozzáférést a másodlagos régióhoz, az adatai mindig olvashatók lesznek, például olyan helyzetekben, amikor az elsődleges régió elérhetetlenné válik. Ha olvasási hozzáférést szeretne a másodlagos régióhoz, engedélyezze az olvasási hozzáférésű geo-redundáns tárolást (RA-GRS) vagy az olvasási hozzáférésű földrajzi zóna-redundáns tárolást (RA-GZRS).

> [!NOTE]
> A Azure Files nem támogatja az olvasási hozzáférésű geo-redundáns tárolást (RA-GRS) és az olvasási hozzáférésű geo-Zone-redundáns tárolást (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Alkalmazások tervezése a másodlagoshoz való olvasási hozzáféréshez

Ha a Storage-fiókja olvasási hozzáférésre van konfigurálva a másodlagos régióhoz, akkor megtervezheti, hogy az alkalmazások zökkenőmentesen átálljanak a másodlagos régió adatainak olvasására, ha az elsődleges régió bármilyen okból elérhetetlenné válik. 

Az RA-GRS vagy RA-GZRS engedélyezése után a másodlagos régió olvasási hozzáférésre is használható, így előre ellenőrizheti, hogy az alkalmazás megfelelően van-e beolvasva a másodlagosról leállás esetén. További információ az alkalmazások magas rendelkezésre állásra való tervezéséről: a [geo-redundancia használata a magas rendelkezésre állású alkalmazások tervezéséhez](geo-redundant-design.md).

Ha engedélyezve van az olvasási hozzáférés a másodlagoshoz, az alkalmazás beolvasható a másodlagos végpontból, illetve az elsődleges végpontból is. A másodlagos végpont hozzáfűzi az utótagot *– a másodlagos* nevet a fiók nevéhez. Ha például a blob Storage elsődleges végpontja `myaccount.blob.core.windows.net` , akkor a másodlagos végpont `myaccount-secondary.blob.core.windows.net` . A Storage-fiókhoz tartozó fiók-hozzáférési kulcsok mind az elsődleges, mind a másodlagos végpont esetében azonosak.

### <a name="check-the-last-sync-time-property"></a>Az Utolsó szinkronizálás időpontja tulajdonság ellenőrzése

Mivel a rendszer aszinkron módon replikálja az adatfájlokat a másodlagos régióba, a másodlagos régió gyakran az elsődleges régió mögött van. Ha hiba történik az elsődleges régióban, akkor valószínű, hogy az elsődlegesnél az összes írás még nem lett replikálva a másodlagosra.

Annak megállapításához, hogy mely írási műveletek lettek replikálva a másodlagos régióba, az alkalmazás megtekintheti a Storage-fiók **utolsó szinkronizálási idő** tulajdonságát. Az elsődleges régióba az utolsó szinkronizálás időpontja előtt írt összes írási műveletet sikeresen replikálták a másodlagos régióba, ami azt jelenti, hogy elérhetők a másodlagos helyről való olvasáshoz. Az elsődleges régióba az utolsó szinkronizálás időpontja után írt írási műveletek nem replikálódtak a másodlagos régióra, ami azt jelenti, hogy nem lesznek elérhetők olvasási műveletekhez.

A **Legutóbbi szinkronizálási idő** tulajdonság értékét a Azure PowerShell, az Azure CLI vagy az Azure Storage ügyféloldali kódtárai használatával kérdezheti le. Az **utolsó szinkronizálás időpontja** tulajdonság egy GMT dátum/idő érték. További tudnivalókért tekintse [meg a Storage-fiók utolsó szinkronizálási idejének tulajdonságát](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Redundancia-beállítások összefoglalása

A következő szakaszban található táblázatok összefoglalják az Azure Storage-ban elérhető redundancia-beállításokat

### <a name="durability-and-availability-parameters"></a>Tartóssági és rendelkezésre állási paraméterek

Az alábbi táblázat az egyes redundancia-beállítások főbb paramétereit ismerteti:

| Paraméter | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Objektumok tartóssága az adott évben | legalább 99,999999999% (11 9) | legalább 99,9999999999% (12 9) | legalább 99.99999999999999% (16 9) | legalább 99.99999999999999% (16 9) |
| Olvasási kérelmek rendelkezésre állása | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) a GRS<br /><br />Legalább 99,99% (99,9% a lassú elérési szinthez) az RA-GRS | Legalább 99,9% (99% a lassú elérési szinthez) a GZRS<br /><br />Legalább 99,99% (99,9% a lassú elérési szinthez) az RA-GZRS |
| Írási kérelmek rendelkezésre állása | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) |
| A különálló csomópontokon tárolt adatpéldányok száma | Három másolat egyetlen régión belül | Három másolat az egyes régiókban található különálló rendelkezésre állási zónák között | Hat másolat összesen, beleértve az elsődleges régióban háromat, a másodlagos régióban pedig háromat | Hat másolat összesen, beleértve az elsődleges régió különálló rendelkezésre állási zónáinak három, valamint a másodlagos régióban három helyileg redundáns másolatát |

### <a name="durability-and-availability-by-outage-scenario"></a>Tartósság és rendelkezésre állás leállás esetén

Az alábbi táblázat azt jelzi, hogy az adatai tartósak-e, és elérhetőek-e az adott forgatókönyvben, attól függően, hogy milyen típusú redundancia van érvényben a Storage-fiókhoz:

| Kimaradási forgatókönyv | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Az adatközpontban lévő csomópont elérhetetlenné válik | Igen | Igen | Igen | Yes |
| Egy teljes adatközpont (Zona vagy nem zónák) elérhetetlenné válik | Nem | Igen | Igen<sup>1</sup> | Yes |
| Az elsődleges régióban az egész régióra kiterjedő leállás következik be | Nem | Nem | Igen<sup>1</sup> | Igen<sup>1</sup> |
| A másodlagos régióhoz való olvasási hozzáférés akkor érhető el, ha az elsődleges régió elérhetetlenné válik | Nem | Nem | Igen (az RA-GRS-vel) | Igen (az RA-GZRS-vel) |

<sup>1</sup> a fiók feladatátvétele szükséges az írási rendelkezésre állás visszaállításához, ha az elsődleges régió elérhetetlenné válik. További információkért lásd a vész [-helyreállítási és a Storage-fiók feladatátvételét](storage-disaster-recovery-guidance.md)ismertető témakört.

### <a name="supported-azure-storage-services"></a>Támogatott Azure Storage-szolgáltatások

Az alábbi táblázat az egyes Azure Storage-szolgáltatások által támogatott redundancia-beállításokat mutatja be.

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br />Azure Managed Disks | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br /> | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br /> |

### <a name="supported-storage-account-types"></a>Támogatott Storage-fiókok típusai

A következő táblázat bemutatja, hogy az egyes típusú Storage-fiókok milyen redundancia-beállításokat támogatnak. A Storage-fiókok típusairól a [Storage-fiók áttekintése](storage-account-overview.md)című témakörben olvashat bővebben.

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Általános célú v2<br /> Általános célú v1<br /> BlockBlobStorage<br /> BlobStorage<br /> FileStorage | Általános célú v2<br /> BlockBlobStorage<br /> FileStorage | Általános célú v2<br /> Általános célú v1<br /> BlobStorage | Általános célú v2 |

Az összes Storage-fiókra vonatkozó összes adattal a rendszer a Storage-fiók redundancia beállításának megfelelően másolja. Az objektumok, például a Blobok, a Blobok, a Blobok, a várólisták, a táblák és a fájlok másolása történik. A rendszer átmásolja az összes szintet, beleértve az archiválási szintet is. A blob-rétegekkel kapcsolatos további információkért lásd [: Azure Blob Storage: gyors, ritka elérésű és archív hozzáférési szintek](../blobs/storage-blob-storage-tiers.md).

Az egyes redundancia-lehetőségek díjszabásáról az [Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/)ismertető cikk nyújt tájékoztatást.

> [!NOTE]
> Az Azure Premium Disk Storage jelenleg csak a helyileg redundáns tárolást (LRS) támogatja. A blob Storage-fiókok nem támogatják a helyileg redundáns tárolást (LRS) és a Zone redundáns tárolást (ZRS) bizonyos régiókban.

## <a name="data-integrity"></a>Adatintegritás

Az Azure Storage rendszeresen ellenőrzi a ciklikus redundancia-ellenőrzések (FCSF) használatával tárolt adatok integritását. Ha a rendszer az adatsérülést észleli, a redundáns adatvesztéssel kerül kijavításra. Az Azure Storage az összes hálózati forgalom ellenőrzőösszegét is kiszámítja, hogy észlelje az adatcsomagok sérülését az adatok tárolása vagy beolvasása során.

## <a name="see-also"></a>Lásd még

- [A Storage-fiók utolsó szinkronizálási ideje tulajdonságának megtekintése](last-sync-time-get.md)
- [Storage-fiók redundancia beállításának módosítása](redundancy-migration.md)
- [A Geo-redundancia használata a magasan elérhető alkalmazások kialakításához](geo-redundant-design.md)
- [Vészhelyreállítás és tárfiók feladatátvétele](storage-disaster-recovery-guidance.md)
