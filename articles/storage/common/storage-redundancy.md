---
title: Adatredundancia
titleSuffix: Azure Storage
description: Az Azure Storage-beli adatredundania. A rendszer a Microsoft Azure Storage és a magas rendelkezésre állás érdekében replikálja az adatokat.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3665421ddbdd9cf079ff4aab9377fc9164a1599c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575360"
---
# <a name="azure-storage-redundancy"></a>Redundancia az Azure Storage szolgáltatásban

Az Azure Storage mindig több másolatot tárol az adatokról, így védve vannak a tervezett és nem tervezett eseményektől, beleértve az átmeneti hardverhibákat, a hálózati vagy áramkimaradásokat és a súlyos természeti katasztrófákat. A redundancia biztosítja, hogy a tárfiók még meghibásodások esetén is megfeleljen a rendelkezésre állási és tartóssági céloknak.

Amikor eldönti, hogy melyik redundancia-lehetőség a legmegfelelőbb az adott forgatókönyvhöz, vegye figyelembe az alacsonyabb költségek és a magasabb rendelkezésre állás közötti különbségeket. A választható redundancia-beállítást befolyásoló tényezők többek között a következők:  

- Az adatok replikálása az elsődleges régióban
- Az adatok replikálása egy második, az elsődleges régiótól földrajzilag távoli régióba történik-e a regionális katasztrófákkal szembeni védelem érdekében
- Azt határozza meg, hogy az alkalmazás olvasási hozzáférést igényel-e a másodlagos régióban található replikált adatokhoz, ha az elsődleges régió valamilyen okból elérhetetlenné válik

## <a name="redundancy-in-the-primary-region"></a>Redundancia az elsődleges régióban

Az Azure Storage-fiókban található adatokat a rendszer mindig háromszor replikálja az elsődleges régióban. Az Azure Storage két lehetőséget ajánl fel az adatok replikálási módjaként az elsődleges régióban.

- **A helyileg redundáns tárolás (LRS)** az adatokat szinkron módon háromszor másolja az elsődleges régió egyetlen fizikai helyére. Az LRS a legkevésbé költséges replikációs lehetőség, de nem ajánlott a magas rendelkezésre állást igénylő alkalmazásokhoz.
- **A zónaredundáns tárolás (ZRS)** szinkron módon másolja az adatokat az elsődleges régió három Azure rendelkezésre állási zónájában. A magas rendelkezésre állást igénylő alkalmazások esetében a Microsoft a ZRS elsődleges régióban való használatát, valamint másodlagos régióba való replikálást javasolja.

> [!NOTE]
> A Microsoft a ZRS elsődleges régióban való használatát javasolja Azure Data Lake Storage Gen2 számítási feladatokhoz.

### <a name="locally-redundant-storage"></a>Helyileg redundáns tárolás

A helyileg redundáns tárolás (LRS) háromszor replikálja az adatokat az elsődleges régió egyetlen adatközpontjában. Az LRS legalább 99,99999999999%-os (11 kilences) tartósságot biztosít az objektumok számára egy adott évben.

