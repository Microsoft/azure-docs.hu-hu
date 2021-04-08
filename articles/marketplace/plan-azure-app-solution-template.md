---
title: Megoldási sablon megtervezése Azure-alkalmazási ajánlathoz
description: Megtudhatja, mi szükséges ahhoz, hogy létrehozzon egy új Azure-alkalmazásra vonatkozó megoldási sablont a Microsoft partner Center kereskedelmi piactér portáljának használatával.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: aab856b1e1d2d991cc4964d061a990dbedbeddb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98876511"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>Megoldási sablon megtervezése Azure-alkalmazási ajánlathoz

Ez a cikk ismerteti a megoldási sablonok Azure-beli alkalmazásokra vonatkozó tervének közzétételének követelményeit. A megoldási sablon az Azure-alkalmazások által támogatott két típusú csomag egyike. További információ a két csomag típusa közötti különbségről: [csomagok típusai](plan-azure-application-offer.md#plans). Ha még nem tette meg, olvassa el [Az Azure-alkalmazás ajánlatának megtervezése](plan-azure-application-offer.md)című cikkét.

A megoldási sablon típusa [Azure Resource Manager sablont (ARM-sablont)](../azure-resource-manager/templates/overview.md) igényel a megoldás-infrastruktúra automatikus telepítéséhez.

## <a name="solution-template-requirements"></a>A megoldás sablonjának követelményei

| Követelmények | Részletek |
| ------------ | ------------- |
| Számlázás és mérés | A megoldási sablonok csomagjai nem vonhatók le, de használhatók a Microsoft kereskedelmi Piactéren keresztül számlázott fizetős virtuális gépek üzembe helyezésére. A megoldás ARM-sablonja által üzembe helyezett erőforrások az ügyfél Azure-előfizetésében vannak beállítva. Az utólagos elszámolású virtuális gépeket a Microsoft és az ügyfél Azure-előfizetése alapján számlázzák az ügyfélen keresztül. <br><br> A saját licencek (BYOL) számlázásához, bár a Microsoft az ügyfél-előfizetésben felmerülő infrastrukturális költségeket számláz, a szoftveres licencelési díjakat közvetlenül a vásárlónak kell átirányítani. |
| Azure-kompatibilis virtuális merevlemez (VHD) | A virtuális gépeket Windows vagy Linux rendszerre kell építeni. További információkért lásd:<ul><li>[Azure-beli virtuális gép technikai eszközének létrehozása](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (Windows rendszerű virtuális merevlemezekhez)</li><li>Az Azure-ban [támogatott Linux-disztribúciók](../virtual-machines/linux/endorsed-distros.md) (linuxos virtuális merevlemezek esetén).</li></ul> |
| Ügyfelek általi használat nyomon követése | Az Azure Marketplace-en közzétett összes megoldási sablonban engedélyezni kell az ügyfél-használati jóváírást. További információ az ügyfél-használati feladatokról és annak engedélyezéséről: az [Azure-partneri ügyfél használati](azure-partner-customer-usage-attribution.md)feladatának megállapítása. |
| Felügyelt lemezek használata | A [Managed Disks](../virtual-machines/managed-disks-overview.md) az alapértelmezett beállítás az Azure-beli infrastruktúra-(IaaS-) virtuális gépek megőrzött lemezei számára. A megoldási sablonokban felügyelt lemezeket kell használnia.<ul><li>A megoldási sablonok frissítéséhez kövesse a [felügyelt lemezek használata Azure Resource Manager-sablonokban](../virtual-machines/using-managed-disks-template-deployments.md)című témakör útmutatását, és használja a megadott [mintákat](https://github.com/Azure/azure-quickstart-templates).</li><li>Ha a virtuális merevlemezt képként szeretné közzétenni az Azure Marketplace-en, a felügyelt lemezek mögöttes VHD-jét a [Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) vagy az [Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) használatával importálhatja egy Storage-fiókba.</ul> |
| Központi telepítési csomag | Szüksége lesz egy központi telepítési csomagra, amely lehetővé teszi az ügyfeleknek a terv üzembe helyezését. Ha több olyan csomagot hoz létre, amelyek ugyanazt a technikai konfigurációt igénylik, akkor ugyanazt a csomagot használhatja. Részletekért tekintse meg a következő szakaszt: üzembehelyezési csomag. |
|||

## <a name="deployment-package"></a>Központi telepítési csomag

A központi telepítési csomag tartalmazza az ehhez a csomaghoz szükséges összes sablont, valamint a további erőforrásokat. zip-fájlként csomagolva.

Az összes Azure-alkalmazásnak tartalmaznia kell a következő két fájlt a. zip archívum gyökérkönyvtárában:

- Egy [mainTemplate.js](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template)nevű Resource Manager-sablonfájl. Ez a sablon határozza meg az ügyfél Azure-előfizetésében telepítendő erőforrásokat. A Resource Manager-sablonokra vonatkozó példákért tekintse meg az [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/) katalógusát vagy a megfelelő [githubot: Azure Resource Manager Gyorsindítás sablonok](https://github.com/azure/azure-quickstart-templates) tárháza.
- Felhasználói felület definíciója az Azure-alkalmazás létrehozási élményéhez [createUiDefinition.js](../azure-resource-manager/managed-applications/create-uidefinition-overview.md). A felhasználói felületen elemeket ad meg, amelyek lehetővé teszik a felhasználók számára paraméterértékek megadását.

A maximálisan támogatott fájlméretek a következők:

- Akár 1 GB, összesen tömörített. zip-archívum mérete
- Legfeljebb 1 GB a. zip-archívumban található összes egyes tömörítetlen fájlhoz

Az Azure-alkalmazások minden új ajánlatának tartalmaznia kell egy [Azure-partner ügyfél-használati jóváírási](azure-partner-customer-usage-attribution.md) GUID azonosítót is.

## <a name="azure-regions"></a>Azure-régiók

A tervet közzéteheti az Azure nyilvános régiójában, Azure Government régióban vagy mindkettőben. A [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md)való közzététel előtt tesztelje és érvényesítse a tervet a környezetben, mivel egyes végpontok eltérőek lehetnek. A csomag beállításához és teszteléséhez [Microsoft Azure Government próbaverzióból](https://azure.microsoft.com/global-infrastructure/government/request/)kérjen próbaverziós fiókot.

Ön, mint közzétevő, felelős a megfelelőségi ellenőrzésért, a biztonsági intézkedésekért és az ajánlott eljárásokhoz. A Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az Egyesült Államokban található).

A kereskedelmi piactér által támogatott országok és régiók listáját a [földrajzi Elérhetőség és a pénznemek támogatása](marketplace-geo-availability-currencies.md)című témakörben tekintheti meg.

Azure Government a szolgáltatások bizonyos kormányzati szabályozások és követelmények hatálya alá esnek. Például: FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 és CJIS. A programok minősítésének megismeréséhez akár 100 hivatkozást is megadhat, amely leírja azokat. Ezek lehetnek közvetlenül a programra mutató hivatkozások vagy a saját webhelyein való megfelelőség leírására mutató hivatkozások. Ezek a hivatkozások csak Azure Government ügyfelek számára láthatók.

## <a name="choose-who-can-see-your-plan"></a>Válassza ki, hogy ki láthatja a csomagot

Beállíthatja, hogy az egyes tervek mindenki számára (nyilvános) vagy csak egy adott célközönségre (privát) legyenek láthatók. Akár 100-es csomagot is létrehozhat, és legfeljebb 45 lehet privát. Előfordulhat, hogy létre kell hoznia egy privát csomagot, amely különböző díjszabási lehetőségeket vagy technikai konfigurációkat kínál bizonyos ügyfelek számára.

Az Azure-előfizetési azonosítók használatával hozzáférést biztosíthat egy privát csomaghoz, amely tartalmazza az összes hozzárendelt előfizetés-azonosító leírását. A használatával legfeljebb 10 előfizetés-azonosítót adhat meg manuálisan vagy akár 10 000 előfizetés-azonosítóhoz. CSV-fájl. Az Azure-előfizetési azonosítók GUID azonosítóként jelennek meg, és a betűknek kisbetűsnek kell lenniük.

> [!NOTE]
> Ha közzétesz egy privát csomagot, később megtekintheti a nyilvános láthatóságot. Ha azonban egy nyilvános csomagot tesz közzé, a saját láthatósága nem módosítható.

A megoldási sablonokhoz a tervet az Azure Marketplace-ről is elvégezheti. Erre akkor lehet szükség, ha a csomagot csak egy másik megoldási sablonon vagy felügyelt alkalmazáson keresztül telepítik.

> [!NOTE]
> A Private-csomagok nem támogatottak a Cloud Solution Provider program (CSP) viszonteladóján keresztül létesített Azure-előfizetések esetében.

További információ: [privát ajánlatok a Microsoft kereskedelmi piactéren](private-offers.md).

## <a name="next-steps"></a>Következő lépések

- [Azure-alkalmazások ajánlatának létrehozása a kereskedelmi piactéren](create-new-azure-apps-offer.md)