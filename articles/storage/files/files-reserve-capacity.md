---
title: A fenntartott kapacitású Azure Files költségeinek optimalizálása
titleSuffix: Azure Files
description: Megtudhatja, hogyan takaríthat meg költségeket az Azure-fájlmegosztás üzemelő példányokon Azure Files fenntartott kapacitás használatával.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 918320cdb24442e551249e4e67d65e4ba85846c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027598"
---
# <a name="optimize-costs-for-azure-files-with-reserved-capacity"></a>A fenntartott kapacitású Azure Files költségeinek optimalizálása
Az Azure-fájlmegosztás kapacitás-foglalásokkal való tárolásának költségei megtakarítást biztosítanak. Azure Files fenntartott kapacitás a tárolási költségekre vonatkozó kapacitást kínál, ha egy vagy három évig egy foglalásra véglegesíti. A foglalás rögzített tárolási kapacitást biztosít a foglalás feltételeihez.

Azure Files fenntartott kapacitás jelentősen csökkentheti az Azure-fájlmegosztás adattárolási kapacitásának költségeit. A megtakarítás időtartama a foglalás időtartamától, a lefoglalt teljes kapacitástól, valamint az Azure-fájlmegosztás számára kiválasztott szinttől és redundancia-beállításoktól függ. A fenntartott kapacitás számlázási kedvezményt biztosít, és nem befolyásolja az Azure-fájlmegosztás állapotát.

