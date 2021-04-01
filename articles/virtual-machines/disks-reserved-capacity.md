---
title: Azure Disk Storage foglalásával kapcsolatos költségek optimalizálása
description: Megtudhatja, hogyan vásárolhat Azure Disk Storage foglalásokat a prémium SSD-vel felügyelt lemezek költségeinek megtakarítása érdekében.
author: roygara
ms.author: rogarana
ms.date: 01/30/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: db6575894904e6ced2d4be48fec5961f5b8b8a54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602626"
---
# <a name="reduce-costs-with-azure-disks-reservation"></a>Költségek csökkentése Azure-beli lemezfoglalással

Mentse a Azure Disk Storage használatot fenntartott kapacitással. Azure Disk Storage a Azure Reserved Virtual Machine Instances összevont foglalások lehetővé teszik a virtuális gép (VM) teljes költségeinek csökkentését. A foglalási kedvezményt a rendszer automatikusan alkalmazza a kijelölt foglalási hatókörben lévő megfelelő lemezekre. Ennek az automatikus alkalmazásnak az alkalmazása miatt nem szükséges foglalást hozzárendelni egy felügyelt lemezhez a kedvezmények beszerzéséhez.

A kedvezményeket óránként, a lemez használattól függően kell alkalmazni. A nem használt fenntartott kapacitás nem veszi át a feladatokat. Azure Disk Storage foglalási kedvezmény nem vonatkozik a nem felügyelt lemezekre, az ultra-lemezekre vagy az oldal blob-felhasználására.

## <a name="determine-your-storage-needs"></a>A tárolási igények meghatározása

A foglalás megvásárlása előtt határozza meg a tárolási igényeket. Jelenleg Azure Disk Storage foglalások csak az Azure Premium SSD SKU-k esetében érhetők el. A prémium szintű SSD SKU a lemez méretének és teljesítményének meghatározása.

A tárolási igények meghatározásakor ne gondoljon a lemezekre a csak kapacitás alapján. Nem rendelkezhet például egy P40-lemez foglalásával, és ezzel két kisebb P30-lemezért kell fizetnie. Foglalás megvásárlásakor csak a lemezek teljes számának megadását kell megvásárolnia SKU-ra.

A lemezes foglalások lemezes SKU-ra épülnek. Ennek eredményeképpen a foglalási felhasználás a megadott méret helyett a lemezes SKU egységén alapul.

Tegyük fel például, hogy fenntart egy P40 lemezt, amely 2 TiB kiépített tárolókapacitással rendelkezik. Azt is feltételezzük, hogy csak két P30-lemezt foglal le. A P40-foglalás ebben az esetben nem veszi figyelembe a P30-felhasználást, és az utólagos elszámolású díjszabást a P30-lemezekre kell fizetnie.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>A vásárláskor megfontolandó szempontok

A lemezek foglalásának megvásárlásakor a következő eljárásokat javasoljuk:

- A használati adatok elemzésével megállapíthatja, hogy mely foglalásokat kell megvásárolnia. A kiépítés vagy a felhasznált lemez kapacitása helyett ügyeljen arra, hogy nyomon kövesse a lemezes SKU használatát.
- Ellenőrizze a lemez foglalását a virtuális gép foglalásával együtt. Javasoljuk, hogy a maximális megtakarítás érdekében foglaljon le a virtuális gépek használatának és a lemezes használatnak a fenntartását. A virtuális gép megfelelő foglalásának meghatározásával és a lemezes foglalás kiértékelésével kezdheti meg. Általánosságban elmondható, hogy minden számítási feladathoz standard konfiguráció tartozik. Előfordulhat például, hogy egy SQL Server kiszolgáló két P40 adatlemezzel és egy P30 operációsrendszer-lemezzel rendelkezik.
  
  Ez a fajta minta segíthet megállapítani a megvásárolt fenntartott összeget. Ezzel a megközelítéssel egyszerűsítheti a kiértékelési folyamatot, és gondoskodhat arról, hogy a virtuális gép és a lemezek esetében is legyen igazított terv. A terv olyan szempontokat tartalmaz, mint például az előfizetések vagy a régiók.

## <a name="purchase-restrictions"></a>Vásárlási korlátozások

A foglalási kedvezmények jelenleg nem érhetők el a következőhöz:

- Nem felügyelt lemezek vagy Blobok.
- Standard SSD-k vagy standard merevlemez-meghajtók (HDD-k).
- Prémium SSD SKU kisebb, mint P30: P1, P2, P3, P4, P6, P10, P15 és P20 SSD SKU.
- Azure Government, Azure Germany vagy Azure China-régiók lemezei.

Ritka körülmények között az Azure korlátozza az új foglalások beszerzését a lemezes SKU részhalmazára, mivel a régió alacsony kapacitása van.

## <a name="buy-a-disk-reservation"></a>Lemezes foglalás vásárlása

