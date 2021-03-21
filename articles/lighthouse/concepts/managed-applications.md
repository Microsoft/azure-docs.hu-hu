---
title: Azure Lighthouse és Azure Managed Applications
description: Ismerje meg, hogyan használhatók együtt az Azure Lighthouse és az Azure által felügyelt alkalmazások.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 5c30c3234a57e25ceaa521ad485f58d4d663ebe9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693972"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse és Azure Managed Applications

Az Azure Managed Applications és az Azure Lighthouse is működik azáltal, hogy lehetővé teszi a szolgáltató számára az ügyfél bérlője által használt erőforrások elérését. Hasznos lehet megérteni a különbségeket a működésük módjában, valamint az azokhoz a forgatókönyvekben, amelyek segítenek az engedélyezésben, és hogy hogyan használhatók együtt.

> [!TIP]
> Bár a jelen témakörben a szolgáltatók és az ügyfelekre is hivatkozunk, a [több bérlőt kezelő vállalatok](enterprise.md) ugyanazokat a folyamatokat és eszközöket használhatják.

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>Az Azure Lighthouse és az Azure által felügyelt alkalmazások összehasonlítása

Ez a táblázat olyan magas szintű különbségeket mutat be, amelyek hatással lehetnek az Azure Lighthouse vagy az Azure által felügyelt alkalmazások használatára. Ahogy az alábbi ábrán is látható, megtervezheti azokat a megoldásokat, amelyek együtt használják azokat.

|Megfontolandó  |Azure Lighthouse  |Azure felügyelt alkalmazások  |
|---------|---------|---------|
|Tipikus felhasználó     |Több bérlőt kezelő szolgáltatók vagy vállalatok         |Független szoftvergyártók (ISV-ket)         |
|A bérlők közötti hozzáférés hatóköre     |Előfizetés (ok) vagy erőforráscsoport (ok)         |Erőforráscsoport (egyetlen alkalmazás hatóköre)         |
|Megvásárolható az Azure Marketplace-en     |Nem (az ajánlatokat közzéteheti az Azure Marketplace-en, de az ügyfeleket külön számlázzák)        |Yes         |
|IP-védelem     |Igen (az IP maradhat a szolgáltató bérlője)        |Igen (a tervezés szerint az erőforráscsoport zárolva van az ügyfelek számára)         |
|Megtagadás-hozzárendelések     |Nem         |Igen        |

### <a name="azure-lighthouse"></a>Azure Lighthouse

Az [Azure Lighthouse](../overview.md)használatával a szolgáltatók számos felügyeleti feladatot végezhetnek el közvetlenül az ügyfél előfizetésében (vagy erőforráscsoporthoz). Ez a hozzáférés egy logikai kivetítésen keresztül érhető el, amely lehetővé teszi a szolgáltatók számára, hogy bejelentkezzenek a saját bérlőbe, és hozzáférhessenek az ügyfél bérlőhöz tartozó erőforrásokhoz. Az ügyfél meghatározhatja, hogy mely előfizetések vagy erőforráscsoportok legyenek delegálva a szolgáltatónak, és az ügyfél teljes hozzáférést tart fenn ezekhez az erőforrásokhoz. A szolgáltató hozzáférését bármikor el is távolíthatja.

Az Azure Lighthouse használatához az Azure-beli erőforrás- [kezelést](azure-delegated-resource-management.md) az ARM-sablonok vagy egy [felügyelt szolgáltatási ajánlat Azure Marketplace-](managed-services-offers.md)en való [üzembe helyezésével](../how-to/onboard-customer.md) lehet előkészíteni. A [partnerek azonosítójának összekapcsolásával](../how-to/partner-earned-credit.md)nyomon követheti az ügyfelek bevonásának hatásait.

Az Azure világítótorony általában akkor használatos, ha egy szolgáltató folyamatosan végzi a kezelési feladatokat az ügyfél számára.

### <a name="azure-managed-applications"></a>Azure felügyelt alkalmazások

Az [Azure által felügyelt alkalmazások](../../azure-resource-manager/managed-applications/overview.md) lehetővé teszik, hogy a szolgáltató vagy az ISV olyan felhőalapú megoldásokat nyújtson, amelyek megkönnyítik az ügyfelek számára a saját előfizetésekben való üzembe helyezést és használatát.