A Azure Files foglalási kapacitásával kapcsolatos díjszabásért tekintse meg a [Azure Files díjszabását](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="reservation-terms-for-azure-files"></a>A Azure Files foglalási feltételei
A következő szakaszok a Azure Files kapacitás foglalásának feltételeit ismertetik.

### <a name="reservation-capacity"></a>Foglalási kapacitás
Egy éves vagy hároméves időszakra megvásárolhatja Azure Files fenntartott kapacitást 10 TiB és 100 TiB/hó egységben.

### <a name="reservation-scope"></a>Foglalás hatóköre
Azure Files fenntartott kapacitás egyetlen előfizetéshez vagy több előfizetéshez (közös hatókörű) érhető el. Ha a hatókör egyetlen előfizetésre vonatkozik, a foglalási kedvezmény csak a kiválasztott előfizetésre lesz alkalmazva. Több előfizetésre vonatkozó hatókör esetén a foglalási kedvezmény az ügyfél számlázási környezetében található előfizetések között lesz megosztva. A foglalás a megvásárolt hatókörön belüli használatra vonatkozik, és nem korlátozható az előfizetésen belüli adott tárolási fiókra, tárolóra vagy objektumra.

A Azure Files kapacitásának foglalása csak az előfizetésben vagy a megosztott erőforráscsoporthoz tárolt adatmennyiségre vonatkozik. A tranzakció, a sávszélesség és az adatátviteli díjak nem szerepelnek a foglalásban. A foglalás megvásárlása után a foglalási attribútumoknak megfelelő kapacitás díjait az utólagos elszámolású csomagok díja helyett a kedvezményes árakon számoljuk el. További információt az Azure-foglalásokról a [Mi a Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)című témakörben talál.

### <a name="supported-tiers-and-redundancy-options"></a>Támogatott szintek és redundancia-beállítások
Azure Files fenntartott kapacitás csak az általános célú 2. verziójú Storage-fiókokban (GPv2) üzembe helyezett standard fájlmegosztás esetében érhető el. A fenntartott kapacitás nem érhető el az Azure-fájlmegosztás prémium vagy tranzakciós optimalizált szintjein.

Jelenleg csak az Azure-fájlmegosztás a gyakori és ritka elérésű szinteken támogatja a foglalásokat. Minden tárolási redundancia támogatja a foglalásokat. A redundancia lehetőségeivel kapcsolatos további információkért lásd: [Azure Files redundancia](storage-files-planning.md#redundancy).

### <a name="security-requirements-for-purchase"></a>A vásárlás biztonsági követelményei
Fenntartott kapacitás megvásárlása:

- Legalább egy Nagyvállalati vagy egyéni előfizetéshez **tulajdonosi** szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.
- Vállalati előfizetések esetén az EA portálon engedélyezni kell a **fenntartott példányok hozzáadását** . Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie.
- A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg Azure Files fenntartott kapacitást.

## <a name="determine-required-capacity-before-purchase"></a>A szükséges kapacitás meghatározása a vásárlás előtt
Azure Files foglalás megvásárlásakor a foglaláshoz ki kell választania a régiót, a szintet és a redundancia beállítást. A foglalás csak az adott régióban, a szint és a redundancia szintjén tárolt adatai esetében érvényes. Tegyük fel például, hogy megvásárolja az USA nyugati régiójában tárolt adatok foglalását a forró szinthez a zóna redundáns tárolás (ZRS) használatával. A foglalás nem vonatkozik az USA keleti régiójában tárolt adatokra, a lassú rétegbeli adatokra vagy a Geo-redundáns tárolásba (GRS) vonatkozó adatokra. Azonban további foglalásokat is vásárolhat a további igényekhez.  

A foglalások 10 TiB-vagy 100 TiB-blokkhoz érhetők el, és a 100 TiB-blokkoknál nagyobb kedvezményt biztosítanak. Ha foglalást vásárol a Azure Portalban, a Microsoft a korábbi használat alapján javaslatokat tehet az Ön számára, hogy eldöntse, melyik foglalást kell megvásárolnia.

## <a name="purchase-azure-files-reserved-capacity"></a>Azure Files fenntartott kapacitás megvásárlása
A [Azure Portal](https://portal.azure.com)használatával megvásárolhatja Azure Files fenntartott kapacitást. A foglalásért fizethet előre vagy havi részletekben. További információ a havi fizetések megvásárlásáról: [Azure-beli foglalások vásárlása előzetes vagy havi fizetéssel](../../cost-management-billing/reservations/prepare-buy-reservation.md).

A forgatókönyvhöz legmegfelelőbb Foglalási feltételek azonosításához lásd: [Az Azure Storage szolgáltatás számára fenntartott kapacitás kedvezményének megismerése](../../cost-management-billing/reservations/understand-storage-charges.md).

A fenntartott kapacitás megvásárlásához kövesse az alábbi lépéseket:

1. Navigáljon a Azure Portal [foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) paneljére.  
1. Új foglalás megvásárlásához válassza a **Azure Files** lehetőséget.  
1. Töltse ki a kötelező mezőket az alábbi táblázatban leírtak szerint:

    ![A fenntartott kapacitás megvásárlását bemutató képernyőfelvétel](./media/files-reserve-capacity/select-reserved-capacity.png)

   |Mező  |Leírás  |
   |---------|---------|
   |**Hatókör**   |  Azt jelzi, hogy hány előfizetés használhatja a foglaláshoz kapcsolódó számlázási kedvezményt. Azt is meghatározza, hogy a foglalás hogyan legyen alkalmazva az adott előfizetésekre. <br/><br/> Ha a **megosztott** lehetőséget választja, a foglalási kedvezmény a számlázási környezetben lévő összes előfizetés Azure Files kapacitására vonatkozik. A számlázási környezet az Azure-ra való feliratkozáson alapul. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. Az utólagos elszámolású ügyfelek esetében a megosztott hatókör magában foglalja az összes olyan előfizetést, amely a fiók rendszergazdája által létrehozott utólagos elszámolású díjszabású.  <br/><br/>  Ha az **egyszeri előfizetést** választja, a rendszer a foglalási kedvezményt a kiválasztott előfizetéshez tartozó Azure Files kapacitásra alkalmazza. <br/><br/> Ha **egyetlen erőforráscsoportot** választ, a foglalási kedvezményt a rendszer a kijelölt előfizetésben lévő Azure Files kapacitásra, valamint az adott előfizetéshez tartozó kiválasztott erőforráscsoporthoz alkalmazza. <br/><br/> A foglalási hatókört a foglalás megvásárlása után módosíthatja.  |
   |**Előfizetés**  | Az Azure Files foglalásának kifizetéséhez használt előfizetés. A kiválasztott előfizetéshez tartozó fizetési mód a költségek kitöltésére szolgál. Az előfizetésnek a következő típusok egyikének kell lennie: <br/><br/>  Nagyvállalati Szerződés (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P): nagyvállalati előfizetések esetén a díjakat a regisztrációhoz tartozó Azure-előfizetésből (korábban pénzügyi kötelezettségvállalásnak nevezett) egyenlegből, vagy a túlterhelésnek megfelelően számoljuk el. <br/><br/> Egyéni előfizetés utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P): egyéni előfizetés utólagos elszámolású díjszabással, a díjak számlázása a hitelkártyára vagy a számla fizetési módjára történik az előfizetésben.    |
   | **Régió** | Az a régió, ahol a foglalás érvényben van. |
   | **Szint** | Az a szintű, amelyben a foglalás érvényes. *A lehetőségek* közé tartozik *a gyakori* és a ritka elérés. |
   | **Redundancia** | A foglalás redundancia lehetősége. A lehetőségek közé tartozik a *LRS*, a *ZRS*, a *GRS* és a *GZRS*. A redundancia lehetőségeivel kapcsolatos további információkért lásd: [Azure Files redundancia](storage-files-planning.md#redundancy). |
   | **Számlázási gyakoriság** | Azt jelzi, hogy a fiók milyen gyakran kerül kiszámlázásra a foglaláshoz. A lehetőségek közé tartoznak a *havonta* vagy a *kezdeti* beállítások. |
   | **Méret** | A lefoglalni kívánt kapacitás mennyisége. |
   |**Időszak**  | Egy vagy három év.   |

1. A foglalás paramétereinek kiválasztása után a Azure Portal megjeleníti a költségeket. A portálon az utólagos elszámolású számlázásnál is látható az engedmény százaléka.

1. A foglalás **foglalása** panelen tekintse át a foglalás teljes díjait. Megadhatja a foglalás nevét is.

A foglalás megvásárlása után a rendszer automatikusan alkalmazza a foglalás feltételeinek megfelelő összes meglévő Azure-fájlmegosztást. Ha még nem hozott létre Azure-fájlmegosztást, akkor a foglalás akkor érvényes, ha olyan erőforrást hoz létre, amely megfelel a foglalás feltételeinek. A foglalás időtartama mindkét esetben azonnal megkezdődik a sikeres vásárlás után.

## <a name="exchange-or-refund-a-reservation"></a>Foglalás cseréje vagy visszatérítése
A foglalásokat bizonyos korlátozásokkal lehet cserélni vagy visszatéríteni. Ezeket a korlátozásokat a következő szakaszokban ismertetjük.

Egy foglalás cseréjéhez vagy visszafizetéséhez navigáljon a Azure Portal foglalási adataihoz. Válassza az **Exchange** vagy a **visszatérítés** lehetőséget, és kövesse az utasításokat egy támogatási kérelem elküldéséhez. A kérelem feldolgozása után a Microsoft e-mailt küld Önnek a kérelem befejezésének megerősítéséhez.

Azure Reservations szabályzatokkal kapcsolatos további információkért lásd: [önkiszolgáló cserék és visszatérítések Azure Reservationshoz](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Foglalás cseréje
A foglalások cseréje lehetővé teszi, hogy a foglalás fel nem használt része alapján arányosan fizetett visszatérítést kapjon. Ezután a visszatérítést új Azure Files foglalás beszerzési árára is alkalmazhatja.

Az elvégezhető cserék száma nincs korlátozva. Emellett az Exchange-hez nem tartozik díj. A megvásárolt új foglalásnak egyenlőnek vagy nagyobbnak kell lennie, mint az eredeti foglalásból származó arányos kreditek összege. Azure Files foglalást csak egy másik Azure Files foglalásra lehet cserélni, és nem a többi Azure-szolgáltatás foglalásához.

### <a name="refund-a-reservation"></a>Foglalás visszafizetése
Bármikor megszakíthat egy Azure Files foglalást. Ha megszakítja a szolgáltatást, a rendszer a foglalás hátralévő időtartama alapján, mínusz a 12%-os korai lemondási díjat fogja kapni. A maximális visszatérítés évente $50 000.

A foglalás megszakítása azonnal leállítja a foglalást, és a fennmaradó hónapokat visszaadja a Microsoftnak. A fennmaradó arányos egyenleg, a díj levonása után a rendszer a vásárlás eredeti formáját fogja visszatéríteni.

## <a name="expiration-of-a-reservation"></a>Foglalás lejárata
Ha egy foglalás lejár, az adott foglalás keretében használt Azure Files kapacitás díját az utólagos elszámolás díjszabása alapján számoljuk el. A foglalások nem újulnak meg automatikusan.

A foglalás lejárata előtt 30 nappal a lejárati dátummal megjelenő e-mail-értesítést fog kapni. A foglalás által biztosított költségmegtakarítások kihasználása érdekében a lejárati dátumnál újabb megújítást kell folytatni.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat
Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- [Mi az az Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [A foglalási kedvezmények Azure Storage-szolgáltatásokra való alkalmazásának ismertetése](../../cost-management-billing/reservations/understand-storage-charges.md)