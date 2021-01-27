---
title: Az Azure-használati API áttekintése
description: Megtudhatja, hogyan biztosítanak az Azure-használati API-k programozott hozzáférést az Azure-erőforrások költség- és használati adataihoz.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: reference
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 4b8b24bacaee87dc9868fab1d5d071201a7215b8
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897730"
---
# <a name="azure-consumption-api-overview"></a>Az Azure-használati API áttekintése

Az Azure használati API-k programozott hozzáférést biztosítanak az Azure-erőforrások költség- és használati adataihoz. Ezek az API-k jelenleg csak a nagyvállalati regisztrációkkal és a Web Direct-előfizetésekkel használhatók (néhány kivétellel). Az API-k folyamatosan frissülnek, hogy más típusú Azure-előfizetéseket is támogassanak.

Az Azure használati API-k a következőkhöz biztosítanak hozzáférést:
- Vállalati és Web Direct-ügyfelek
    - Használati adatok
    - A Marketplace díjai
    - Foglalási javaslatok
    - Foglalási adatok
    - Foglalási összegzések
- Csak vállalati ügyfelek számára
    - Árlista
    - Költségvetések
    - Egyenlegek

## <a name="usage-details-api"></a>Usage Details API

A Usage Details API-val lekérheti az összes saját Azure-erőforrás díj- és használati adatait. Az információk használati adatokra vonatkozó rekordok formájában jelennek meg, amelyek jelenleg naponta erőforrásonként és mérőnként egyszer vannak kibocsátva. Az adatokkal összeadhatja az összes erőforrás költségét, vagy megvizsgálhatja az adott erőforrás(ok) költségeit/használati adatait.

Az API a következőket tartalmazza:

-    **Mérési szint használati adatai** – Itt láthatja az adatokat, beleértve a használati költségeket, a díjat kibocsátó mérőt, valamint azt, hogy milyen Azure-erőforrásra vonatkozik a díj. Az összes használati adatrekord egy napi gyűjtőre van leképezve.
-    **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](../../role-based-access-control/role-assignments-cli.md) vagy [Azure PowerShell parancsmagok](/powershell/azure/) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. A hívóknak standard Azure Active Directory-jogkivonatokat kell használniuk a hitelesítéshez. Adja hozzá a hívót a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörhöz, hogy hozzáférjen az adott Azure-előfizetéshez tartozó használati adatokhoz.
-    **Szűrés** – Az API-eredményhalmazt a használati adatrekordok kisebb halmazára csökkentheti a következő szűrőkkel:
    - Használat vége/használat kezdete
    - Erőforráscsoport
    - Erőforrás neve
-    **Adatösszesítés** – OData használatával kifejezéseket alkalmazhat a használati adatok címkék vagy szűrési tulajdonságok alapján történő összesítéséhez
-    **Használati adatok különböző ajánlattípusokhoz** – A használati adatok információi jelenleg csak nagyvállalati és Web Direct-ügyfelek számára érhetők el.

További információt a [Usage Details API](/rest/api/consumption/usagedetails) műszaki specifikációjában talál.

## <a name="marketplace-charges-api"></a>Marketplace Charges API

A Marketplace Charges API-val lekérheti az összes piactéri erőforrásra (az Azure harmadik féltől származó ajánlataira) vonatkozó díj- és használati adatokat. Ezekkel az adatokkal összeadhatja az összes piactéri erőforrás költségét, vagy megvizsgálhatja az adott erőforrás(ok) költségeit/használati adatait.

Az API a következőket tartalmazza:

-    **Mérési szint használati adatai** – Itt láthatja az adatokat, beleértve a piactéri használati költségeket, a díjat kibocsátó mérőt, valamint azt, hogy milyen erőforrásra vonatkozik a díj. Az összes használati adatrekord egy napi gyűjtőre van leképezve.
-    **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](../../role-based-access-control/role-assignments-cli.md) vagy [Azure PowerShell parancsmagok](/powershell/azure/) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. A hívóknak standard Azure Active Directory-jogkivonatokat kell használniuk a hitelesítéshez. Adja hozzá a hívót a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörhöz, hogy hozzáférjen az adott Azure-előfizetéshez tartozó használati adatokhoz.
-    **Szűrés** – Az API-eredményhalmazt a piactéri rekordok kisebb halmazává csökkentheti a következő szűrőkkel:
    - Használat kezdete/használat vége
    - Erőforráscsoport
    - Erőforrás neve
-    **Használati adatok különböző ajánlattípusokhoz** – A piactéri információk jelenleg csak nagyvállalati és Web Direct-ügyfelek számára érhetők el.

További információt a [Marketplace Charges API](/rest/api/consumption/marketplaces) műszaki specifikációjában talál.

## <a name="balances-api"></a>Balances API