Az LRS a legalacsonyabb költségű redundancia, és a többi lehetőséghez képest a legalacsonyabb tartósságot kínálja. Az LRS megvédi adatait a kiszolgálóállvány- és meghajtóhibáktól. Ha azonban egy katasztrófa, például tűz vagy elárasztás következik be az adatközpontban, akkor az LRS-t használó tárfiók összes replikája elveszhet vagy nem állítható vissza. A kockázat csökkentése érdekében a Microsoft zónaredundáns [tárolás](#zone-redundant-storage) (ZRS), [](#geo-redundant-storage) georedundáns tárolás (GRS) vagy geo-zónaredundáns tárolás (GZRS) használatát javasolja. [](#geo-zone-redundant-storage)

Az LRS-t használó tárfiókok írási kérése szinkron módon történik. Az írási művelet csak akkor tér vissza sikeresen, ha az adatok mind a három replikára fel vannak írva.

Az alábbi ábra bemutatja, hogyan replikálja az adatokat egy LRS-sel egy adatközpontban:

:::image type="content" source="media/storage-redundancy/locally-redundant-storage.png" alt-text="Ábra az adatok LRS-sel való replikálásról egyetlen adatközpontban":::

Az LRS a következő esetekben jó választás:

- Ha az alkalmazás olyan adatokat tárol, amelyek adatvesztés esetén könnyen rekonstruálhatók, választhatja az LRS-t.
- Ha az alkalmazás adatirányítási követelmények miatt csak egy országon vagy régión belül replikálja az adatokat, választhatja az LRS használatát. Bizonyos esetekben a párosított régiók, amelyekben az adatok georeplikáltak, egy másik országban vagy régióban lehetnek. További információ a párosított régiókról: [Azure-régiók.](https://azure.microsoft.com/regions/)

### <a name="zone-redundant-storage"></a>Zónaredundáns tárolás

A zónaredundáns tárolás (ZRS) szinkron módon replikálja az Azure Storage-adatokat az elsődleges régió három Azure rendelkezésre állási zónájában. Minden rendelkezésreállási zóna egy fizikailag elkülönített, független áramforrással, hűtéssel és hálózatkezelési megoldással rendelkező hely. A ZRS legalább 99,99999999999%-os (12 9-es) tartósságot biztosít az Azure Storage-adatobjektumok számára egy adott évben.

A ZRS-sel az adatok akkor is elérhetők az olvasási és írási műveletekhez, ha egy zóna elérhetetlenné válik. Ha egy zóna elérhetetlenné válik, az Azure hálózati frissítéseket végez, például a DNS-re való újrapontozást. Ezek a frissítések hatással lehetnek az alkalmazásra, ha a frissítések befejezése előtt fér hozzá az adatokhoz. Amikor ZRS-hez tervez alkalmazásokat, kövesse az átmeneti hibák kezelésére vonatkozó eljárásokat, beleértve az exponenciális visszacsatolással való újrapróbálkozási szabályzatok megvalósításának gyakorlatát.

A ZRS-t használó tárfiókra vonatkozó írási kérések szinkron módon történnek. Az írási művelet csak akkor tér vissza sikeresen, ha az adatokat a három rendelkezésre állási zónában lévő összes replikába írta.

A Microsoft a ZRS elsődleges régióban való használatát javasolja olyan forgatókönyvek esetén, amelyek konzisztenciát, tartósságot és magas rendelkezésre állást igényelnek. A ZRS használata javasolt az adatok egy országon vagy régión belüli replikációjának az adatirányítási követelményeknek való megfelelés érdekében történő korlátozásához.

Az alábbi ábra bemutatja, hogyan replikálja az adatokat a ZRS-sel az elsődleges régió rendelkezésre állási zónáiban:

:::image type="content" source="media/storage-redundancy/zone-redundant-storage.png" alt-text="Az adatok ZRS-sel az elsődleges régióban való replikálási folyamatát bemutató ábra":::

A ZRS kiváló teljesítményt, alacsony késést és rugalmasságot biztosít az adatok számára, ha azok ideiglenesen elérhetetlenné válnak. Előfordulhat azonban, hogy a ZRS önmagában nem védi az adatokat olyan regionális katasztrófákkal szemben, amelyekben több zóna véglegesen érintett. A regionális katasztrófákkal szembeni védelem [](#geo-zone-redundant-storage) érdekében a Microsoft a geo-zónaredundáns tárolás (GZRS) használatát javasolja, amely ZRS-t használ az elsődleges régióban, és az adatokat egy másodlagos régióba replikálja.

Az alábbi táblázat bemutatja, hogy mely tárfióktípusok mely régiókban támogatják a ZRS-t:

| Tárfiók típusa | Támogatott régiók | Támogatott szolgáltatások |
|--|--|--|
| Általános célú v2<sup>1</sup> | (Afrika) Dél-Afrika északi részén<br /> (Ázsia és a Csendes-óceáni térség) Délkelet-Ázsia<br /> (Ázsia és a Csendes-óceáni térség) Kelet-Ausztrália<br /> (Ázsia és a Csendes-óceáni térség) Kelet-Japán<br /> (Kanada) Közép-Kanada<br /> (Európa) Észak-Európa<br /> (Európa) Nyugat-Európa<br /> (Európa) Közép-Franciaország<br /> (Európa) Nyugat-Németország – középső régió<br /> (Európa) Egyesült Királyság déli régiója<br /> (Dél-Amerika) Dél-Brazília<br /> (USA) USA középső középső<br /> (US) USA keleti régiója<br /> (USA) USA 2. keleti régiója<br /> (USA) USA déli középső sarka<br /> (USA) USA nyugati régiója<br /> (USA) USA 2. nyugati régiója | Blokkblobok<br /> Lapblobok<sup>2</sup><br /> Fájlmegosztások (standard)<br /> Táblázatok<br /> Üzenetsorok<br /> |
| BlockBlobStorage<sup>1</sup> | Délkelet-Ázsia<br /> Kelet-Ausztrália<br /> Észak-Európa<br /> Nyugat-Európa<br /> Közép-Franciaország <br /> Kelet-Japán<br /> Az Egyesült Királyság déli régiója <br /> USA keleti régiója <br /> USA 2. keleti régiója <br /> USA 2. nyugati régiója| Csak prémium szintű blokkblobok |
| FileStorage (Fájltartó) | Délkelet-Ázsia<br /> Kelet-Ausztrália<br /> Észak-Európa<br /> Nyugat-Európa<br /> Közép-Franciaország <br /> Kelet-Japán<br /> Az Egyesült Királyság déli régiója <br /> USA keleti régiója <br /> USA 2. keleti régiója <br /> USA 2. nyugati régiója | Csak prémium szintű fájlmegosztások |

<sup>1</sup> Az archív szint jelenleg nem támogatott a ZRS-fiókok esetében.<br />
<sup>2 A</sup> virtuális gépekhez Azure-beli felügyelt lemezeket tartalmazó tárfiókok mindig LRS-t használnak. A nem azure-beli nem használt lemezeknek is LRS-t kell használniuk. Létre lehet hozni egy tárfiókot grS-t használó, nem nem használt Azure-lemezekhez, de ez nem ajánlott az aszinkron georeplikáció konzisztenciájának lehetséges problémái miatt. Sem a felügyelt, sem a nem felügyelt lemezek nem támogatják a ZRS-t vagy a GZRS-t. A felügyelt lemezekkel kapcsolatos további információkért lásd: [Az Azure-beli felügyelt lemezek díjszabása.](https://azure.microsoft.com/pricing/details/managed-disks/)

Arról, hogy mely régiók támogatják a ZRS-t, tekintse meg a Szolgáltatások **támogatása**  régiónként szakaszt a Mi a [Azure Availability Zones?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Redundancia másodlagos régióban

A magas rendelkezésre állást igénylő alkalmazások esetében dönthet úgy, hogy a tárfiókban található adatokat egy másodlagos régióba másolja, amely több száz kilométerre van az elsődleges régiótól. Ha a tárfiókot egy másodlagos régióba másolja, akkor az adatok akkor is tartósak, ha az elsődleges régió nem állítható helyre teljes regionális kimaradás vagy katasztrófa esetén.

A tárfiók létrehozásakor ki kell választania a fiók elsődleges régióját. A párosított másodlagos régiót az elsődleges régió határozza meg, és nem módosítható. További információ az Azure által támogatott régiókról: [Azure-régiók.](https://azure.microsoft.com/global-infrastructure/regions/)

Az Azure Storage két lehetőséget kínál az adatok másodlagos régióba való másolására:

- A **Georedundáns tárolás (GRS)** az adatokat szinkron módon, az LRS használatával háromszor másolja le az elsődleges régió egy fizikai helyére. Ezután aszinkron módon másolja át az adatokat a másodlagos régió egy fizikai helyére. A másodlagos régión belül az adatok másolása szinkron módon háromszor, LRS használatával történt.
- **A geo-zónaredundáns tárolás (GZRS)** szinkron módon másolja az adatokat az elsődleges régió három Azure rendelkezésre állási zónájában a ZRS használatával. Ezután aszinkron módon másolja át az adatokat a másodlagos régió egy fizikai helyére. A másodlagos régión belül az adatok másolása szinkron módon háromszor, LRS használatával történt.

> [!NOTE]
> A GRS és a GZRS közötti elsődleges különbség az adatok elsődleges régióban való replikálása. A másodlagos régión belül az adatok replikálása mindig szinkron módon, háromszor, LRS használatával történt. A másodlagos régióban található LRS megvédi az adatait a hardverhibáktól.

GRS vagy GZRS rendszerekkel a másodlagos régióban található adatok nem érhetők el olvasási vagy írási hozzáféréshez, kivéve, ha feladatátvétel van a másodlagos régióba. A másodlagos régióhoz való olvasási hozzáféréshez konfigurálja a tárfiókot írási hozzáférésű georedundáns tárolás (RA-GRS) vagy írási hozzáférésű geo-zónaredundáns tárolás (RA-GZRS) használatára. További információ: Olvasási hozzáférés a másodlagos [régióban található adatokhoz.](#read-access-to-data-in-the-secondary-region)

Ha az elsődleges régió elérhetetlenné válik, dönthet úgy, hogy átveszi a feladatátvételt a másodlagos régióba. A feladatátvétel befejezése után a másodlagos régió lesz az elsődleges régió, és újra olvashatja és írhatja az adatokat. A vészhelyreállítással kapcsolatos további információkért és a másodlagos régióba való feladatátvétel elsajátításért lásd: Vészhelyreállítás és tárfiók [feladatátvétele.](storage-disaster-recovery-guidance.md)

> [!IMPORTANT]
> Mivel a rendszer aszinkron módon replikálja az adatokat a másodlagos régióba, az elsődleges régiót érintő hiba adatvesztést eredményezhet, ha az elsődleges régiót nem lehet helyreállítani. Az elsődleges régióba végzett legutóbbi írások és a másodlagos régióba végzett utolsó írás közötti időközt helyreállítási időkorrekta (RPO) néven ismerjük. Az RPO azt az időpontot jelzi, amelyre az adatok helyreállíthatóak. Az Azure Storage RPO-ja általában kevesebb, mint 15 perc, bár jelenleg nincs SLA arra, hogy mennyi ideig tart az adatok replikálása a másodlagos régióba.

### <a name="geo-redundant-storage"></a>Georedundáns tárolás

A Georedundáns tárolás (GRS) az adatokat szinkron módon, az LRS használatával háromszor másolja le az elsődleges régió egy fizikai helyére. Ezután aszinkron módon átmásolja az adatokat egy másodlagos régió egyetlen fizikai helyére, amely több száz kilométerre van az elsődleges régiótól. A GRS legalább 99,999999999999999%-os (16 9-es) tartósságot biztosít az Azure Storage-adatobjektumok számára egy adott évben.

Az írási műveleteket a rendszer először az elsődleges helyre írja, és az LRS használatával replikálja. A rendszer ezután aszinkron módon replikálja a frissítést a másodlagos régióba. Amikor a rendszer a másodlagos helyre írja az adatokat, az LRS használatával ezen a helyen is replikálja az adatokat.

Az alábbi ábra az adatok GRS vagy RA-GRS replikációját mutatja be:

:::image type="content" source="media/storage-redundancy/geo-redundant-storage.png" alt-text="Az adatok GRS vagy RA-GRS segítségével való replikálási folyamatát bemutató ábra":::

### <a name="geo-zone-redundant-storage"></a>Zóna- és georedundáns tárolás

A geo-zónaredundáns tárolás (GZRS) a rendelkezésre állási zónák közötti redundancia által biztosított magas rendelkezésre állást ötvözi a georeplikáció által biztosított regionális kimaradásokkal szembeni védelemmel. A GZRS-tárfiókok adatait a rendszer az elsődleges régió három [Azure](../../availability-zones/az-overview.md) rendelkezésre állási zónájára másolja, és a regionális katasztrófák elleni védelem érdekében egy másodlagos földrajzi régióba is replikálja. A Microsoft a GZRS használatát javasolja a maximális konzisztenciát, tartósságot és rendelkezésre állást, kiváló teljesítményt és rugalmasságot igénylő alkalmazásokhoz a vészhelyreállításhoz.

A GZRS-tárfiókokkal továbbra is olvashatja és írhatja az adatokat, ha egy rendelkezésre állási zóna elérhetetlenné válik vagy nem állítható vissza. Emellett az adatok teljes regionális kimaradás vagy olyan katasztrófa esetén is tartósak, amelyben az elsődleges régió nem állítható helyre. A GZRS legalább 99,99999999999999999%-os (16 9-es) tartós objektumokat biztosít egy adott évben.

Az alábbi ábra bemutatja, hogyan replikálja az adatokat a rendszer a GZRS vagy az RA-GZRS segítségével:

:::image type="content" source="media/storage-redundancy/geo-zone-redundant-storage.png" alt-text="Az adatok GZRS-sel vagy RA-GZRS-sel való replikálási folyamatát bemutató ábra":::

Csak az általános célú v2-tárfiókok támogatják a GZRS-t és az RA-GZRS-t. További információ a tárfióktípusokról: [Az Azure Storage-fiók áttekintése.](storage-account-overview.md) A GZRS és az RA-GZRS támogatja a blokkblobokat, lapblobokat (kivéve a VHD-lemezeket), a fájlokat, a táblákat és az üzenetsorokat.

A GZRS és az RA-GZRS a következő régiókban támogatott:

- (Afrika) Dél-Afrika északi részén
- (Ázsia és a Csendes-óceáni térség) Kelet-Ázsia
- (Ázsia és a Csendes-óceáni térség) Délkelet-Ázsia
- (Ázsia és a Csendes-óceáni térség) Kelet-Ausztrália
- (Ázsia és a Csendes-óceáni térség) Közép-India
- (Ázsia és a Csendes-óceáni térség) Kelet-Japán
- (Ázsia és a Csendes-óceáni térség) Dél-Korea középső
- (Kanada) Közép-Kanada
- (Európa) Észak-Európa
- (Európa) Nyugat-Európa
- (Európa) Közép-Franciaország
- (Európa) Nyugat-Németország – Középső régió
- (Európa) Kelet-Kelet
- (Európa) Észak-Svájc
- (Európa) Egyesült Királyság déli régiója
- (Közel-Kelet) Egyesült Arab Emírségek északi iránya
- (Dél-Amerika) Dél-Brazília
- (USA) USA középső középső
- (US) USA keleti régiója
- (USA) USA 2. keleti régiója
- (USA) USA északi középső sarka
- (USA) USA déli középső sarka
- (USA) USA nyugati régiója
- (USA) USA 2. nyugati régiója

A díjszabásról a [blobok,](https://azure.microsoft.com/pricing/details/storage/blobs) [](https://azure.microsoft.com/pricing/details/storage/files/) [fájlok,](https://azure.microsoft.com/pricing/details/storage/queues/)üzenetsorok és táblák díjszabási részleteiben [olvashat.](https://azure.microsoft.com/pricing/details/storage/tables/)

## <a name="read-access-to-data-in-the-secondary-region"></a>Olvasási hozzáférés a másodlagos régióban található adatokhoz

A georedundáns tárolás (GRS vagy GZRS esetén) az adatokat a regionális kimaradásokkal szembeni védelem érdekében egy másik fizikai helyre replikálja a másodlagos régióban. Ezek az adatok azonban csak akkor olvashatók, ha az ügyfél vagy a Microsoft feladatátvételt kezdeményez az elsődleges régióból a másodlagosba. Ha engedélyezi az olvasási hozzáférést a másodlagos régióhoz, az adatok mindig olvashatók lesznek, olyan esetekben is, amikor az elsődleges régió elérhetetlenné válik. A másodlagos régióhoz való olvasási hozzáféréshez engedélyezze az írási hozzáférésű georedundáns tárolást (RA-GRS) vagy az írási hozzáférésű geo-zónaredundáns tárolást (RA-GZRS).

> [!NOTE]
> Azure Files nem támogatja az írási hozzáférésű georedundáns tárolást (RA-GRS) és az írási hozzáférésű geo-zónaredundáns tárolást (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Az alkalmazások megtervezése a másodlagoshoz való olvasási hozzáféréshez

Ha a tárfiók olvasási hozzáférésre van konfigurálva a másodlagos régióhoz, akkor úgy tervezheti meg az alkalmazásokat, hogy zökkenőmentesen váltson át a másodlagos régióból származó adatok olvasására, ha az elsődleges régió valamilyen okból elérhetetlenné válik. 

A másodlagos régió az RA-GRS vagy az RA-GZRS engedélyezése után olvasási hozzáféréssel is elérhető, így előre tesztelheti az alkalmazást, hogy biztosan megfelelően olvasson a másodlagos régióból szolgáltatáskimaradás esetén. További információ az alkalmazások magas rendelkezésre állásra tervezéséhez: Georedundania használata magas rendelkezésre állású [alkalmazások tervezéséhez.](geo-redundant-design.md)

Ha engedélyezve van a másodlagos adatbázis olvasási hozzáférése, az alkalmazás a másodlagos végpontról és az elsődleges végpontról is beolvasható. A másodlagos végpont hozzáfűzi a *–secondary* utótagot a fióknévhez. Ha például a Blob Storage elsődleges végpontja `myaccount.blob.core.windows.net` , akkor a másodlagos végpont a `myaccount-secondary.blob.core.windows.net` . A tárfiók fiókelérési kulcsai ugyanazok az elsődleges és a másodlagos végpontok esetén is.

### <a name="check-the-last-sync-time-property"></a>Az Utolsó szinkronizálás időpontja tulajdonság ellenőrzése

Mivel a rendszer aszinkron módon replikálja az adatokat a másodlagos régióba, a másodlagos régió gyakran az elsődleges régió mögött van. Ha hiba történik az elsődleges régióban, valószínű, hogy az elsődleges régióba végzett összes írás még nem lett replikálva a másodlagosba.

Annak megállapításához, hogy mely írási műveletek replikálva vannak  a másodlagos régióba, az alkalmazás ellenőrizheti a tárfiók Utolsó szinkronizálás ideje tulajdonságát. Az elsődleges régióba az utolsó szinkronizálási időpont előtt írt összes írási művelet sikeresen replikálva lett a másodlagos régióba, ami azt jelenti, hogy a másodlagos régióból beolvashatók. Az utolsó szinkronizálási időpont után az elsődleges régióba írt írási műveletek esetleg nem replikálódnak a másodlagos régióba, ami azt jelenti, hogy nem érhetők el olvasási műveletekhez.

A Legutóbbi szinkronizálás ideje  tulajdonság értékét az Azure PowerShell, az Azure CLI vagy az Egyik Azure Storage-ügyfélkódtár használatával is lekérdezheti. Az **Utolsó szinkronizálás időpontja** tulajdonság egy GMT dátum-/időérték. További információ: A tárfiók utolsó szinkronizálási ideje [tulajdonság ellenőrzése.](last-sync-time-get.md)

## <a name="summary-of-redundancy-options"></a>A redundanciabeállítások összefoglalása

Az alábbi szakaszok az Azure Storage-hoz elérhető redundanciabeállításokat foglalják össze.

### <a name="durability-and-availability-parameters"></a>Tartóssági és rendelkezésre állási paraméterek

Az alábbi táblázat ismerteti az egyes redundancia-beállítások fő paramétereit:

| Paraméter | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Objektumok tartósságának százalékos aránya egy adott évben | legalább 99,9999999999%-os (11 9-es) | legalább 99,99999999999%-os (12 9-es) | legalább 99,9999999999999%-os (16 9-es) | legalább 99,9999999999999%-os (16 9-es) |
| Olvasási kérelmek rendelkezésre állása | Legalább 99,9% (99% a elérésű hozzáférési szint esetében) | Legalább 99,9% (99% a elérésű hozzáférési szint esetében) | Legalább 99,9% (99% a elérésű hozzáférési szint esetében) GRS-hez<br /><br />Legalább 99,99% (99,9% a ritka elérésű hozzáférési szint esetében) az RA-GRS esetében | GZRS esetében legalább 99,9% (99% a elérésű hozzáférési szint esetében)<br /><br />Legalább 99,99% (99,9% a ritka elérésű hozzáférési szint esetében) az RA-GZRS esetében |
| Írási kérések rendelkezésre állása | Legalább 99,9% (99% a elérésű hozzáférési szint esetében) | Legalább 99,9% (99% a elérésű hozzáférési szint esetében) | Legalább 99,9% (99% a elérésű hozzáférési szint esetében) | Legalább 99,9% (99% a elérésű hozzáférési szint esetében) |
| A különálló csomópontokon fenntartott adatmásolatok száma | Három példány egy régión belül | Három példány különálló rendelkezésre állási zónákban egy régión belül | Összesen hat másolat, köztük három az elsődleges régióban és három a másodlagos régióban | Összesen hat példány, beleértve három példányt az elsődleges régió különböző rendelkezésre állási zónáiban, valamint három helyileg redundáns példányt a másodlagos régióban |

### <a name="durability-and-availability-by-outage-scenario"></a>Tartósság és rendelkezésre állás szolgáltatáskimaradási forgatókönyv szerint

Az alábbi táblázat azt mutatja, hogy az adatok tartósak és elérhetők-e egy adott forgatókönyvben attól függően, hogy a tárfiók milyen típusú redundanciát biztosít:

| Kimaradás esete | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Az adatközpontban található csomópontok elérhetetlenné válnak | Igen | Igen | Igen | Yes |
| Egy teljes adatközpont (zóna vagy nem zóna) elérhetetlenné válik | Nem | Igen | Igen<sup>1</sup> | Yes |
| Az egész régióra kiterjedő kimaradás történik az elsődleges régióban | Nem | Nem | Igen<sup>1</sup> | Igen<sup>1</sup> |
| A másodlagos régió olvasási hozzáférése akkor érhető el, ha az elsődleges régió elérhetetlenné válik | Nem | Nem | Igen (RA-GRS-sel) | Igen (RA-GZRS-sel) |

<sup>1</sup> Ha az elsődleges régió elérhetetlenné válik, a fiók feladatátvétele szükséges az írási rendelkezésre állás helyreállításához. További információ: [Vészhelyreállítás és tárfiók feladatátvétele.](storage-disaster-recovery-guidance.md)

### <a name="supported-azure-storage-services"></a>Támogatott Azure Storage-szolgáltatások

Az alábbi táblázat az egyes Azure Storage-szolgáltatások által támogatott redundanciabeállításokat mutatja be.

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br />Felügyelt Azure-lemezek | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br /> | Blob Storage<br />Queue Storage<br />Table Storage<br />Azure Files<br /> |

### <a name="supported-storage-account-types"></a>Támogatott tárfióktípusok

Az alábbi táblázat bemutatja, hogy az egyes tárfiókok mely redundanciabeállításokat támogatják. További információ a tárfióktípusokról: [Tárfiókok áttekintése.](storage-account-overview.md)

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Általános célú v2<br /> Általános célú v1<br /> BlockBlobStorage<br /> BlobStorage<br /> FileStorage (Fájltartó) | Általános célú v2<br /> BlockBlobStorage<br /> FileStorage (Fájltartó) | Általános célú v2<br /> Általános célú v1<br /> BlobStorage | Általános célú v2 |

A rendszer az összes tárfiók összes adatát átmásolja a tárfiók redundancia-beállításának megfelelően. A rendszer olyan objektumokat másol, mint a blokkblobok, hozzáfűző blobok, lapblobok, üzenetsorok, táblák és fájlok. A rendszer minden szinten, így az archív szinten is másolja az adatokat. A blobrétegekkel kapcsolatos további információkért lásd: [Azure Blob Storage: hot, cool, and archive access tiers (Azure Blob Storage:](../blobs/storage-blob-storage-tiers.md)a gyors, a elérésű és az archív hozzáférési szintek).

Az egyes redundancia-lehetőségek díjszabásával kapcsolatos információkért lásd: [Az Azure Storage díjszabása.](https://azure.microsoft.com/pricing/details/storage/)

> [!NOTE]
> Az Azure Premium Disk Storage jelenleg csak a helyileg redundáns tárolást (LRS) támogatja. A blokkblob-tárfiókok támogatják a helyileg redundáns tárolást (LRS) és a zónaredundáns tárolást (ZRS) bizonyos régiókban.

## <a name="data-integrity"></a>Adatintegritás

Az Azure Storage rendszeresen ellenőrzi a ciklikus redundancia-ellenőrzések (CRC-k) használatával tárolt adatok integritását. Ha a rendszer adatsérülést észlel, redundáns adatokkal javítja. Az Azure Storage emellett kiszámítja az összes hálózati forgalom ellenőrző összegét, hogy észlelje az adatcsomagok sérülését az adatok tárolásakor vagy leolvasásakor.

## <a name="see-also"></a>Lásd még

- [Tárfiók utolsó szinkronizálási ideje tulajdonságának ellenőrzése](last-sync-time-get.md)
- [Tárfiók redundancia-beállításának módosítása](redundancy-migration.md)
- [Georedundania használata magas rendelkezésre állású alkalmazások tervezéséhez](geo-redundant-design.md)
- [Vészhelyreállítás és tárfiók feladatátvétele](storage-disaster-recovery-guidance.md)
