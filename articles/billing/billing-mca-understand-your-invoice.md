---
title: A Microsoft-ügyfélszerződés Azure-beli számláinak ismertetése
description: Ismerje meg, hogyan olvashatók le és értelmezhetők a Microsoft-ügyfélszerződésre vonatkozó Azure-beli számlák.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: a93a108a3820f712d6618646d8c11588d2b567b4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223732"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>A Microsoft-ügyfélszerződés számláin szereplő kifejezések

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó Azure-beli számlázási fiókra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-ügyfélszerződéshez](#check-access-to-a-microsoft-customer-agreement).

A számla tartalmazza a díjak összesítését, valamint a fizetésre vonatkozó utasításokat. A számlát letöltheti Portable Document Format (.pdf) formátumban az [Azure Portalról](https://portal.azure.com/), vagy kérheti, hogy e-mailben legyen elküldve. További információ: [A Microsoft Azure-számla megtekintése és letöltése](billing-download-azure-invoice.md).

## <a name="billing-period"></a>Számlázási időszak

A számlázás havi rendszerességgel történik. A számlázás napját megtalálhatja az [Azure Portalon](https://portal.azure.com/) a számlázási profil tulajdonságai alatt, a *számla dátuma* szakaszban. A számlázási időszak vége és a számla dátuma közötti időben felmerülő díjak a következő havi számlán szerepelnek, mivel azok a következő számlázási időszakra esnek. Az egyes számlák számlázási időszakának kezdési és záró dátumait a számlázási PDF-fájl tartalmazza az **Összesítő elszámolás** felett.

## <a name="invoice-terms-and-descriptions"></a>A számlában használatos kifejezések és leírásaik

A következő szakaszokban a számlán látható fontos kifejezések és azok leírásai szerepelnek.

### <a name="invoice-summary"></a>Számlaösszesítő

A **Számlaösszesítő** az első oldal tetején található, és a számlázási profiljával és a fizetés módjával kapcsolatos információkat jeleníti meg.

![Számlaösszesítő szakasz](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Időtartam | Leírás |
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

![Összesítő elszámolás szakasz](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Időtartam | Leírás |
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

A részletek az egyes számlaszakaszok költségeit mutatják termékrendelések szerinti lebontásban. Az egyes termékrendeléseken belül a költségek a szolgáltatás típusa szerint vannak lebontva. A termékekre és szolgáltatásokra vonatkozó napidíjakat az Azure Portalon, az Azure-használati adatok és díjak CSV-fájljában találja. További információ: [A Microsoft-ügyfélszerződés számláján szereplő díjak ismertetése](billing-mca-understand-your-bill.md).

Az egyes szolgáltatáscsaládokhoz tartozó teljes összeg kiszámítása az *Azure-kreditek* *kreditekből/díjakból* való levonásával és az *adó* hozzáadásával történik:


![Számlaszakasz-információk](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Időtartam |Leírás |
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
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [A számlázási profil számláján szereplő díjak ismertetése](billing-mca-understand-your-bill.md)
- [Az Azure számlázási és napi használati adatainak beszerzése](billing-download-azure-invoice-daily-usage-date.md)
- [A szervezet Azure-díjszabásásnak megtekintése](billing-ea-pricing.md)
- [A számlázási profil adózási dokumentumainak megtekintése](billing-mca-download-tax-document.md)