A nagyvállalati ügyfelek a Balances API-val havi összefoglaló információkat kérhetnek le az egyenlegekről, az új vásárlásokról, az Azure Marketplace szolgáltatási díjairól, a kiigazításokról és a többletköltségekről. Ezeket az adatokat az aktuális számlázási időszakra vagy bármely múltbéli időszakra lekérheti. A nagyvállalatok ezeket az adatokat összehasonlíthatják a manuálisan kiszámított összegző díjakkal. Ez az API nem biztosít erőforrás-specifikus adatokat és a költségek összesített nézetét.

Az API a következőket tartalmazza:

-    **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](../../role-based-access-control/role-assignments-cli.md) vagy [Azure PowerShell parancsmagok](/powershell/azure/) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. A hívóknak standard Azure Active Directory-jogkivonatokat kell használniuk a hitelesítéshez. Adja hozzá a hívót a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörhöz, hogy hozzáférjen az adott Azure-előfizetéshez tartozó használati adatokhoz.
-    **Csak nagyvállalati ügyfelek számára** Ez az API csak EA-ügyfelek számára érhető el.
    - Az ügyfeleknek vállalati rendszergazdai engedélyekkel kell rendelkezniük az API meghívásához

További információt a [Balances API](/rest/api/consumption/balances) műszaki specifikációjában talál.

## <a name="budgets-api"></a>Budgets API

A nagyvállalati ügyfelek ezzel az API-val költségalapú vagy használati költségvetéseket hozhatnak létre erőforrások, erőforráscsoportok vagy számlázási mérőszámok részére. Az információk meghatározása után riasztás állítható be, hogy értesítést kapjon a felhasználó által megadható költségvetési küszöbértékek túllépése esetén.

Az API a következőket tartalmazza:

-    **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](../../role-based-access-control/role-assignments-cli.md) vagy [Azure PowerShell parancsmagok](/powershell/azure/) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. A hívóknak standard Azure Active Directory-jogkivonatokat kell használniuk a hitelesítéshez. Adja hozzá a hívót a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörhöz, hogy hozzáférjen az adott Azure-előfizetéshez tartozó használati adatokhoz.
-    **Csak nagyvállalati ügyfelek számára** – Ez az API csak EA-ügyfelek számára érhető el.
-    **Konfigurálható értesítések** – Itt adhatja meg a költségvetés túllépésekor értesítendő felhasználó(ka)t.
-    **Használat- vagy költségalapú költségvetések** – A forgatókönyvnek megfelelően fogyasztás vagy költség alapján hozhatja létre a költségvetést.
-    **Szűrés** – A költségvetést az erőforrások kisebb halmazára szűrheti a következő konfigurálható szűrőkkel
    - Erőforráscsoport
    - Erőforrás neve
    - Mérő
-    **Konfigurálható költségvetési időszakok** – Itt adhatja meg, hogy milyen gyakran kell alaphelyzetbe állítani a költségvetést, és hogy mennyi ideig érvényes a költségvetés.

További információt a [Budgets API](/rest/api/consumption/budgets) műszaki specifikációjában talál.

## <a name="reservation-recommendations-api"></a>Reservation Recommendations API

Ezzel az API-val javaslatokat kaphat a Reserved VM Instances megvásárlásához. A javaslatokkal az ügyfelek könnyebben elemezhetik a várható költségmegtakarításokat és a vásárlási összegeket.

Az API a következőket tartalmazza:

-    **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](../../role-based-access-control/role-assignments-cli.md) vagy [Azure PowerShell parancsmagok](/powershell/azure/) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. A hívóknak standard Azure Active Directory-jogkivonatokat kell használniuk a hitelesítéshez. Adja hozzá a hívót a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörhöz, hogy hozzáférjen az adott Azure-előfizetéshez tartozó használati adatokhoz.
-    **Szűrés** – Az alábbi szűrőkkel szabhatja testre a javaslatok eredményeit:
    - Hatókör
    - Visszatekintési időszak
-    **Foglalások különböző ajánlattípusokhoz** – A foglalások információi jelenleg csak nagyvállalati és Web Direct-ügyfelek számára érhetők el.

További információt a [Reservation Recommendations API](/rest/api/consumption/reservationrecommendations) műszaki specifikációjában talál.

## <a name="reservation-details-api"></a>Reservation Details API

A Reservation Details API-val megtekintheti a korábban megvásárolt virtuális gépek foglalásával kapcsolatos információkat, például a lefoglalt felhasználás mértékét, illetve a ténylegesen felhasznált mennyiséget. Az adatokat virtuális gépenkénti részletességgel tekintheti meg.

Az API a következőket tartalmazza:

