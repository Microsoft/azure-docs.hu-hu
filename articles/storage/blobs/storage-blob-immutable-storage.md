---
title: Nem változtatható blob Storage – Azure Storage
description: Az Azure Storage szolgáltatás a blob (Object) tároló számára biztosít féreg (egyszer írható, olvasható) támogatást, amely lehetővé teszi a felhasználók számára, hogy a megadott intervallumban nem törölhető, nem módosítható állapotban tárolja az adattárolást.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 8d04d1bd758480ec33a7480e4045d28ed750f22e
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430938"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Az üzlet szempontjából létfontosságú blobadatok tárolása nem módosítható tárolással

Az Azure Blob Storage nem módosítható tárolója lehetővé teszi, hogy a felhasználók üzleti szempontból kritikus fontosságú adatobjektumokat tároljanak egy FÉREGben (egyszer írható, olvasható) állapot. Ez az állapot a felhasználó által megadott intervallumban nem törölhető és nem módosítható adatvesztést tesz lehetővé. A megőrzési időtartam időtartama alatt a Blobok létrehozhatók és olvashatók, de nem módosíthatók és nem törölhetők. A nem módosítható tárterület az összes Azure-régióban általános célú v1-, általános célú v2-, BlobStorage-és BlockBlobStorage-fiókokhoz érhető el.

A jogcímek beállításával és törlésével, illetve a Azure Portal, a PowerShell vagy az Azure CLI használatával történő időalapú adatmegőrzési szabályzat létrehozásával kapcsolatos további információkért lásd: [módosíthatatlansági házirendek beállítása és kezelése a blob Storage](storage-blob-immutability-policies-manage.md)-hoz.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="about-immutable-blob-storage"></a>A nem változtatható blob Storage-ról

A nem módosítható tárterület segíti az egészségügyi szervezetet, a pénzügyi intézményeket és a kapcsolódó iparágakat, &mdash; különösen a közvetítő-kereskedő szervezeteket &mdash; az adattárolás biztonságos tárolásához. A nem módosítható tárolók bármilyen forgatókönyvben is kihasználhatók a kritikus fontosságú adatmódosítások vagy Törlés elleni védelemhez.

Jellemző alkalmazási területek:

