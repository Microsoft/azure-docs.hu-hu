---
title: A Microsoft-ügyfélszerződés Azure-beli számláinak ismertetése
description: Ismerje meg, hogyan olvashatók le és értelmezhetők a Microsoft-ügyfélszerződésre vonatkozó Azure-beli számlák.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: banders
ms.openlocfilehash: ff53131f3078b33b7e7d853c1fca891b0b86d792
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484598"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>A Microsoft-ügyfélszerződés számláin szereplő kifejezések

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó Azure-beli számlázási fiókra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-ügyfélszerződéshez](#check-access-to-a-microsoft-customer-agreement).

A számla tartalmazza a díjak összesítését, valamint a fizetésre vonatkozó utasításokat. A számlát letöltheti Portable Document Format (.pdf) formátumban az [Azure Portalról](https://portal.azure.com/), vagy kérheti, hogy e-mailben legyen elküldve. További információ: [A Microsoft Azure-számla megtekintése és letöltése](download-azure-invoice.md).

Tekintse meg [a Számla Microsoft Ügyfélszerződés videóját,](https://www.youtube.com/watch?v=e2LGZZ7GubA) amelyből megismerheti a számlát, és megtudhatja, hogyan elemezheti a rajta szereplő díjakat.

>[!VIDEO https://www.youtube.com/embed/e2LGZZ7GubA]

## <a name="billing-period"></a>Számlázási időszak

A számlázás havi rendszerességgel történik. A számlázás napját megtalálhatja az [Azure Portalon](https://portal.azure.com/) a számlázási profil tulajdonságai alatt, a *számla dátuma* szakaszban. A számlázási időszak vége és a számla dátuma közötti időben felmerülő díjak a következő havi számlán szerepelnek, mivel azok a következő számlázási időszakra esnek. Az egyes számlák számlázási időszakának kezdési és záró dátumait a számlázási PDF-fájl tartalmazza az **Összesítő elszámolás** felett.

Ha nagyvállalati szerződésről migrál egy Microsoft Ügyfélszerződés, a migrálás dátumáig továbbra is megkapja az EA számláit. Az új számla a Microsoft Ügyfélszerződés az áttelepítést követően a hónap ötödik napján jön létre. Az első számla a migrálás dátumának részleges díját mutatja. A későbbi számlák havonta jönnek létre, és az egyes havi díjakat mutatják.

### <a name="changes-for-pay-as-you-go-subscriptions"></a>A fizetéses előfizetések változásai

Az előfizetések átváltása, átadása vagy lemondása esetén a legutóbb létrehozott számla tartalmazza az előző számlázási ciklus és az új hiányos számlázási ciklus költségeit.

Például:

Tegyük fel, hogy a fizetéses előfizetés számlázási ciklusa minden hónap 8. naptól 7-ig tart. Az előfizetés november 16-án Microsoft Ügyfélszerződés át egy Microsoft Ügyfélszerződés. A legutóbbi, fizetéses számlában a 2020. október 8. és 2020. november 7. között számlázott díjak vannak. Emellett az új részleges számlázási ciklus díjai is Microsoft Ügyfélszerződés 2020. november 8. és 2020. november 16. között. Lássunk egy példát.

:::image type="content" source="./media/mca-understand-your-invoice/last-invoice-billing-cycle.png" alt-text="Példa egy számlára a legutóbbi számlázási ciklussal." lightbox="./media/mca-understand-your-invoice/last-invoice-billing-cycle.png" :::

## <a name="invoice-terms-and-descriptions"></a>A számlában használatos kifejezések és leírásaik

A következő szakaszokban a számlán látható fontos kifejezések és azok leírásai szerepelnek.

### <a name="invoice-summary"></a>Számlaösszesítő

A **Számlaösszesítő** az első oldal tetején található, és a számlázási profiljával és a fizetés módjával kapcsolatos információkat jeleníti meg.

![Számlaösszesítő szakasz](./media/mca-understand-your-invoice/invoicesummary.png)

| Időszak | Leírás |
| --- | --- |
| Sold to (Vevő) |A jogi személy címe, amely a számlázási fiók tulajdonságaiban található|
| Bill to (Számlázási cím) |A számlát fogadó számlázási profil számlázási címe, amely a számlázási profil tulajdonságaiban található|
| Számlázási profil |A számlát fogadó számlázási profil neve |
| Rendelési szám |Egy választható rendelési szám, amelyet Ön rendel hozzá nyomkövetés céljából |
| Számlaszám |Egy egyedi, a Microsoft által létrehozott, nyomkövetési célokra használt számlaszám |
| Invoice Date (Számla dátuma) |A számla kiállításának dátuma, amely általában 5–12 nappal követi a számlázási ciklus végét. A számla dátumát a számlázási profil tulajdonságaiban ellenőrizheti.|
| Fizetési feltételek |A Microsoft-számla fizetési módja. A *30 nap* azt jelenti, hogy a számláját a számla dátumától számított 30 napon belül egyenlíti ki. |

### <a name="billing-summary"></a>Összesítő elszámolás

Az **Összesítő elszámolás** az előző számlázási időszak óta a számlázási profilra vonatkozó díjakat, az alkalmazott krediteket, az adót és az esedékes teljes összeget mutatja.

![Összesítő elszámolás szakasz](./media/mca-understand-your-invoice/billingsummary.png)

| Időszak | Leírás |
| --- | --- |
| Díjak|Az utolsó számlázási időszak óta felmerült Microsoft-díjak száma összesen. |
| Kreditek |Visszatérítésekből kapott kreditek |
| Alkalmazott Azure-kreditek | Az Azure-díjakra az egyes számlázási időszakokban automatikusan alkalmazott Azure-kreditek |
| Részösszeg |Az adók nélküli esedékes összeg |
| Adó |A számlázási profil országától vagy régiójától függően fizetendő adó típusa és összege. Ha nem kell adót fizetnie, nem szerepel a számlán. |
| Becsült összes megtakarítás |Az érvényes kedvezményekből megtakarított becsült teljes összeg. Ha alkalmazhatók, az érvényes kedvezményes díjszabások a számlaszakasz-információk beszerzési sorainak elemei alatt találhatók. |

### <a name="invoice-sections"></a>Számlaszakaszok

A számlázási profilban szereplő minden egyes számlaszakasznál láthatja a díjakat, az alkalmazott Azure-kreditek mennyiségét, az adót és az esedékes teljes összeget.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Számlaszakasz-információk

A részletek az egyes számlaszakaszok költségeit mutatják termékrendelések szerinti lebontásban. Az egyes termékrendeléseken belül a költségek a szolgáltatás típusa szerint vannak lebontva. A termékekre és szolgáltatásokra vonatkozó napidíjakat az Azure Portalon, az Azure-használati adatok és díjak CSV-fájljában találja. További információ: [A Microsoft-ügyfélszerződés számláján szereplő díjak ismertetése](review-customer-agreement-bill.md).

Az egyes szolgáltatáscsaládokhoz tartozó teljes összeg kiszámítása az *Azure-kreditek**kreditekből/díjakból* való levonásával és az *adó* hozzáadásával történik:


![Számlaszakasz-információk](./media/mca-understand-your-invoice/invoicesectiondetails.png)

| Időszak |Leírás |
| --- | --- |
| Egységár | A szolgáltatás (a díjszabási pénznemében számított) tényleges egységára, amely a használati díj megállapítását szolgálja. Ez minden termék, szolgáltatáscsalád, mérőszám és ajánlat esetén egyedi. |
| Mennyiség | A számlázási időszak során megvásárolt vagy felhasznált mennyiség |
| Díjak/kreditek | A díjak nettó összege a kreditek/visszatérítések alkalmazása után |
| Azure-kredit | A díjakra/kreditekre alkalmazott Azure-kreditek mennyisége|
| Adókulcs | Az adott országnak vagy régiónak megfelelő adókulcs(ok) |
| Adó összege | A vásárlásra alkalmazott adó mennyisége az adókulcs alapján |
| Összesen | A vásárlás teljes összege |

### <a name="how-to-pay"></a>Fizetési mód

A számla alján található, a számla kifizetésére szolgáló útmutató. Fizethet csekkel, átutalással vagy az interneten keresztül. Online fizetéskor használhat hitelkártyát vagy Azure-krediteket, ha erre mód nyílik.

### <a name="publisher-information"></a>Kibocsátó adatai

Ha a számlán harmadik féltől származó szolgáltatások szerepelnek, az egyes kibocsátók neve és címe a számla alján jelenik meg.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [A számlázási profil számláján szereplő díjak ismertetése](review-customer-agreement-bill.md)
- [Az Azure számlázási és napi használati adatainak beszerzése](../manage/download-azure-invoice-daily-usage-date.md)
- [A szervezet Azure-díjszabásásnak megtekintése](../manage/ea-pricing.md)
- [A számlázási profil adózási dokumentumainak megtekintése](mca-download-tax-document.md)
