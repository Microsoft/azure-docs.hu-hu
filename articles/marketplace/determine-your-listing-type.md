---
title: Bevezetés a termékoldali lehetőségekbe – Microsoft kereskedelmi piactér
description: Ez a cikk a Microsoft AppSource és a Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 01/14/2021
ms.openlocfilehash: 467cc4a62cd044cb4ad264ec8643728e6db0b10c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479814"
---
# <a name="introduction-to-listing-options"></a>Bevezetés a listázási lehetőségekbe

Amikor létrehoz egy ajánlattípust, kiválaszt egy vagy több listázási lehetőséget. Ezek a beállítások határozzák meg, hogy az ügyfelek milyen gombokat látnak az online áruházak ajánlatoldalán. A listázási lehetőségek közé tartozik **az Ingyenes próba,** a **Teszt** meghajtó, **a Kapcsolatfelvétel,** és **a Get It Now**.

Az alábbi táblázat az egyes ajánlattípusokkal elérhető termékoldali lehetőségeket mutatja be:

| Csomag típusa | Ingyenes próba | Tesztverzió | Kapcsolatfelvétel | Szerezze be most `*` |
| ------------ | ------------- | ------------- | ------------- | ------------- |
| Azure-alkalmazás (felügyelt alkalmazás) |   | &#10004; |   | &#10004; |
| Azure-alkalmazás (megoldássablon) |  |  |  | &#10004; |
| Tanácsadási szolgáltatás |  |  | &#10004; |  |
| Azure-tároló |  |  |  | &#10004; |
| Dynamics 365 Business Central | &#10004; | &#10004; | &#10004; | &#10004; |
| Dynamics 365 Customer Engagement & PowerApps | &#10004; | &#10004; | &#10004; | &#10004; |
| Dynamics 365 for Operations | &#10004; | &#10004; | &#10004; | &#10004; |
| IoT Edge modul |  |  |  | &#10004; |
| Felügyelt szolgáltatás |  |  |  | &#10004; |
| Power BI alkalmazás |  |  |  | &#10004; |
| Azure-beli virtuális gép | &#10004; | &#10004; |  | &#10004; |
| Szolgáltatott szoftver | &#10004; | &#10004; | &#10004; | &#10004; |
||||||

&#42; A **Get It Now** termékoldali lehetőség tartalmazza a Get It Now (Ingyenes), a saját licenc használata (BYOL), az Előfizetés és a Használatalapú díjszabás lehetőséget. További információ: [Get It Now](#get-it-now).

## <a name="change-the-offer-type"></a>Az ajánlat típusának módosítása

[!INCLUDE [change-offer-type](./includes/change-offer-type.md)]

## <a name="free-trial"></a>Ingyenes próba

A kereskedelmi piactéren javíthatja a felderíthetőséget, és automatizálhatja a megoldás próbaverziós élményének kiépítését. Ez lehetővé teszi a leendő ügyfelek számára, hogy az ajánlat típusától függően 30 naptól 6 hónapig költség nélkül használják a szolgáltatott szoftvert (SaaS), a szolgáltatott infrastruktúrát (IaaS) vagy a Microsoft alkalmazáson belüli élményét.

Az ügyfelek az **ingyenes próbaverzió** gombot használják az ajánlat termékoldalán az ajánlat kipróbálása érdekében. Ha egy ajánlaton belül több csomaghoz is biztosít ingyenes próbaverziót, az ügyfelek átválthatnak egy másik csomag ingyenes próbaverziójára, de a próbaidőszak nem indul újra.

A virtuálisgép-ajánlatok esetében az azure-beli infrastruktúra költségei az ajánlat próbaidőszakon belül való használata után fizetendők. A próbaidőszak lejárta után az ügyfelek automatikusan díjat számolnak fel az utolsó, standard díjszabás szerint kipróbált csomagért, kivéve, ha a próbaidőszak vége előtt lemondják a próbaidőszakot.

## <a name="test-drive"></a>Tesztverzió

Az ügyfelek az ajánlat **termékoldalán** található Teszt meghajtó gombbal férhetnek hozzá egy előre konfigurált környezethez egy meghatározott számú órán keresztül. További információ a tesztmeghajtókról: [Mi az a tesztmeghajtó?](what-is-test-drive.md).

> [!TIP]
> A Teszt meghajtó lehetőség eltér az ingyenes próbaverziótól. Kínálhat Test Drive-ot, ingyenes próbaverziót vagy mindkettőt. Mindkettő egy meghatározott időtartamra biztosítja az Ön megoldását az ügyfelei számára. A Tesztmeghajtó azonban magában foglalja a termék fő funkcióinak és előnyeinek gyakorlati, önvezető bemutatóját is, amely egy valós megvalósítási forgatókönyvben van bemutatva.