-    **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](../../role-based-access-control/role-assignments-cli.md) vagy [Azure PowerShell parancsmagok](/powershell/azure/) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. A hívóknak standard Azure Active Directory-jogkivonatokat kell használniuk a hitelesítéshez. Adja hozzá a hívót a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörhöz, hogy hozzáférjen az adott Azure-előfizetéshez tartozó használati adatokhoz.
-    **Szűrés** – Az API-eredményhalmazt a foglalások kisebb halmazává csökkentheti a következő szűrővel:
    - Dátumtartomány
-    **Foglalások különböző ajánlattípusokhoz** – A foglalások információi jelenleg csak nagyvállalati és Web Direct-ügyfelek számára érhetők el.

További információt a [Reservation Details API](/rest/api/consumption/reservationsdetails) műszaki specifikációjában talál.

## <a name="reservation-summaries-api"></a>Reservation Summaries API

Ezzel az API-val megtekintheti a korábban megvásárolt virtuális gépek foglalásával kapcsolatos összesített információkat, például a lefoglalt felhasználás mértékét, illetve az összesítésben ténylegesen felhasznált mennyiséget.

Az API a következőket tartalmazza:

-    **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](../../role-based-access-control/role-assignments-cli.md) vagy [Azure PowerShell parancsmagok](/powershell/azure/) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. A hívóknak standard Azure Active Directory-jogkivonatokat kell használniuk a hitelesítéshez. Adja hozzá a hívót a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörhöz, hogy hozzáférjen az adott Azure-előfizetéshez tartozó használati adatokhoz.
-    **Szűrés** – Az alábbi szűrővel szabhatja testre az eredményeket napi részletességi szint használata esetén:
    - Usage Date (Használat dátuma)
-    **Foglalások különböző ajánlattípusokhoz** – A foglalások információi jelenleg csak nagyvállalati és Web Direct-ügyfelek számára érhetők el.
-    **Napi vagy havi összesítések** – A hívók meghatározhatják, hogy napi vagy havi részletességi szinten szeretnék-e látni a foglalásuk összegzését.

További információt a [Reservation Summaries API](/rest/api/consumption/reservationssummaries) műszaki specifikációjában talál.

## <a name="price-sheet-api"></a>Price Sheet API
A nagyvállalati ügyfelek ezzel az API-val kérhetik le az összes mérő egyéni díjszabását. A vállalatok ezekkel az adatokkal, valamint a használati adatokkal és a piactér használati adataival végezhetnek költségszámításokat.

Az API a következőket tartalmazza:

-    **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)** – hozzáférési szabályzatok konfigurálása a [Azure Portal](https://portal.azure.com), az [Azure CLI](../../role-based-access-control/role-assignments-cli.md) vagy [Azure PowerShell parancsmagok](/powershell/azure/) segítségével megadhatja, hogy mely felhasználók vagy alkalmazások férhetnek hozzá az előfizetés használati adatokhoz. A hívóknak standard Azure Active Directory-jogkivonatokat kell használniuk a hitelesítéshez. Adja hozzá a hívót a számlázási olvasó, olvasó, tulajdonos vagy közreműködő szerepkörhöz, hogy hozzáférjen az adott Azure-előfizetéshez tartozó használati adatokhoz.
-    **Csak nagyvállalati ügyfelek számára** – Ez az API csak EA-ügyfelek számára érhető el. A Web Direct-ügyfeleknek a RateCard API-t kell használniuk a díjszabás lekéréséhez.

További információt a [Price Sheet API](/rest/api/consumption/pricesheet) műszaki specifikációjában talál.

## <a name="scenarios"></a>Forgatókönyvek

Néhány forgatókönyv, amelyek a használati API-kkal válnak lehetségessé:

-    **Számla egyeztetése** – A Microsoft a megfelelő összeget számította fel?  Mennyi a számlám, és ki tudom számítani magamnak?
-    **Keresztköltségek** – Most, hogy tudom, mennyit számítottak fel, kinek kell kifizetnie a számlát a szervezetben?
-    **Költségoptimalizálás** – Tudom, hogy mennyit számítottak fel… Hogyan használhatom ki jobban az Azure-ra költött összeget?
-    **Költségkövetés** – Szeretném látni, hogy az idő függvényében mennyit költök az Azure-ra, és mennyit használom. Mik a trendek? Hogyan tudnék jobb eredményt elérni?
-    **Az Azure-t a hónap folyamán költi** – Mennyibe kerül az aktuális hónap napja? Változtatnom kell azon, mennyit költök az Azure-ra és/vagy hogyan használom? A hónap során mikor használom a legtöbbet az Azure-t?
-    **Riasztások beállítása** – Szeretnék erőforrás-alapú használatot vagy pénzügyi alapú riasztásokat beállítani a költségvetés alapján.

## <a name="next-steps"></a>További lépések

- További információ a REST API-k díjszabásáról az összes Azure-szolgáltatásra vonatkozóan: az [Azure kiskereskedelmi árainak áttekintése](/rest/api/cost-management/retail-prices/azure-retail-prices).
