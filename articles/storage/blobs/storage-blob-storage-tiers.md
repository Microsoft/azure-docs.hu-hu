---
title: Hozzáférési szintek Azure Blob Storage – gyakori, ritka elérésű és archív
description: Az Azure Blob Storage gyakori, ritka elérésű és archív hozzáférési szintjeiről olvashat. Tekintse át a rétegek használatát támogató Storage-fiókokat.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: klaasl
ms.openlocfilehash: 1a1cb8e1676405cbfbb3f4f61c86d8136b688b88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656838"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Hozzáférési szintek Azure Blob Storage – gyakori, ritka elérésű és archív

Az Azure Storage különböző hozzáférési szinteket kínál, így a legtöbb költséghatékony módon tárolhatja a blob-objektumokat. Az elérhető hozzáférési szintek a következők:

-  A gyakran használt adatok tárolására optimalizált.
-  A ritkán használt és a legalább 30 napig tárolt adatok tárolására optimalizált.
- A ritkán használt adatok tárolására optimalizált **archiválás** – legalább 180 napig, a rugalmas késési követelményekkel együtt, az órák sorrendjében.

Az egyes tárolási rétegek esetén a következő szempontokat érdemes figyelembe venni:

- A hozzáférési szintet a feltöltés során vagy azt követően lehet beállítani a blobban.
- Fiókszinten csak a ritka és a gyakori elérésű tárolási szint állítható be. Az archív hozzáférési szint csak a blob szintjén állítható be.
- A lassú elérési szinten lévő adatok valamivel alacsonyabb rendelkezésre állást biztosítanak, de továbbra is magas tartóssági, lekérési késéssel és átviteli teljesítménnyel rendelkeznek, hasonlóan a gyors adatokhoz. A ritkán használt adatok esetében a valamivel alacsonyabb rendelkezésre állás és a magasabb hozzáférési költségek elfogadható kompromisszumot jelentenek a nagy mennyiségű adattárolási költségekhez képest. További információt a [tárolók rendelkezésreállási szolgáltatási szintjeit](https://azure.microsoft.com/support/legal/sla/storage/v1_5/) ismertető szakaszban talál.
- Az archív hozzáférési szintben tárolt adatok a kapcsolat nélküli állapotban tárolódnak. Az archiválási szint a legalacsonyabb tárolási költségeket, de a legmagasabb hozzáférési költségeket és késést is biztosítja.
- A gyakori és a ritka elérésű szintek támogatják az összes redundancia-beállítást. Az archiválási szint csak a LRS, a GRS és az RA-GRS használatát támogatja.
- Az adattárolási korlátokat a fiók szintjén, nem pedig hozzáférési szinten kell megadni. Választhat, hogy az összes korlátot egy vagy mindhárom szinten használja-e.

A felhőben tárolt adatmennyiség exponenciális ütemben növekszik. A növekvő tárolási szükségletek költségeinek kezelése érdekében hasznos lehet az adatokat olyan attribútumok alapján szervezni, mint a hozzáférés gyakorisága vagy a tervezett megőrzési időtartam, így optimalizálhatók a költségek. A felhőben tárolt adatok különbözhetnek keletkezésük és feldolgozásuk módjában, valamint előfordulhat, hogy élettartamuk különböző szakaszaiban különböző módon kell elérni őket. Egyes adatokat aktívan használnak és módosítanak teljes élettartamuk során. Egyes adatokat élettartamuk korai szakaszában sokat használnak, az adatok életkorának növekedésével azonban a hozzáférések mennyisége drasztikusan csökken. Egyes felhőbeli adatok inaktívak maradnak, és tárolásuk során ritkán, vagy akár sohasem használják őket.

Az adathozzáférési forgatókönyvek mindegyike egy másik hozzáférési szinten érhető el, amely egy adott hozzáférési mintára van optimalizálva. A gyakori, ritka elérésű és archív hozzáférési szintekkel az Azure Blob Storage különböző díjszabási modellel rendelkező, differenciált hozzáférési szintekre van szükség.

A következő eszközök és ügyféloldali kódtárak támogatják a blob szintű réteg-és archiválási tárterületet.

- Azure Portal
- PowerShell
- Azure CLI-eszközök
- .NET-ügyfélkódtár
- Java-ügyfélkódtár
- Python-ügyfélkódtár
- Node.js ügyféloldali kódtár

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Rétegezést támogató Storage-fiókok

Blob Storage és általános célú v2-(GPv2-) fiókok esetében az objektum tárolási adatrétege a gyakori, a lassú és az archív adatok között támogatott. A általános célú v1-(GPv1-) fiókok nem támogatják a rétegek használatát. A meglévő GPv1 egyszerűen átalakíthatja, vagy Blob Storage fiókokat, hogy GPv2 a fiókokat a Azure Portal. A GPv2 új díjszabást és funkciókat biztosít a blobokhoz, fájlokhoz és várólistákhoz. Bizonyos funkciók és árak csökkentése csak GPv2-fiókokban vehető igénybe. Bizonyos számítási feladatok drágábbak lehetnek a GPv2, mint a GPv1. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.

A Blob Storage és a GPv2 fiók szintjén elérhetők a **hozzáférési szint** attribútuma. Ez az attribútum lehetővé teszi az alapértelmezett hozzáférési szint megadását minden olyan blob esetében, amely nem kifejezetten az objektum szintjén van beállítva. A explicit módon beállított szintű objektumok esetében a fiók szintje nem érvényes. Az archiválási szint csak az objektum szintjén alkalmazható. Bármikor válthat a hozzáférési szintek között.

Az GPv2 használata a rétegek közötti Blob Storage fiókok helyett. A GPv2 támogatja az összes olyan funkciót, Blob Storage a fiókok támogatása, és még sok más. A Blob Storage és a GPv2 közötti díjszabás majdnem azonos, de az új funkciók és az árak csökkentése csak a GPv2-fiókokon érhető el.

A GPv1- és GPv2-fiókok díjszabási struktúrája eltér egymástól, ezért az ügyfeleknek érdemes mindkettőt alaposan áttekinteni, mielőtt a GPv2-fiókok használata mellett döntenek. Meglévő Blob Storage- vagy GPv1-fiókját könnyedén, egyetlen kattintással átalakíthatja GPv2-fiókká. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.

## <a name="hot-access-tier"></a>Gyakori elérésű tárolási szint

A gyakori elérésű hozzáférési szint tárolási költségei magasabbak, mint a ritka és az archív szinté, viszont ennek a legalacsonyabbak az elérési költségei. Példa használati forgatókönyvek a gyakori hozzáférési szinthez:

- Az aktív használatban lévő adatok, illetve a várhatóan gyakran olvashatók és írhatók
- A lassú elérési szintre történő feldolgozásra és az esetleges áttelepítésre előkészített adatok

## <a name="cool-access-tier"></a>Ritka elérésű tárolási szint

A ritka hozzáférési szintnek a gyakori elérés tároláshoz képest alacsonyabbak a tárolási költségei, és magasabbak az elérési költségei. Ezen a szinten olyan adatokat érdemes tárolni, amelyek legalább 30 napig maradnak a ritka elérésű szinten. Példa használati forgatókönyvek a ritka hozzáférési szinthez:

- Rövid távú biztonsági mentés és vész-helyreállítás
- A régebbi adatértékek nem használatosak gyakran, de várhatóan azonnal elérhetővé válnak a hozzáférés során
- Nagyméretű adatkészletek, amelyeket költséghatékonyan kell tárolni, míg a későbbi feldolgozáshoz több adat gyűlik össze

## <a name="archive-access-tier"></a>Archív tárolási szint

Az archív hozzáférési szint a legalacsonyabb tárterülettel rendelkezik, de a gyors és a lassú elérésű szintekhez képest nagyobb Adatlekérdezési költségekkel jár. Az adatoknak az archiválási szinten kell maradniuk legalább 180 napig, vagy egy korai törlési díjat kell fizetni. Az archiválási szinten lévő adatok több órát is igénybe vehetnek a megadott rehidratáló prioritástól függően. Kisméretű objektumok esetében a magas prioritású rehidratálás egy óra alatt lekérheti az objektumot az archív verzióról. További információért lásd [a blob-adatok rehidratálása az archív szintről](storage-blob-rehydration.md) című témakört.

Míg a Blobok archiválási tárolóban találhatók, a blob-adatok offline állapotban vannak, és nem olvashatók és nem módosíthatók. Az archívumban található Blobok olvasásához vagy letöltéséhez először egy online szintre kell kiszáradni. Az archív tárolóban nem lehet pillanatképeket készíteni egy blobról. A blob metaadatai azonban online és elérhető állapotban maradnak, így a blob, a tulajdonságai, a metaadatok és a blob-index címkéi is kilistázható. A blob metaadatainak beállítása vagy módosítása az archiválás során nem engedélyezett. Azonban beállíthatja és módosíthatja a blob-index címkéit. Az archívumban található Blobok esetében az egyetlen érvényes művelet a [blob tulajdonságai](/rest/api/storageservices/get-blob-properties), a [blob metaadatainak beolvasása](/rest/api/storageservices/get-blob-metadata), a Blobok [beállítása](/rest/api/storageservices/set-blob-tags), a [Blobok lekérése](/rest/api/storageservices/get-blob-tags), a Blobok [megkeresése címkék alapján](/rest/api/storageservices/find-blobs-by-tags), [a Blobok](/rest/api/storageservices/set-blob-tier) [listázása](/rest/api/storageservices/list-blobs), a Blobok [másolása](/rest/api/storageservices/copy-blob)és a Blobok [törlése](/rest/api/storageservices/delete-blob).

Az archív hozzáférési szint használati forgatókönyvei például a következők:

- Hosszú távú biztonsági mentések, másodlagos biztonsági mentések és archiválási adatkészletek
- Az eredeti (nyers) adatmegőrzési érték, amelyet még azután is meg kell őrizni, hogy a rendszer véglegesen felhasználható formában dolgozza fel
- A megfelelőségi és archiválási adatai, amelyeket hosszú ideig kell tárolni, és szinte soha nem fér hozzá

> [!NOTE]
> Az archiválási szint ZRS, GZRS vagy RA-GZRS fiókok esetén nem támogatott. A LRS-ről GRS-re való Migrálás nem támogatott, ha a Storage-fiók blobokat tartalmaz az archiválási szinten.

## <a name="account-level-tiering"></a>Fiók szintű rétegek

A Blobok mind a három hozzáférési szinten egyszerre létezhetnek ugyanabban a fiókban. Minden olyan blob, amely nem rendelkezik explicit módon hozzárendelt szintjével, a fiók hozzáférési szintje beállításból kikövetkezteti a szintet. Ha a hozzáférési szintet a fiókból származik, a **hozzáférési rétegek kikövetkeztetett** blob tulajdonsága "true" értékre van állítva, a **hozzáférési szintű** blob tulajdonság pedig megegyezik a fiók szintjével. A Azure Portal a _hozzáférési réteg kikövetkeztetett_ tulajdonsága a blob-hozzáférési szinttel jelenik meg, mint a **gyors (késleltetett)** vagy a ritka **(késleltetett)**.

A fiók hozzáférési rétegének módosítása a fiókban tárolt összes olyan _hozzáférési rétegre_ vonatkozik, amely nem rendelkezik explicit szintű készlettel. Ha bekapcsolja a fiók rétegét a melegről a lassúra, akkor az írási műveletek (10 000) után az összes blobra vonatkozóan csak a GPv2-fiókokat tartalmazó set szint használata után kell fizetnie. Blob Storage fiókokban ez a változás díjmentes. Az olvasási műveletekért (10 000) és az adatok lekéréséhez (GB-onként) kell fizetnie, ha a lassúról a gyakori elérésű Blob Storage vagy GPv2-fiókokra vált.

Csak a gyakori és ritka elérésű hozzáférési szintek állíthatók be alapértelmezett fiók-hozzáférési szintként. Az archív szint csak az objektumok szintjén állítható be. A blob feltöltésekor megadhatja, hogy az alapértelmezett fiók szintjétől függetlenül milyen hozzáférési réteg legyen gyors, lassú vagy archív. Ez a funkció lehetővé teszi, hogy közvetlenül az Archívum szintjére írja az adatait, így az adatok a blob Storage-ban való létrehozása pillanatában költségmegtakarítást eredményezhet.

## <a name="blob-level-tiering"></a>Blobszintű rétegezés

A blob szintű rétegek lehetővé teszik az adatok feltöltését az Ön által választott hozzáférési szintre a Blobok [elhelyezése](/rest/api/storageservices/put-blob) vagy a [letiltási lista](/rest/api/storageservices/put-block-list) műveleteivel, és az adatok rétegét az objektum szintjén módosíthatja a [blob szint beállítása](/rest/api/storageservices/set-blob-tier) művelet vagy az [életciklus-kezelési](#blob-lifecycle-management) funkció használatával. A szükséges hozzáférési szinthez feltöltheti az adatokat, majd a használati minták változása révén egyszerűen módosíthatja a blob-hozzáférési szintet a gyakori, ritka vagy archív rétegek között, anélkül, hogy az adatokat át kellene helyeznie a fiókok között. Az összes rétegbeli módosítási kérelem azonnal megtörténik, és a gyors és a lassú elérési szint közötti változások azonnaliek. A Blobok archiválási szintről való rehidratálása több órát is igénybe vehet.

Az utolsó blobszint-módosítás időpontja a **Hozzáférési szint utolsó módosítása** blobtulajdonságon keresztül érhető el. A **hozzáférési szint változási ideje** egy blob szintű tulajdonság, amely nem frissül, ha módosul az alapértelmezett fiók szintje. A fiók tulajdonságai és a blob tulajdonságai külön vannak elkülönítve. Ha a fiók alapértelmezett hozzáférési szintje megváltozik, a rendszer nem fogja tudni frissíteni a **hozzáférési szintet** a Storage-fiók minden blobján.

Amikor egy blobot felülír a gyakori vagy a ritka elérésű rétegben, az újonnan létrehozott blob örökli a blob azon rétegét, amelyet a rendszer felülírt, kivéve, ha az új blob-hozzáférési szintet explicit módon beállította a létrehozáshoz. Ha egy blob az archiválási szinten van, akkor nem lehet felülírni, ezért a blob feltöltése nem engedélyezett ebben a forgatókönyvben.

> [!NOTE]
> Az archív tárolás és a blobszintű rétegezés csak a blokkblobokat támogatja.

### <a name="blob-lifecycle-management"></a>BLOB-életciklus kezelése

A blob Storage életciklus-kezelése sokoldalú, szabályon alapuló szabályzatot kínál, amellyel átválthatja az adatait a legjobb hozzáférési rétegre, és lemondhatja az adatait az életciklusa végén. További információért lásd: [Az Azure Blob Storage hozzáférési szintjeinek automatizálásával kapcsolatos költségek optimalizálása](storage-lifecycle-management-concepts.md) .

> [!NOTE]
> A blokk blob Storage-fiókban (prémium szintű teljesítmény) tárolt adatok jelenleg nem állíthatók be gyors, ritka elérésű vagy archív verzióra a [blob-réteg beállítása](/rest/api/storageservices/set-blob-tier) vagy az Azure Blob Storage életciklus-kezelés használatával.
> Az adatok áthelyezéséhez a blob Storage-fiókból egy másik fiókban lévő, a [put blokk URL API](/rest/api/storageservices/put-block-from-url) -val vagy az API-t támogató AzCopy-verzióval szinkronban kell átmásolni a blobokat a blokkból.
> A **put blokk from URL** API szinkron módon másolja az adatait a-kiszolgálón, ami azt jelenti, hogy a hívás csak akkor fejeződik be, ha az összes adatok átkerülnek az eredeti kiszolgáló helyéről a célhelyre.

### <a name="blob-level-tiering-billing"></a>Blobszintű rétegezési számlázás

Ha egy blobot a rétegek között töltenek fel vagy helyeznek át, akkor a feltöltés vagy a rétegek változásakor a megfelelő díjszabás alapján kell fizetni.

Ha egy blobot egy hűvösebb rétegbe helyez át (a gyors >a lassú elérésű, a gyors >az archívumot vagy a lassú elérésű >archívumot), a művelet a célként megadott szintre írási műveletként történik, ahol az írási művelet (10 000) és a célként megadott adatírás (GB-onként) érvényes.

Ha egy blobot egy melegebb rétegbe helyez át (archivált >lassú elérésű, archív >gyors vagy lassú elérésű >), a művelet számlázása a forrás rétegből történik, ahol az olvasási művelet (10 000) és a forrás szint (GB-onként) Adatlekérdezési díja érvényes. A ritka elérésű vagy archív szintről kihelyezett Blobok [korai törlési](#cool-and-archive-early-deletion) díjai is érvényesek. [Az archiválási rétegből származó adatok újraszárítása](storage-blob-rehydration.md) időt vesz igénybe, és az adatok az archiválási árakon lesznek felszámítva, amíg az adatok online állapotba nem állnak vissza, és a blob réteg gyakran vagy ritkán változik.

A következő táblázat összefoglalja, hogyan történik a szintek változásainak számlázása.

| | **Írási díjak (művelet + hozzáférés)** | **Olvasási díjak (művelet + hozzáférés)** |
| ---- | ----- | ----- |
| **Set Blob Tier** | gyors ><br> gyors > Archívum<br> Cool-> Archívum | Archívum – ><br> archiválás – > gyors<br> Cool > – gyors

### <a name="cool-and-archive-early-deletion"></a>Korai törlés a ritka elérésű és az archív szinten

A lassú rétegbe áthelyezett Blobok (csak a GPv2-fiókok esetében) egy 30 napos, korai törlési időszakra vonatkoznak. Az archiválási szintre áthelyezett Blobok egy 180 napos korai törlési időszakra vonatkoznak. A díj számlázása időarányosan történik. Ha például egy blobot az archívumba helyez át, majd 45 nap után törli vagy áthelyezi a gyors elérésű szintre, akkor a blobnak az archívumban való tárolásával megegyező korai törlési díjat kell fizetnie a 135 (180 mínusz 45) napon.

A ritka elérésű és az archív rétegek közötti váltás néhány részletet mutat be:

1. Ha egy blobot a Storage-fiók alapértelmezett hozzáférési szintje alapján, a blobot pedig archívumba helyezik el, a rendszer nem veszi figyelembe a korai törlés díját.
1. Ha a blobot explicit módon áthelyezi a lassú szintre, majd áthelyezi az archívumba, a rendszer a korai törlési díjat alkalmazza.

Kiszámítja a korai törlési időt az **utolsó módosítású** blob tulajdonság használatával, ha nem módosult a hozzáférési réteg. Ellenkező esetben akkor használja, ha a hozzáférési réteg utolsó módosítását a (z) a következő blob tulajdonság megtekintésével használta: **hozzáférés-réteg-változási idő**. További információ a blob tulajdonságairól: [blob tulajdonságainak beolvasása](/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>A blob Storage-beli blokkolási beállításainak összehasonlítása

Az alábbi táblázat a prémium szintű, a blob Storage és a gyakori, ritka elérésű és archív hozzáférési szintek összehasonlítását mutatja be.

|                                           | **Prémium szintű teljesítmény**   | **Gyors elérési szint** | **Hűvös szint**       | **Archiválási szint**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Rendelkezésre állás**                          | 99.9%                     | 99.9%        | 99%                 | Offline           |
| **Rendelkezésre állás** <br> **(RA-GRS olvasások)**  | N/A                       | 99.99%       | 99.9%               | Offline           |
| **Használati díjak**                         | Magasabb tárolási költségek, alacsonyabb hozzáférés és tranzakciós költségek | Magasabb tárolási költségek, alacsonyabb hozzáférés és tranzakciós költségek | Alacsonyabb tárolási költségek, magasabb hozzáférési és tranzakciós költségek | Legalacsonyabb tárolási költségek, legmagasabb hozzáférési és tranzakciós költségek |
| **Minimális tárolási időtartam**              | N.A.                       | N.A.          | 30 nap<sup>1</sup> | 180 nap
| **Késés** <br> **(az első bájtig eltelt idő)** | Egy számjegyű ezredmásodperc | ezredmásodperc | ezredmásodperc        | óra<sup>2</sup> |

<sup>1</sup> a GPv2-fiókok ritka elérési szintjében lévő objektumok minimális megőrzési időtartama 30 nap. Blob Storage fiókok nem rendelkeznek minimális megőrzési időtartammal a ritka elérési szinthez.

<sup>2</sup> Archive Storage jelenleg két, magas és standard szintű, a lekérési késést és a költségeket biztosító, különböző rehidratáló prioritásokat támogat. További információkért lásd: [Blobok adatainak rehidratálása az archív szintről](storage-blob-rehydration.md).

> [!NOTE]
> Blob Storage-fiókok ugyanazt a teljesítmény-és méretezhetőségi célokat támogatják, mint az általános célú v2 Storage-fiókok. További információ: [blob Storage méretezhetőségi és teljesítménybeli céljai](scalability-targets.md).

## <a name="pricing-and-billing"></a>Árak és számlázás

Az összes Storage-fiók díjszabási modellt használ az egyes Blobok szintjétől függően a blob Storage blokkolásához. Tartsa szem előtt az alábbi számlázási szempontokat:

- **Tárolási költségek**: Az adattárolás díja a tárolt adatok mennyisége mellett a hozzáférési rétegtől függ. A gigabájtonkénti költség csökken, ha a szint ritkábban használt adatokat tárol.
- **Adathozzáférési költségek**: az adathozzáférési költségek emelkednek, ha a szint ritkábban használt adatokat tárol. A lassú elérésű és az archív hozzáférési szinten tárolt adatokért a GB-os adatelérési díjat számítjuk fel a beolvasáshoz.
- **Tranzakciós költségek**: az összes olyan réteg esetében, amely növeli a szintet, megnövekszik a tranzakciós díj.
- **Földrajzi replikálási adatátviteli költségek**: Ez a díj csak a Geo-replikációval konfigurált fiókokra vonatkozik, beleértve a GRS és az ra-GRS. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.
- **Kimenő adatátviteli költségek**: A kimenő adatátvitel (azaz az adott Azure-régióból kivitt adatok) esetében gigabájtalapú sávszélesség-használati díjak lépnek fel, csakúgy, mint az általános célú tárfiókok esetében.
- **A hozzáférési réteg módosítása**: a fiók hozzáférési rétegének módosítása azt eredményezi, hogy az egyes Blobok nem rendelkeznek explicit szintű készlettel. Az egyes Blobok hozzáférési szintjének módosításával kapcsolatos információkért lásd: [blob szintű szintek számlázása](#blob-level-tiering-billing).

    A blob hozzáférési rétegének módosítása a verziószámozás engedélyezésekor, vagy ha a blob rendelkezik pillanatképekkel, további díjakat eredményezhet. További információ az engedélyezett verziószámozású blobokról: [díjszabás és számlázás](versioning-overview.md#pricing-and-billing) a blob verziószámozási dokumentációjában. A pillanatképekkel rendelkező Blobokkal kapcsolatos információkért lásd: [díjszabás és számlázás](snapshots-overview.md#pricing-and-billing) a blob-Pillanatképek dokumentációjában.

> [!NOTE]
> A blokkos Blobok díjszabásával kapcsolatos további információkért lásd a [Blobok díjszabásának blokkolása](https://azure.microsoft.com/pricing/details/storage/blobs/)című témakört. A kimenő adatátviteli díjakkal kapcsolatos további információkért lásd a [sávszélesség-díjszabás részleteit](https://azure.microsoft.com/pricing/details/bandwidth/) ismertető oldalt.

## <a name="availability"></a>Rendelkezésre állás

A különböző hozzáférési szintek, valamint a blob szintű rétegek a kiválasztott régiókban érhetők el. A teljes listát a [Régiónként elérhető Azure-termékek](https://azure.microsoft.com/global-infrastructure/services/?products=storage) című részben tekintheti meg.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan kezelheti a blobokat és a fiókokat a hozzáférési rétegek között.

- [Blobok szintjeinek kezelése Azure Storage-fiókban](manage-access-tier.md)
- [Azure Storage-fiók alapértelmezett fiók-hozzáférési szintjeinek kezelése](../common/manage-account-default-access-tier.md)
- [A költségek optimalizálása az Azure Blob Storage hozzáférési szintjeinek automatizálásával](storage-lifecycle-management-concepts.md)