## <a name="contact-me"></a>Kapcsolatfelvétel

Ez a lehetőség az alkalmazás vagy szolgáltatás egyszerű listája. Az ügyfelek az ajánlat termékoldalán található **Kapcsolatfelvétel** gombbal kérhetik, hogy csatlakozzon hozzájuk az ajánlattal kapcsolatban.

## <a name="get-it-now"></a>Szerezze be most

Ez a termékoldali lehetőség tartalmazza a tranzakciós ajánlatokat (előfizetések vagy felhasználóalapú díjszabás), a saját licenc használata (BYOL) és a **Get It Now (Ingyenes) lehetőséget.** A tranzakciós ajánlatokat a kereskedelmi piactéren lehet értékesíteni. A számlázásért és a gyűjteményekért a Microsoft felelős. Az ügyfelek a **Get It Now (Szerezze be most) gombbal** kapják meg az ajánlatot.

Ez a táblázat bemutatja, hogy mely ajánlattípusok támogatják a **Get It Now** termékoldalon elérhető díjszabási lehetőségeket.

| Csomag típusa | Szerezze be most (ingyenes) | BYOL | Előfizetés | Használatalapú díjszabás |
| ------------ | ------------- | ------------- | ------------- | ------------- |
| Azure-alkalmazás (felügyelt alkalmazás) |   |   | &#10004; | &#10004; |
| Azure-alkalmazás (megoldássablon) | &#10004; |   |   |   |
| Tanácsadási szolgáltatás |   |   |   |   |
| Azure Container | &#10004;<sup>1</sup> | &#10004;<sup>1</sup> |   |   |
| Dynamics 365 Business Central | &#10004; |   |   |   |
| Dynamics 365 Customer Engagement & PowerApps | &#10004; |   |   |   |
| Dynamics 365 for Operations | &#10004; |   |   |   |
| IoT Edge modul | &#10004;<sup>1</sup> | &#10004;<sup>1</sup> |   |   |
| Felügyelt szolgáltatás |   | &#10004; |   |   |
| Power BI alkalmazás | &#10004; |   |   |   |
| Azure-beli virtuális gép |   | &#10004; |   | &#10004;<sup>2.</sup> |
| Szolgáltatott szoftver | &#10004; |   | &#10004; | &#10004; |
||||||

<sup>1</sup> A Csomag áttekintése  lap **Díjszabási modell** oszlopában az **Ingyenes** vagy **a BYOL** érték látható, de nem választható ki.

<sup>2</sup> Óránként árazva és havonta számlázva.

### <a name="get-it-now-free"></a>Szerezze be most (ingyenes)

Ezzel a termékoldali lehetőséggel ingyenesen kínálhat alkalmazást. Az ingyenes **ajánlathoz az** Ügyfelek a Get It Now (Lekért most) gombbal kapják meg az ingyenes ajánlatot.

