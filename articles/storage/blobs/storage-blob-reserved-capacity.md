---
title: A fenntartott kapacitású Blob Storage költségeinek optimalizálása
titleSuffix: Azure Storage
description: Ismerje meg az Azure Storage szolgáltatás számára fenntartott kapacitás megvásárlását a blobok és Azure Data Lake Storage Gen2 erőforrások költségeinek megtakarítása érdekében.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6fd791495602846b95e2dd3e99423db5505f5307
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98600921"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>A fenntartott kapacitású Blob Storage költségeinek optimalizálása

Az Azure Storage szolgáltatás számára fenntartott kapacitással pénzt takaríthat meg a blob-adattárolási költségekkel kapcsolatban. Az Azure Storage szolgáltatás számára fenntartott kapacitás kedvezményt biztosít a blokkos blobok és a standard szintű Storage-fiókok Azure Data Lake Storage Gen2 adatai számára, ha egy vagy három évre szóló foglalást végez. A foglalás rögzített tárolási kapacitást biztosít a foglalás feltételeihez.

Az Azure Storage szolgáltatás számára fenntartott kapacitás jelentős mértékben csökkentheti a blokk-blobok és Azure Data Lake Storage Gen2-adatmennyiségek kapacitásának költségeit. Az elért költségmegtakarítás a foglalás időtartamától, a fenntartott teljes kapacitástól, valamint a Storage-fiókhoz választott hozzáférési szinttől és típustól függ. A fenntartott kapacitás számlázási kedvezményt biztosít, és nem befolyásolja az Azure Storage-erőforrások állapotát.