- **Jogszabályi megfelelőség**: az Azure Blob Storage-hoz tartozó nem módosítható tárterület segíti a szervezeteket a SEC 17a-4 (f), a CFTC 1.31 (d), a FINRA és más rendeletek esetében. A Cohasset által használt technikai tanulmány részletesen ismerteti, hogyan kezeli ezeket a szabályozási követelményeket a [Microsoft szolgáltatás megbízhatósági portálján](https://aka.ms/AzureWormStorage)keresztül. A [Azure biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter/compliance/compliance-overview) részletes információkat tartalmaz a megfelelőségi tanúsítványokról.

- **Biztonságos dokumentumok megőrzése**: az Azure Blob Storage nem módosítható tárolója biztosítja, hogy az adatok bármely felhasználó számára nem módosíthatók vagy törölhetők, beleértve a fiók rendszergazdai jogosultságokkal rendelkező felhasználókat is.

- **Jogi szabályozás**: az Azure Blob Storage-hoz tartozó nem módosítható tárolás lehetővé teszi a felhasználók számára, hogy a megfelelő időtartamig a jogszabályi vagy üzleti felhasználás szempontjából kritikus fontosságú adatokat tároljanak, amíg a mentességet el nem távolítják. Ez a funkció nem korlátozódik kizárólag a jogi használati esetekre, hanem eseményvezérelt vagy vállalati zárolásra is gondolhat, ahol szükség van az adatvédelemre az esemény-eseményindítók vagy a vállalati szabályzatok alapján.

A nem módosítható tároló a következő funkciókat támogatja:

- **[Időalapú adatmegőrzési szabályzatok támogatása](#time-based-retention-policies)**: a felhasználók házirendeket állíthatnak be az adatok egy adott intervallumra való tárolásához. Időalapú adatmegőrzési szabályzat beállításakor a Blobok létrehozhatók és olvashatók, de nem módosíthatók és nem törölhetők. A megőrzési időszak lejárta után a blobokat törölheti, de nem lehet felülírni.

- **[Jogszabályi szabályzatok támogatása](#legal-holds)**: Ha a megőrzési időköz nem ismert, a felhasználók megállíthatják, hogy a jogi tartalékok a nem módosítható adatok tárolására szolgálnak.  Ha be van állítva egy jogszabályi szabályzat, a Blobok létrehozhatók és olvashatók, de nem módosíthatók és nem törölhetők. Minden jogi Hold egy felhasználó által definiált alfanumerikus címkével (például egy eset-AZONOSÍTÓval, az esemény nevével stb.) van társítva, amelyet azonosító sztringként használ. 

- **Minden blobszint támogatása**: A WORM-szabályzatok az Azure Blob Storage-szintektől függetlenek, és minden szintre vonatkoznak, legyen az ritka elérésű, gyakori elérésű vagy archív. Így a felhasználók a számítási feladatok szempontjából leginkább költségoptimalizált szinten tárolhatják adataikat, miközben továbbra sem tudják őket módosítani.

- **Tároló szintű konfiguráció**: a felhasználók időalapú adatmegőrzési szabályzatokat és a jogi megtartási címkéket is konfigurálhatják a tároló szintjén. A felhasználók egyszerű tárolószintű beállítások segítségével többek között létrehozhatnak és zárolhatnak időalapú adatmegőrzési szabályzatokat, meghosszabbíthatják a megőrzési időtartamot, és jogi célú visszatartást állíthatnak be vagy oldhatnak fel. Ezek a szabályzatok a tárolóban található összes blobra vonatkoznak, a meglévőkre és az újakra is. A HNS kompatibilis fiókok esetében ezek a házirendek a tároló összes könyvtárára is érvényesek.

- **Naplózási naplózás támogatása**: minden tároló tartalmaz egy házirend-naplót. Legfeljebb hét időalapú adatmegőrzési parancsot jelenít meg a zárolt időalapú adatmegőrzési házirendek esetében, és tartalmazza a felhasználói azonosítót, a parancs típusát, az időbélyegzőket és a megőrzési időt. A jogi célú visszatartások esetében a naplóbejegyzés tartalmazza a felhasználói azonosítót, a parancs típusát, az időbélyegeket és a jogi céllal történő zárolás címkéit. Ezt a naplót a házirend élettartama érdekében a SEC 17a-4 (f) szabályozási irányelveknek megfelelően megőrzi a rendszer. Az [Azure-tevékenység naplója](../../azure-monitor/essentials/platform-logs-overview.md) az összes vezérlő síkja tevékenységének átfogóbb naplóját jeleníti meg; az [Azure-erőforrás-naplók](../../azure-monitor/essentials/platform-logs-overview.md) engedélyezésekor az adatsík műveletei megmaradnak és láthatók. A felhasználó felelőssége, hogy gondoskodjon a naplók állandó tárolásáról, mivel szabályozási és egyéb célból is szükség lehet rájuk.

## <a name="how-it-works"></a>Működés

Az Azure Blob Storage nem módosítható tárolási funkciója két típusú WORM vagy nem módosítható szabályzatot támogat: az időalapú adatmegőrzést és a jogi célú visszatartást. Ha egy tárolón időalapú adatmegőrzési szabályzatot vagy jogi megtartást alkalmaz, az összes meglévő blob nem módosítható féreg állapotba kerül 30 másodpercnél kevesebb ideig. A házirend által védett tárolóba feltöltött összes új blob nem változtatható állapotba kerül. Ha az összes blob megváltoztathatatlan állapotban van, a rendszer megerősíti a megváltoztathatatlan házirendet, és a nem módosítható tárolóban lévő felülírási vagy törlési műveletek nem engedélyezettek. A HNS használatára képes fiók esetén a blobokat nem lehet átnevezni, vagy áthelyezni egy másik könyvtárba.

A tároló-és a Storage-fiók törlése szintén nem engedélyezett, ha a tárolóban olyan Blobok vannak, amelyeket jogi vagy zárolt időalapú szabályzat véd. A jogi tartási szabályzat a Blobok, tárolók és a Storage-fiókok törlésével szembeni védelmet nyújt. A feloldva és a zárolt időalapú házirendek is védelmet kapnak a Blobok törlésével a megadott ideig. A feloldva és a zárolt időalapú házirendek is csak akkor védik a tároló törlését, ha legalább egy blob létezik a tárolón belül. Csak a *zárolt* időalapú házirenddel rendelkező tároló fogja védeni a Storage-fiókok törlését; a zárolt időalapú házirendekkel rendelkező tárolók nem kínálnak a Storage-fiókok törlésének védelmét és megfelelőségét.

Az alábbi ábra azt mutatja be, hogy az időalapú adatmegőrzési szabályzatok és a jogi szabályok hogyan akadályozzák az írási és törlési műveleteket, amíg azok érvényben vannak.

:::image type="content" source="media/storage-blob-immutable-storage/worm-diagram.png" alt-text="Ábra, amely bemutatja, hogyan akadályozza meg az írási és törlési műveleteket az adatmegőrzési házirendek és a jogi szabályok":::

Az időalapú adatmegőrzési szabályzatok beállításával és zárolásával kapcsolatos további információkért lásd: [módosíthatatlansági-szabályzatok beállítása és kezelése a blob Storage-hoz](storage-blob-immutability-policies-manage.md).

## <a name="time-based-retention-policies"></a>Időalapú adatmegőrzési szabályzatok

> [!IMPORTANT]
> Az időalapú adatmegőrzési szabályzatot *zárolni* kell ahhoz, hogy a blob megfelelő, megváltoztathatatlan (írási és törlési) állapotban legyen a SEC 17a-4 (f) és más szabályozási megfelelőség esetében. Javasoljuk, hogy a szabályzatot ésszerű időn belül, általában 24 óránál rövidebb ideig zárolja. Az alkalmazott időalapú adatmegőrzési szabályzat kezdeti állapota *zárolva* van, így a zárolása előtt tesztelheti a szolgáltatást, és módosíthatja a szabályzatot. Míg a *zárolt* állapot módosíthatatlansági védelmet biztosít, a rövid távú szolgáltatásokra vonatkozó kísérletektől eltérő célra nem ajánlott a *feloldva* állapotot használni. 

Ha egy tárolón időalapú adatmegőrzési szabályt alkalmaz, a tárolóban lévő összes blob a *tényleges* megőrzési időtartamig nem változtatható állapotban marad. A Blobok tényleges megőrzési időtartama megegyezik a blob **létrehozási ideje** és a felhasználó által megadott megőrzési időtartam közötti különbséggel. Mivel a felhasználóknak lehetőségük van meghosszabbítani az adatmegőrzési időtartamot, a nem módosítható tárolás a felhasználó által legutóbb megadott adatmegőrzési időtartamot fogja használni a tényleges megőrzési időtartam kiszámolásakor.

Tegyük fel például, hogy egy felhasználó egy időalapú adatmegőrzési szabályzatot hoz létre öt év megőrzési időtartammal. A tárolóban meglévő blob, a _testblob1_ egy éve jött létre. tehát a _testblob1_ érvényes megőrzési ideje négy év. Amikor új blobot töltenek fel a tárolóba, a _testblob2_ érvényes megőrzési idő a létrehozástól számított öt év a _testblob2_.

A kinyitott időalapú adatmegőrzési szabályzat csak a szolgáltatások tesztelésére ajánlott, és a házirendet zárolni kell ahhoz, hogy meg lehessen felelni a SEC 17a-4 (f) és más szabályozási megfelelőségnek. Az időalapú adatmegőrzési szabályzat zárolása után a szabályzat nem távolítható el, és a tényleges megőrzési időtartam legfeljebb öt nő lehet.

Az adatmegőrzési szabályokra az alábbi korlátozások vonatkoznak:

- A Storage-fiók esetében a zárolt, időalapú, rögzített házirendekkel rendelkező tárolók maximális száma 10 000.
- A minimális megőrzési időköz 1 nap. A maximális érték 146 000 nap (400 év).
- Tároló esetén a zárolt időalapú megváltoztathatatlan házirendek megőrzési időtartamának meghosszabbítására szolgáló szerkesztési adatok maximális száma 5.
- Tároló esetén a zárolt szabályzatok számára legfeljebb hét időalapú adatmegőrzési szabály van megtartva.

### <a name="allow-protected-append-blobs-writes"></a>Védett hozzáfűző Blobok írásának engedélyezése

A hozzáfűző Blobok adatblokkokból állnak, és a naplózási és naplózási forgatókönyvekhez szükséges adathozzáfűzési műveletekhez vannak optimalizálva. A hozzáfűző Blobok csak az új blokkoknak a blob végéhez való hozzáadását teszik lehetővé. A módosíthatatlansági függetlenül a meglévő blokkok módosítása vagy törlése alapvetően nem engedélyezett a hozzáfűző blobokban. A Blobok hozzáfűzésével kapcsolatos további tudnivalókért tekintse meg [a hozzáfűző Blobok](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)című témakört.

Csak az időalapú adatmegőrzési házirendek rendelkeznek olyan `allowProtectedAppendWrites` beállítással, amely lehetővé teszi, hogy új blokkokat írjon egy hozzáfűzési blobhoz a módosíthatatlansági-védelem és a megfelelőség megőrzése mellett. Ha ez a beállítás engedélyezve van, létrehozhat egy hozzáfűzési blobot közvetlenül a szabályzat által védett tárolóban, és továbbra is hozzáadhat új adatblokkokat a meglévő hozzáfűzési Blobok végéhez a *AppendBlock* API használatával. Csak új blokkok vehetők fel, és minden meglévő blokk nem módosítható és nem törölhető. Az időmegőrzés módosíthatatlansági védelme továbbra is érvényes, így megelőzhető a hozzáfűző blob törlése, amíg a tényleges megőrzési időszak el nem telik. A beállítás engedélyezése nem befolyásolja a Blobok vagy módosíthatatlansági viselkedését.

Mivel ez a beállítás egy időalapú adatmegőrzési szabályzat részét képezi, a hozzáfűző Blobok továbbra is megváltoztathatatlan állapotban maradnak a *hatályos* megőrzési időtartam alatt. Mivel az új adatok hozzáfűzhető a hozzáfűző blob kezdeti létrehozása után, némi különbség van a megőrzési időszak meghatározásakor. A tényleges megőrzés a blob **utolsó módosításának** és a felhasználó által megadott megőrzési időtartamnak a különbsége. Hasonlóképpen, ha a megőrzési időtartam ki van bővítve, a nem módosítható tároló a felhasználó által megadott megőrzési időtartam legutóbbi értékét használja a tényleges megőrzési időtartam kiszámításához.

Tegyük fel például, hogy egy felhasználó létrehoz egy időalapú adatmegőrzési szabályzatot, amely `allowProtectedAppendWrites` engedélyezve van, és 90 nap megőrzési időtartammal rendelkezik. A tárolóban a _logblob1_-ben létrehozott hozzáfűző Blobok még ma is létrejönnek, az új naplók továbbra is a következő 10 napra lesznek hozzáadva a hozzáfűzési blobhoz; így a _logblob1_ érvényes megőrzési ideje 100 nap a mai naptól számítva (az utolsó hozzáfűzés + 90 nap időpontjában).

A zárolt időalapú adatmegőrzési házirendek lehetővé teszik a `allowProtectedAppendWrites` beállítás engedélyezését és letiltását. Az időalapú adatmegőrzési szabályzat zárolása után a `allowProtectedAppendWrites` beállítás nem módosítható.

A jogi megtartási szabályzatok nem engedélyezhetik `allowProtectedAppendWrites` az "allowProtectedAppendWrites" tulajdonságot. Ha az engedélyezett időalapú adatmegőrzési szabályzat érvényes `allowProtectedAppendWrites` , akkor a *AppendBlock* API-t a rendszer addig nem hajtja végre, amíg meg nem szünteti a jogi megtartást.

## <a name="legal-holds"></a>Jogi célú visszatartások

A jogi tartalékok olyan ideiglenes tárolók, amelyek jogi nyomozási célokra vagy általános védelmi házirendekhez használhatók. Minden jogszabályhoz tartozó szabályzatot egy vagy több címkével kell társítani. A címkék névvel ellátott azonosítóként, például egy eset AZONOSÍTÓJAként vagy eseményként használatosak, hogy kategorizálják és leírják a tartás célját.

Egy tároló egyszerre rendelkezhet jogi és időalapú adatmegőrzési házirenddel is. Az adott tárolóban található összes blob mindaddig nem módosítható állapotban marad, amíg az összes jogi célú visszatartást fel nem oldják, még akkor is, ha azok tényleges adatmegőrzési időtartama lejár. A blobok továbbá mindaddig nem módosítható állapotban maradnak, amíg a tényleges megőrzési időtartamuk le nem jár, még a jogi célú visszatartások feloldását követően is.

A következő korlátozások érvényesek a jogi részesedésre:

- A Storage-fiók esetében a jogszabályi megtartási beállítással rendelkező tárolók maximális száma 10 000.
- A tárolók esetében a jogi megtartó címkék maximális száma 10.
- A jogi megtartási címke minimális hossza három alfanumerikus karakter. A maximális hossz 23 alfanumerikus karakter.
- A tárolók esetében a szabályzat időtartama alatt legfeljebb 10 jogszabályi szabályzatot tartalmazó napló marad.

## <a name="scenarios"></a>Forgatókönyvek

A következő táblázat a blob Storage-műveletek azon típusait mutatja be, amelyek a különböző változtathatatlan forgatókönyvek esetében le vannak tiltva. További információkért tekintse meg az [Azure Blob Service REST API](/rest/api/storageservices/blob-service-rest-api) dokumentációját.

| Eset | BLOB állapota | Megtagadott blob-műveletek | Tároló és fiók védelme |
|--|--|--|--|
| A blob tényleges adatmegőrzési időtartama még nem járt le és/vagy jogi célú visszatartás van érvényben | Nem módosítható: törlés- és írásvédett | Helyezze az 1<sup>. blobot, helyezze</sup><sup>az 1.</sup>blokkot, az<sup>1</sup>., a tároló törlése, a blob törlése, a blob metaadatainak beállítása, a Put oldal, a blob tulajdonságainak beállítása, a pillanatkép blob, a növekményes másolási blob, a<sup>2</sup> . | A tároló törlése megtagadva; A Storage-fiók törlése megtagadva |
| Lejárt a tényleges megőrzési időköz a blobon, és nincs beállítva jogi fenntartás | Csak írásvédett (a törlési műveletek engedélyezettek) | Helyezze az 1<sup>. blobot, helyezze</sup>az 1<sup>., az</sup><sup>1</sup>. blokkot, a blob metaadatainak beállítása, a Put oldal, a blob tulajdonságainak beállítása, a pillanatkép-blob, a növekményes másolási blob, a<sup>2</sup> . Hozzáfűzés | A tároló törlése megtagadva, ha legalább 1 blob található a védett tárolóban; A Storage-fiók törlése csak a *zárolt* időalapú házirendek esetében megtagadva |
| Nincs alkalmazva féreg-szabályzat (nincs időalapú megőrzési idő, és nincs jogi megtartási címke) | Változtatható | Nincs | Nincs |

<sup>1</sup> a blob szolgáltatás lehetővé teszi, hogy ezek a műveletek egyszer új blobot hozzanak létre. Egy nem módosítható tárolóban lévő blob elérési útban lévő összes további felülírási művelet nem engedélyezett.

<sup>2</sup> a hozzáfűző blokk csak az engedélyezett tulajdonsággal rendelkező időalapú adatmegőrzési házirendek esetében engedélyezett `allowProtectedAppendWrites` . További információ: a [védett hozzáfűző Blobok írási engedélyezése](#allow-protected-append-blobs-writes) szakasz.

> [!IMPORTANT]
> Bizonyos munkaterhelések, például az [SQL-alapú biztonsági mentés az URL-címre](/sql/relational-databases/backup-restore/sql-server-backup-to-url), hozzon létre egy blobot, majd vegyen fel hozzá. Ha a tárolón aktív időalapú adatmegőrzési szabály vagy jogi szabályozás van érvényben, akkor ez a minta nem fog sikerülni.

## <a name="pricing"></a>Díjszabás

A szolgáltatás használata nem jár további díjszabással. A megváltoztathatatlan adatkezelési díjak ugyanúgy változhatnak, mint a változékony adatszolgáltatások. Az Azure Blob Storage szolgáltatás díjszabását az [Azure Storage díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/storage/blobs/)tekintheti meg.

## <a name="faq"></a>GYIK

**Megadhatja a féreg megfelelőségének dokumentációját?**

Igen. A megfelelőség dokumentálása érdekében a Microsoft megőrizte a nyilvántartások kezelésével és az Cohasset társításával foglalkozó vezető független értékelő vállalatot a nem módosítható blob-tárolók kiértékeléséhez és a pénzügyi szolgáltatások iparágára vonatkozó követelmények teljesítéséhez. A Cohasset ellenőrizte, hogy a nem módosítható blob-tároló, amikor az időalapú Blobok egy féreg állapotában való megőrzésére szolgál, megfelel a CFTC-szabály 1.31 (c)-(d), a FINRA szabály 4511-es és a SEC szabály 17a-4. A Microsoft ezt a szabályt célozza meg, mivel a pénzügyi intézmények számára a rekordok megőrzésére leginkább jellemző útmutatást jelentik. A Cohasset-jelentés a [Microsoft szolgáltatás-Adatvédelmi központban](https://aka.ms/AzureWormStorage)érhető el. Ha a Microsofttól igazolást szeretne kérni a féreg módosíthatatlansági megfelelőségével kapcsolatban, forduljon az Azure ügyfélszolgálatához.

**A szolgáltatás csak a Blobok letiltására és Blobok hozzáfűzésére, vagy a Blobok oldalára vonatkozik?**

A nem módosítható tároló bármely blob-típussal használható, mert a tároló szintjén van beállítva, de javasoljuk, hogy a férget olyan tárolók esetén használja, amelyek főleg a blokk Blobok tárolására és a Blobok hozzáfűzésére szolgálnak. Egy tárolóban lévő meglévő Blobok védelme egy újonnan beállított féreg-szabályzattal történik. Az új blobokat azonban létre kell hozni a WORM tárolón kívül, majd a vágólapra kell másolni. A féreg tárolóba való másolás után az oldal blobjának további módosítása nem engedélyezett. Ha olyan tárolón állít be egy WORM Policy-szabályzatot, amely minden aktív virtuális gép számára tárolja a VHD-ket (blobokat), akkor a rendszer nem veszi igénybe a virtuálisgép-lemez zárolását. Javasoljuk, hogy alaposan tekintse át a dokumentációt, és az időalapú házirendek zárolása előtt tesztelje a forgatókönyveit.

**Létre kell hozni egy új Storage-fiókot a funkció használatához?**

Nem, a már meglévő vagy újonnan létrehozott általános célú v1-, általános célú v2-, BlobStorage-vagy BlockBlobStorage-fiókokkal is használhatja a nem módosítható tárolókat. Az általános célú v1 Storage-fiókok támogatottak, de javasoljuk, hogy frissítsen az általános célú v2-re, hogy kihasználhassa a további funkciókat is. Egy meglévő általános célú v1 Storage-fiók frissítésével kapcsolatos információkért lásd: [Storage-fiók frissítése](../common/storage-account-upgrade.md).

**Alkalmazhatok jogi és időalapú adatmegőrzési szabályzatot is?**

Igen, a tároló egyszerre rendelkezhet jogi és időalapú adatmegőrzési házirenddel is. a "allowProtectedAppendWrites" beállítás azonban addig nem érvényes, amíg a jogi megtartást nem törlik. Az adott tárolóban található összes blob mindaddig nem módosítható állapotban marad, amíg az összes jogi célú visszatartást fel nem oldják, még akkor is, ha azok tényleges adatmegőrzési időtartama lejár. A blobok továbbá mindaddig nem módosítható állapotban maradnak, amíg a tényleges megőrzési időtartamuk le nem jár, még a jogi célú visszatartások feloldását követően is. 

**Csak bírósági eljáráshoz vagy más felhasználási helyzetekben van jogi szabályozás?**

Nem, a jogi fenntartás csak a nem időalapú adatmegőrzési szabályhoz használt általános kifejezés. Nem szükséges kizárólag peres eljárással kapcsolatos eljáráshoz használni. A jogszabályi szabályzatok hasznosak a felülírás és a törlés letiltásához a fontos vállalati féreg-adatok védelméhez, ha a megőrzési időtartam ismeretlen. Vállalati szabályzatként használhatja a kritikus fontosságú féreg számítási feladatait, vagy átmeneti házirendként használhatja azt megelőzően, hogy az egyéni esemény eseményindítójának egy időalapú adatmegőrzési szabályzatot kell használnia. 

**El tudom távolítani a _zárolt_ időalapú adatmegőrzési szabályzatot vagy a jogi megtartást?**

Csak a zárolt időalapú adatmegőrzési szabályzatok távolíthatók el a tárolóból. Az időalapú adatmegőrzési szabályzat zárolása után nem távolítható el; csak a tényleges megőrzési időszakra vonatkozó bővítmények engedélyezettek. A jogi megtartási címkék törölhetők. Ha az összes jogi címkét törli, a rendszer eltávolítja a jogi megtartást.

**Mi történik, ha egy olyan tárolót próbálok törölni, amely időalapú adatmegőrzési szabályzattal vagy jogi feladatokkal rendelkezik?**

A tároló törlése művelet sikertelen lesz, ha legalább egy blob létezik a tárolón a zárolt vagy az időalapú adatmegőrzési szabályzattal, vagy ha a tároló rendelkezik jogi megtartással. A tároló törlése művelet csak akkor lesz sikeres, ha a tárolón belül nem találhatók Blobok, és nincs jogi tartalék. 

**Mi történik, ha olyan tárolóval próbálok törölni egy Storage-fiókot, amely időalapú adatmegőrzési szabályzattal vagy jogi megtartással rendelkezik?**

A Storage-fiók törlése sikertelen lesz, ha van legalább egy olyan tároló, amely rendelkezik egy jogszabályi készlettel vagy egy **zárolt** időalapú házirenddel. A zárolt időalapú házirenddel rendelkező tárolók nem védik a Storage-fiókok törlését. A Storage-fiók törlése előtt el kell távolítania az összes jogi birtokot, és törölnie kell az összes **zárolt** tárolót. További információ a tárolók törléséről: előző kérdés. A Storage-fiókra vonatkozóan további törlési védelmet is alkalmazhat [Azure Resource Manager zárolásokkal](../../azure-resource-manager/management/lock-resources.md).

**Áthelyezhetem az adatok között különböző blob-rétegeket (gyakori, ritka, archív), ha a blob nem módosítható állapotban van?**

Igen, a blob-rétegek beállítása paranccsal áthelyezheti az adategységeket a blob szintjeire, miközben a megfelelő, megváltoztathatatlan állapotban tartja az adategységeket. A nem módosítható tárolók a gyakori, ritka elérésű és archív blob-szinteken támogatottak.

**Mi történik, ha elmulasztok fizetni, és a megőrzési időtartam még nem járt le?**

Nem fizetés esetén a normál adatmegőrzési szabályzatok a Microsofttal kötött szerződés feltételei és kikötései szerint lesznek érvényesek. Általános információk: az [adatkezelés a Microsoftnál](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**Létezik a funkció kipróbálására lehetőséget biztosító ingyenes próba- vagy türelmi időszak?**

Igen. Időalapú adatmegőrzési szabály létrehozásakor a rendszer *zárolt* állapotba kerül. Ebben az állapotban bármilyen módosítást elvégezhet a megőrzési időtartamon, például növelheti vagy csökkentheti a szabályzatot, és törölheti is azt. A szabályzat zárolása után a zárolva marad, amíg a megőrzési időtartam lejár. Ez a zárolt házirend megakadályozza a törlést és a módosítást a megőrzési időtartamra. Azt javasoljuk, hogy a *zárolt* állapotot kizárólag próbaverziós célokra használja, és a szabályzatot 24 órás időtartamon belül zárolja. Ezek a gyakorlatok segítenek a SEC 17a-4 (f) és más rendeletek betartásában.

**Használhatom a Soft delete szolgáltatást a nem módosítható blob-házirendek mellett?**

Igen, ha a megfelelőségi követelmények lehetővé teszik a Soft delete engedélyezését. Az [Azure Blob Storage](./soft-delete-blob-overview.md) -hoz készült Soft delete egy Storage-fiókban lévő összes tárolóra érvényes, függetlenül a jogi vagy időalapú adatmegőrzési szabályoktól. Javasoljuk, hogy a nem módosítható féreg-házirendek alkalmazása és megerősítése előtt a további védelem érdekében engedélyezze a Soft delete használatát.

**A HNS használatára képes fiók esetében Átnevezhetem vagy áthelyezhetek egy blobot, ha a blob nem módosítható állapotban van?**
Nem, a név és a címtár szerkezete is fontos tároló szintű adatnak minősül, amely nem módosítható, ha a megváltoztathatatlan házirend érvényben van. Az átnevezés és áthelyezés csak a HNS használatára képes fiókok esetében érhető el.

## <a name="next-steps"></a>Következő lépések

- [BLOB Storage-módosíthatatlansági szabályzatok beállítása és kezelése](storage-blob-immutability-policies-manage.md)
- [Szabályok beállítása a blob-adatkészletek automatikus előállításához és törléséhez az életciklus-kezeléssel](storage-lifecycle-management-concepts.md)
- [Az Azure Storage-blobok helyreállítható törlése](./soft-delete-blob-overview.md)
- [Az előfizetések, erőforráscsoportok és erőforrások Azure Resource Manager zárolásokkal való](../../azure-resource-manager/management/lock-resources.md)ellátása.