> [!NOTE]
> A Get It Now (Ingyenes) ajánlatok nem jogosultak a Marketplace Rewards ajánlatok előnyeire. Mivel az áruházban nincs tranzakció, ezek próbaverzióként vannak **kategorizálva.** Lásd: [Marketplace Rewards.](#marketplace-rewards)

### <a name="bring-your-own-license-byol"></a>Saját licenc használata (BYOL)

Ezzel a termékoldali lehetőséggel az ügyfelek a kereskedelmi piactéren kívül vásárolt licenccel helyezhetik üzembe az ajánlatot. Ez a lehetőség a helyszínről a felhőbe irányuló migráláshoz ideális. Az ügyfelek a **Get It Now (Beszerzés most) gombbal** vásárolhatják meg az ajánlatot az Öntől előre vásárolt licenccel.

> [!NOTE]
> A BYOL-ajánlatok nem jogosultak a Marketplace Rewards ajánlatokra vonatkozó kedvezményekre. Mivel ezekhez az ügyfélnek meg kell szereznie a licencet a partnertől, és nincs tranzakció a kereskedelmi piactéren, ezek listának vannak **kategorizálva.** Lásd: [Marketplace Rewards.](#marketplace-rewards)

### <a name="subscription"></a>Előfizetés

Ezekért az ajánlattípusokért fizethet díjat:

- Az Azure-alkalmazás (felügyelt alkalmazás) havi előfizetéseket támogat.
- Az SaaS havi és éves előfizetéseket is támogat.

### <a name="usage-based-pricing"></a>Használatalapú díjszabás

A következő ajánlattípusok támogatják a használatalapú díjszabást:

- Az Azure-alkalmazás (felügyelt alkalmazás) támogatást nyújt a forgalmi díjas számlázáshoz. További információ: Felügyelt alkalmazások [forgalmi díjas számlázása.](partner-center-portal/azure-app-metered-billing.md)
- Az SaaS támogatást nyújt a forgalmi díjas számlázáshoz és a felhasználónkénti (helyenkénti) díjszabáshoz. További információ a forgalmi díjas számlázásról: Az SaaS forgalmi [díjas számlázása a kereskedelmi piactér mérési szolgáltatásával.](partner-center-portal/saas-metered-billing.md)
- Az Azure-beli virtuális gépek támogatják a magonkénti, a magonkénti **és** a piaci és magméretenkénti **díjszabást.**  Ezek a lehetőségek óránként vannak kiszámlázva, és havonta vannak számlázva.

A tranzakciós ajánlat létrehozásakor fontos tisztában lennie a díjszabással, a számlázással, a számlázással és a kifizetésekkel kapcsolatos szempontokkal, mielőtt kiválaszt egy ajánlattípust, és létrehoz egy ajánlatot. További információ: [Kereskedelmi piactér – online áruházak.](overview.md#commercial-marketplace-online-stores)

## <a name="sample-offer"></a>Mintaajánlat

Az ajánlat közzététele után a kiválasztott termékoldali beállítások gombként jelennek meg az online áruház termékoldalának bal felső sarkában. Az alábbi képen például egy ajánlatlista oldal látható a Microsoft AppSource online áruházban a **Get It Now** és a Test Drive **gombokkal:**

:::image type="content" source="media/listing-options.png" alt-text="Az ajánlat termékoldalát a Get It Now és a Test Drive (Teszt meghajtó) gombokkal ábrázoló képernyőkép.":::

## <a name="listing-and-pricing-options-by-online-store"></a>Online áruházanként elérhető díjszabási lehetőségek listázása és díjszabása

Különböző feltételek alapján határozzuk meg, hogy az ajánlata szerepel-e a Azure Marketplace, Microsoft AppSource online áruházakban vagy mindkét online áruházban. További információ a két online áruház közötti különbségekről: [Kereskedelmi piactéri online áruházak.](overview.md#commercial-marketplace-online-stores)

Az alábbi táblázat a különböző ajánlattípusokhoz és bővítményekhez elérhető lehetőségeket, valamint az ajánlat online áruházait mutatja be.

| Ajánlattípusok és bővítmények | Kapcsolatfelvétel | Ingyenes próba | Szerezze be most (ingyenes) | BYOL | Get It Now (Transact) |
| ------------ | ------------- | ------------- | ------------- | ------------- | ------------- |
| Azure-beli virtuális gép |   |   |   | Azure Piactér | Azure Piactér |
| Azure-alkalmazás |   |   | Azure Piactér | Azure Piactér | Azure Piactér |
| Azure-tároló  |   |   | Azure Piactér | Azure Piactér |   |
| IoT Edge modul |   |   | Azure Piactér | Azure Piactér |   |
| Felügyelt szolgáltatás |   |   |   | Azure Piactér |   |
| Tanácsadási szolgáltatás | Mindkét online áruház |   |   |   |   |
| SaaS  | Mindkét online áruház | Mindkét online áruház | Mindkét online áruház |   | Mindkét online áruház &#42; |
| Microsoft 365 Alkalmazás | AppSource | AppSource |   |   | AppSource- &#42;&#42; |
| Dynamics 365 Business Central | AppSource | AppSource |   |   |   |
| Dynamics 365 for Customer Engagements & PowerApps | AppSource | AppSource |   |   |   |
| Dynamics 365 Operations | AppSource | AppSource |   |   |   |
| Power BI alkalmazás |   |   | AppSource |   |   |
|||||||

&#42; SaaS tranzakciós ajánlatok az AppSource-ban jelenleg csak hitelkártyákat fogadnak el.

&#42;&#42; Microsoft 365 a bővítmények ingyenesen telepíthetők, és saaS-ajánlattal is bevételt kaphatnak. További információ: [Alkalmazás bevétele a kereskedelmi piactéren keresztül.](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)

## <a name="marketplace-rewards"></a>Marketplace Rewards

A Marketplace Rewards a választott termékoldali lehetőségtől függ. További információ: [A kereskedelmi piactér előnyei.](gtm-your-marketplace-benefits.md)

Ha az ajánlata tranzakciós, a kiszámlázható értékesítések növelésével előnyöket szerezhet.

A nem tranzakciós ajánlatok azon alapulnak, hogy ingyenes próbaverzió van-e csatolva.

## <a name="next-steps"></a>Következő lépések

A létrehozni szükséges ajánlattípust a közzétételi útmutató [ajánlattípus szerint való kiadását lásd:](publisher-guide-by-offer-type.md).