Azure Disk Storage foglalásokat a [Azure Portal](https://portal.azure.com/)használatával vásárolhat. A foglalást akár elöl, akár havi fizetéssel is megfizetheti. További információ a havi fizetések megvásárlásáról: [foglalások vásárlása havi fizetéssel](../cost-management-billing/reservations/prepare-buy-reservation.md#buy-reservations-with-monthly-payments).

A fenntartott kapacitás megvásárlásához kövesse az alábbi lépéseket:

1. Lépjen a Azure Portal [vásárlási foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) ablaktáblára.

1. Foglalás megvásárlásához válassza az **Azure Managed Disks** lehetőséget.

    ![A foglalás megvásárlására szolgáló ablaktábla](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Határozza meg a következő táblázatban ismertetett szükséges értékeket:

   |Elem  |Leírás  |
   |---------|---------|
   |**Hatókör**   |  Hány előfizetés használhatja a foglaláshoz kapcsolódó számlázási kedvezményt. Ez az érték határozza meg azt is, hogy a foglalás hogyan legyen alkalmazva az adott előfizetésekre. <br/><br/> Ha a **megosztott** lehetőséget választja, a foglalási kedvezmény az Azure Storage-kapacitásra lesz alkalmazva a számlázási környezetben lévő összes előfizetésben. A számlázási környezet az Azure-ra való feliratkozáson alapul. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. Az utólagos elszámolású ügyfelek esetében a megosztott hatókör magában foglalja az összes olyan előfizetést, amely a fiók rendszergazdája által létrehozott utólagos elszámolású díjszabású.  <br/><br/>  Ha az **egyszeri előfizetést** választja, a foglalási kedvezményt a rendszer a kijelölt előfizetés Azure Storage-kapacitására alkalmazza. <br/><br/> Ha **egyetlen erőforráscsoportot** választ, a foglalási kedvezményt a kiválasztott előfizetésben és az előfizetés kiválasztott erőforráscsoporthoz tartozó Azure Storage-kapacitásra alkalmazza a rendszer. <br/><br/> A foglalási hatókört a foglalás megvásárlása után módosíthatja.  |
   |**Előfizetés**  | Az Azure Storage-foglalás kifizetéséhez használt előfizetés. A kiválasztott előfizetéshez tartozó fizetési mód a költségek kitöltésére szolgál. Az előfizetésnek a következő típusok egyikének kell lennie:<br/><ul><li> Nagyvállalati Szerződés (ajánlati számok: MS-AZR-0017P és MS-AZR-0148P). A nagyvállalati előfizetések esetében a díjakat a beléptetési Azure-előfizetésből (korábban pénzügyi kötelezettségvállalásnak nevezett) egyenlegből és a túlterhelésnek megfelelően kell levonni.</li><br/><li>Egyéni előfizetés utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P és MS-AZR-0023P). Az utólagos elszámolású előfizetések esetében az előfizetés díjait a hitelkártyára vagy a számla fizetési módjára kell fizetni.</li></ul>    |
   | **Lemezek** | A létrehozni kívánt SKU. |
   | **Régió** | Az a régió, ahol a foglalás érvényben van. |
   | **Számlázási gyakoriság** | Milyen gyakran történik a fiók számlázása a foglaláshoz. A lehetőségek közé tartoznak a **havi** és a **kezdeti** beállítások. |

    ![A purchase.png kívánt termék kiválasztására szolgáló ablaktábla](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Miután megadta a foglalás értékeit, a Azure Portal megjeleníti a költségeket. A portálon az utólagos elszámolású számlázásnál is látható az engedmény százaléka. A tovább **gombra kattintva folytassa** a **vásárlási foglalások** ablaktáblát.

1. **A foglalások ablaktáblán** megadhatja a foglalást, és kiválaszthatja a kívánt foglalások teljes mennyiségét. A foglalások száma a lemezek számához. Ha például száz lemezt szeretne lefoglalni, adja meg a **100**-as **mennyiségű** értéket.

1. Tekintse át a foglalás teljes díjait.

    ![A vásárlási foglalások ablaktábla](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

A foglalás megvásárlása után a rendszer automatikusan alkalmazza azokat a meglévő Disk Storage-erőforrásokra, amelyek megfelelnek a foglalás feltételeinek. Ha még nem hozott létre Disk Storage erőforrásokat, akkor a foglalás akkor is érvényes, ha olyan erőforrást hoz létre, amely megfelel a foglalási feltételeknek. A foglalási kifejezés mindkét esetben azonnal megkezdődik sikeres vásárlás után.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokon belül megszakíthatja, cserélheti vagy visszafizetheti a foglalásokat. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="expiration-of-a-reservation"></a>Foglalás lejárata

Ha egy foglalás lejár, az adott foglalás keretében használt Azure Disk Storage kapacitás díját az utólagos elszámolású díjszabás szerint számoljuk el. A foglalások nem újulnak meg automatikusan.

A foglalás lejárta előtt 30 nappal e-mail-értesítést fog kapni, és ismét a lejárati dátummal. A foglalás által biztosított költségmegtakarítások kihasználása érdekében a lejárati dátumnál újabb megújítást kell folytatni.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Mi az az Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Megtudhatja, hogyan alkalmazza a foglalási kedvezményt Azure Disk Storage](../cost-management-billing/reservations/understand-disk-reservations.md)