Az Azure Storage-foglalás díjszabásával kapcsolatos további információkért tekintse meg a [blokkblobok díjszabását](https://azure.microsoft.com/pricing/details/storage/blobs/) és [az Azure Data Lake Storage Gen 2 díjszabását](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Az Azure Storage foglalási feltételei

Az alábbi szakaszok az Azure Storage-foglalás feltételeit ismertetik.

### <a name="reservation-capacity"></a>Foglalási kapacitás

Az Azure Storage szolgáltatás számára fenntartott kapacitást 100 TiB-egységben, a havi 1 PiB pedig egy éves vagy hároméves időszakra is megvásárolhatja.

### <a name="reservation-scope"></a>Foglalás hatóköre

Az Azure Storage szolgáltatás számára fenntartott kapacitás egyetlen előfizetéshez vagy több előfizetéshez (közös hatókörű) érhető el. Ha a hatókör egyetlen előfizetésre vonatkozik, a foglalási kedvezmény csak a kiválasztott előfizetésre lesz alkalmazva. Több előfizetésre vonatkozó hatókör esetén a foglalási kedvezmény az ügyfél számlázási környezetében található előfizetések között lesz megosztva.

Az Azure Storage szolgáltatás számára fenntartott kapacitás megvásárlása esetén a foglalást a blobok és a Azure Data Lake Storage Gen2ek számára is használhatja. A rendszer foglalást alkalmaz a megvásárolt hatókörön belüli használatra, és nem korlátozható az előfizetésben lévő adott tárolási fiókra, tárolóra vagy objektumra.

Az Azure Storage-foglalás csak az előfizetésben vagy a megosztott erőforráscsoporthoz tárolt adatmennyiségre vonatkozik. A foglalás nem tartalmazza a korai törlést, a műveletek, a sávszélesség és az adatátviteli díjakat. A foglalás megvásárlása után a foglalási attribútumoknak megfelelő kapacitás díjait az utólagos elszámolású díjszabási díjak helyett a kedvezményes árakon számoljuk el. További információt az Azure-foglalásokról a [Mi a Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)című témakörben talál.

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Támogatott fióktípus, rétegek és redundancia-beállítások

Az Azure Storage szolgáltatás számára fenntartott kapacitás a standard szintű Storage-fiókokban lévő erőforrásokhoz érhető el, beleértve az általános célú v2 (GPv2) és a blob Storage-fiókokat.

Az összes hozzáférési réteg (gyakori, ritka elérésű és archív) a foglalások esetében támogatott. A hozzáférési szintekkel kapcsolatos további információkért lásd [: Azure Blob Storage: gyors, ritka elérésű és archív hozzáférési szintek](storage-blob-storage-tiers.md).

Az összes típusú redundancia a foglalások esetében támogatott. További információ a redundancia lehetőségeiről: [Azure Storage redundancia](../common/storage-redundancy.md).

> [!NOTE]
> Az Azure Storage szolgáltatás számára fenntartott kapacitás nem érhető el a Premium Storage-fiókok, az általános célú v1 (GPv1) Storage-fiókok, a Azure Data Lake Storage Gen1, a Blobok, az Azure üzenetsor-tárolás, az Azure Table Storage vagy a Azure Files számára.  

### <a name="security-requirements-for-purchase"></a>A vásárlás biztonsági követelményei

Fenntartott kapacitás megvásárlása:

- Legalább egy Nagyvállalati vagy egyéni előfizetéshez **tulajdonosi** szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.
- Vállalati előfizetések esetén az EA portálon engedélyezni kell a **fenntartott példányok hozzáadását** . Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie.
- A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatnak Azure Blob Storage fenntartott kapacitást.

## <a name="determine-required-capacity-before-purchase"></a>A szükséges kapacitás meghatározása a vásárlás előtt

Az Azure Storage-foglalás megvásárlásakor ki kell választania a foglaláshoz a régiót, a hozzáférési szintet és a redundancia beállítást. A foglalás csak az adott régióban, a hozzáférési szint és a redundancia szintjén tárolt adatai esetében érvényes. Tegyük fel például, hogy megvásárolja az USA nyugati régiójában tárolt adatok foglalását a forró szinthez a zóna redundáns tárolás (ZRS) használatával. Nem használhatja ugyanazt a foglalást az USA keleti régiójában lévő adatokhoz, az archív rétegben lévő adatokhoz vagy a Geo-redundáns tároláshoz (GRS) tartozó adatokhoz. Azonban további foglalásokat is vásárolhat a további igényekhez.  

A foglalások jelenleg 100 TiB-vagy 1 PiB-blokkhoz érhetők el, és az 1 PiB-blokknál magasabb kedvezményt biztosítunk. Ha foglalást vásárol a Azure Portalban, a Microsoft a korábbi használat alapján javaslatokat tehet az Ön számára, hogy eldöntse, melyik foglalást kell megvásárolnia.

## <a name="purchase-azure-storage-reserved-capacity"></a>Az Azure Storage szolgáltatás számára fenntartott kapacitás vásárlása

Az Azure Storage szolgáltatás számára fenntartott kapacitást az [Azure Portalon](https://portal.azure.com)keresztül vásárolhatja meg. A foglalásért fizethet előre vagy havi részletekben. További információ a havi fizetések megvásárlásáról: [Azure-beli foglalások vásárlása előzetes vagy havi fizetéssel](../../cost-management-billing/reservations/prepare-buy-reservation.md).

A forgatókönyvhöz legmegfelelőbb Foglalási feltételek azonosításával kapcsolatos segítségért lásd: [Az Azure Storage szolgáltatás számára fenntartott kapacitás kedvezményének megismerése](../../cost-management-billing/reservations/understand-storage-charges.md).

A fenntartott kapacitás megvásárlásához kövesse az alábbi lépéseket:

1. Navigáljon a Azure Portal [vásárlási foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) ablaktáblájához.  
1. Új foglalás megvásárlásához válassza az **Azure Blob Storage** elemet.  
1. Töltse ki a kötelező mezőket az alábbi táblázatban leírtak szerint:

    ![A fenntartott kapacitás megvásárlását bemutató képernyőfelvétel](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Mező  |Leírás  |
   |---------|---------|
   |**Hatókör**   |  Azt jelzi, hogy hány előfizetés használhatja a foglaláshoz kapcsolódó számlázási kedvezményt. Azt is meghatározza, hogy a foglalás hogyan legyen alkalmazva az adott előfizetésekre. <br/><br/> Ha a **megosztott** lehetőséget választja, a foglalási kedvezmény az Azure Storage-kapacitásra vonatkozik a számlázási környezetben található bármely előfizetésben. A számlázási környezet az Azure-ra való feliratkozáson alapul. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. Az utólagos elszámolású ügyfelek esetében a megosztott hatókör magában foglalja az összes olyan előfizetést, amely a fiók rendszergazdája által létrehozott utólagos elszámolású díjszabású.  <br/><br/>  Ha az **egyszeri előfizetést** választja, a foglalási kedvezményt a rendszer a kijelölt előfizetés Azure Storage-kapacitására alkalmazza. <br/><br/> Ha **egyetlen erőforráscsoportot** választ, a foglalási kedvezményt a rendszer a kijelölt előfizetésben és a kiválasztott erőforráscsoporthoz alkalmazza az Azure Storage-kapacitásra. <br/><br/> A foglalási hatókört a foglalás megvásárlása után módosíthatja.  |
   |**Előfizetés**  | Az Azure Storage-foglalás kifizetéséhez használt előfizetés. A kiválasztott előfizetéshez tartozó fizetési mód a költségek kitöltésére szolgál. Az előfizetésnek a következő típusok egyikének kell lennie: <br/><br/>  Nagyvállalati Szerződés (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P): nagyvállalati előfizetések esetén a díjakat a regisztrációhoz tartozó Azure-előfizetésből (korábban pénzügyi kötelezettségvállalásnak nevezett) egyenlegből, vagy a túlterhelésnek megfelelően számoljuk el. <br/><br/> Egyéni előfizetés utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P): egyéni előfizetés utólagos elszámolású díjszabással, a díjak számlázása a hitelkártyára vagy a számla fizetési módjára történik az előfizetésben.    |
   | **Régió** | Az a régió, ahol a foglalás érvényben van. |
   | **Hozzáférési szint** | Az a hozzáférési szintet, amelyben a foglalás érvényes. A lehetőségek *közé tartoznak* a *gyakori, a* ritka elérésű és az *archív* beállítások. A hozzáférési szintekkel kapcsolatos további információkért lásd [: Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szintek](storage-blob-storage-tiers.md). |
   | **Redundancia** | A foglalás redundancia lehetősége. A lehetőségek közé tartoznak a következők: *LRS*, *ZRS*, *GRS*, *GZRS*, *ra-GRS* és *ra-GZRS*. További információ a redundancia lehetőségeiről: [Azure Storage redundancia](../common/storage-redundancy.md). |
   | **Számlázási gyakoriság** | Azt jelzi, hogy a fiók milyen gyakran kerül kiszámlázásra a foglaláshoz. A lehetőségek közé tartoznak a *havonta* vagy a *kezdeti* beállítások. |
   | **Méret** | A lefoglalni kívánt kapacitás mennyisége. |
   |**Időszak**  | Egy vagy három év.   |

1. A foglalás paramétereinek kiválasztása után a Azure Portal megjeleníti a költségeket. A portálon az utólagos elszámolású számlázásnál is látható az engedmény százaléka.

1. **A foglalások ablaktáblán** tekintse át a foglalás teljes díját. Megadhatja a foglalás nevét is.

    ![A foglalás megvásárlását bemutató képernyőfelvétel](media/storage-blob-reserved-capacity/purchase-reservations.png)

A foglalás megvásárlása után a rendszer automatikusan alkalmazza azokat a meglévő Azure Storage blokk-blobokra vagy Azure Data Lake Storage Gen2-erőforrásokra, amelyek megfelelnek a foglalás feltételeinek. Ha még nem hozott létre Azure Storage-erőforrásokat, akkor a foglalás akkor érvényes, ha olyan erőforrást hoz létre, amely megfelel a foglalás feltételeinek. A foglalás időtartama mindkét esetben azonnal megkezdődik a sikeres vásárlás után.

## <a name="exchange-or-refund-a-reservation"></a>Foglalás cseréje vagy visszatérítése

A foglalásokat bizonyos korlátozásokkal lehet cserélni vagy visszatéríteni. Ezeket a korlátozásokat a következő szakaszokban ismertetjük.

Egy foglalás cseréjéhez vagy visszafizetéséhez navigáljon a Azure Portal foglalási adataihoz. Válassza az **Exchange** vagy a **visszatérítés** lehetőséget, és kövesse az utasításokat egy támogatási kérelem elküldéséhez. A kérelem feldolgozása után a Microsoft e-mailt küld Önnek a kérelem befejezésének megerősítéséhez.

Azure Reservations szabályzatokkal kapcsolatos további információkért lásd: [önkiszolgáló cserék és visszatérítések Azure Reservationshoz](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Foglalás cseréje

A foglalások cseréje lehetővé teszi, hogy a foglalás fel nem használt része alapján arányosan fizetett visszatérítést kapjon. Ezután a visszatérítést egy új Azure Storage-foglalás beszerzési árára is alkalmazhatja.

Az elvégezhető cserék száma nincs korlátozva. Emellett az Exchange-hez nem tartozik díj. A megvásárolt új foglalásnak egyenlőnek vagy nagyobbnak kell lennie, mint az eredeti foglalásból származó arányos kreditek összege. Egy Azure Storage-foglalás csak egy másik Azure Storage-foglalásra cserélhető le, és nem a többi Azure-szolgáltatás foglalása esetén.

### <a name="refund-a-reservation"></a>Foglalás visszafizetése

Az Azure Storage-foglalások bármikor megvonhatók. Ha megszakítja a szolgáltatást, a rendszer a foglalás hátralévő időtartama alapján, mínusz a 12%-os korai lemondási díjat fogja kapni. A maximális visszatérítés évente $50 000.

A foglalás megszakítása azonnal leállítja a foglalást, és a fennmaradó hónapokat visszaadja a Microsoftnak. A fennmaradó arányos egyenleg, a díj levonása után a rendszer a vásárlás eredeti formáját fogja visszatéríteni.

## <a name="expiration-of-a-reservation"></a>Foglalás lejárata

Ha egy foglalás lejár, az adott foglalás keretében használt Azure Storage-kapacitás díját az utólagos elszámolás díjszabása alapján számoljuk el. A foglalások nem újulnak meg automatikusan.

A foglalás lejárata előtt 30 nappal a lejárati dátummal megjelenő e-mail-értesítést fog kapni. A foglalás által biztosított költségmegtakarítások kihasználása érdekében a lejárati dátumnál újabb megújítást kell folytatni.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Mi az az Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [A foglalási kedvezmény alkalmazása az Azure Storage-ra](../../cost-management-billing/reservations/understand-storage-charges.md)