A felügyelt alkalmazásokban az alkalmazás által használt erőforrások össze vannak csomagolva és a közzétevő által felügyelt erőforráscsoporthoz vannak telepítve. Ez az erőforráscsoport szerepel az ügyfél előfizetésében, de a közzétevő bérlője rendelkezik hozzáféréssel a tulajdonoshoz. Az ISV továbbra is felügyeli és karbantartja a felügyelt alkalmazást, míg az ügyfél nem rendelkezik közvetlen hozzáféréssel az erőforráscsoporthoz való munkavégzéshez vagy az erőforrásaihoz való hozzáféréshez.

A felügyelt alkalmazások támogatják a [testreszabott Azure Portal élményt](../../azure-resource-manager/managed-applications/concepts-view-definition.md) és [az egyéni szolgáltatókkal való integrációt](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md). Ezekkel a lehetőségekkel testre szabható és integrált felhasználói élményt biztosíthat, így az ügyfelek könnyebben végezhetnek el felügyeleti feladatokat.

A felügyelt alkalmazások [közzétehető az Azure Marketplace](../../marketplace/create-new-azure-apps-offer.md)-en, akár privát ajánlatként, akár egy adott ügyfél általi használatra, akár olyan nyilvános ajánlatokra is, amelyeket több ügyfél is vásárolhat. A szervezeten belüli felhasználók számára is elérhetők, ha [felügyelt alkalmazásokat tesznek közzé a szolgáltatás katalógusában](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md). A Service Catalog és a piactér példányait ARM-sablonok használatával is üzembe helyezheti, amely tartalmazhat egy kereskedelmi Piactéri partner egyedi azonosítóját, amellyel nyomon követheti az [ügyfelek használati](../../marketplace/azure-partner-customer-usage-attribution.md)feladatait.

Az Azure által felügyelt alkalmazások jellemzően egy adott ügyfélhez szükségesek, amely a szolgáltató által teljes körűen felügyelt kulcsrakész megoldáson keresztül érhető el.

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>Az Azure Lighthouse és az Azure által felügyelt alkalmazások együttes használata

Habár az Azure Lighthouse és az Azure által felügyelt alkalmazások különböző hozzáférési mechanizmusokat használnak különböző célok eléréséhez, előfordulhatnak olyan helyzetek, amikor a szolgáltatónak érdemes mindkét ügyfelet ugyanazzal az ügyféllel használni.

Előfordulhat például, hogy egy ügyfél a szolgáltató által az Azure Világítótoronyon keresztül szállított felügyelt szolgáltatásokat kívánja használni, így a partner által feltett műveletekkel és a delegált előfizetésük folyamatos felügyeletével is betekintést nyerhet. Előfordulhat azonban, hogy a szolgáltató nem szeretné, hogy az ügyfél hozzáférjen bizonyos, az ügyfél bérlője által tárolt erőforrásokhoz, vagy engedélyezze az adott erőforrásokra vonatkozó testreszabott műveleteket. Ezeknek a céloknak a teljesítéséhez a szolgáltató felügyelt alkalmazásként tehet közzé privát ajánlatot. A felügyelt alkalmazás tartalmazhat olyan erőforráscsoportot, amelyet az ügyfél bérlője telepít, de az ügyfél nem fér hozzá közvetlenül.

Előfordulhat, hogy az ügyfelek több szolgáltató által felügyelt alkalmazások is érdeklik, függetlenül attól, hogy a felügyelt szolgáltatásokat is használják-e az Azure Lighthouse-n keresztül ezen szolgáltatók bármelyikén. Emellett a Cloud Solution Provider (CSP) program partnerei olyan felügyelt alkalmazásokat is megadhatnak, amelyeket az Azure Lighthouse-n keresztül támogatnak más ISV-ket. Számos lehetőség közül a szolgáltatók kiválaszthatják a megfelelő egyensúlyt az ügyfelek igényeinek kielégítéséhez, miközben szükség esetén korlátozzák az erőforrásokhoz való hozzáférést.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure által felügyelt alkalmazásokról](../../azure-resource-manager/managed-applications/overview.md).
- Ismerje meg, hogyan lehet [előfizetést bevezetni az Azure Lighthouse](../how-to/onboard-customer.md)szolgáltatásba.
- Ismerje meg az [ISV-forgatókönyveket az Azure Lighthouse](isv-scenarios.md)-ben.