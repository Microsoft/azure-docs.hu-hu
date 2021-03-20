---
title: Azure-beli felügyelt alkalmazás megtervezése Azure-alkalmazási ajánlathoz
description: Ismerje meg, hogy mi szükséges a felügyelt alkalmazási terv létrehozásához egy új Azure-alkalmazáshoz a Microsoft partner Center kereskedelmi piactér portáljának használatával.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 694f501efc565ed498c1c8d8e2e38326277e8605
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96621416"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>Azure-beli felügyelt alkalmazás megtervezése Azure-alkalmazási ajánlathoz

Az Azure-beli _felügyelt alkalmazáscsomag_ egyik módja egy Azure-alkalmazás ajánlatának közzététele az Azure Marketplace-en. Ha még nem tette meg, olvassa el [a kereskedelmi piactérre vonatkozó Azure-alkalmazás ajánlatának megtervezése](plan-azure-application-offer.md)című cikkét.

A felügyelt alkalmazások olyan Transact-ajánlatok, amelyek üzembe helyezése és számlázása az Azure Marketplace-en keresztül történik. A felhasználó által megjelenő listázási lehetőség a letöltés.

Azure-alkalmazás használata: felügyelt alkalmazási csomag, ha a következő feltételek szükségesek:

- A virtuális gépet (VM) vagy egy teljes infrastruktúra-szolgáltatást (IaaS) használó megoldásként üzembe kell helyeznie egy előfizetésen alapuló megoldást.
- Ön vagy az ügyfele megköveteli, hogy a megoldást egy partner felügyelje. Egy partner lehet például rendszerintegrátor vagy felügyelt szolgáltató (MSP).

## <a name="managed-application-offer-requirements"></a>A felügyelt alkalmazások ajánlatára vonatkozó követelmények

| Követelmények | Részletek |
| ------------ | ------------- |
| Azure-előfizetés | A felügyelt alkalmazásokat az ügyfél előfizetéséhez kell telepíteni, de egy harmadik fél is felügyelheti. |
| Számlázás és mérés | Az erőforrások az ügyfél Azure-előfizetésében érhetők el. Az utólagos elszámolású fizetési modellt használó virtuális gépeket a Microsofton keresztül, az ügyfél Azure-előfizetésén keresztül számlázzák. <br><br> A saját licencű virtuális gépekhez a Microsoft az ügyfél-előfizetésben felmerülő összes infrastrukturális költséget számláz, de a szoftveres licencelési díjakat közvetlenül az ügyféllel együtt kell lebonyolítani. |
| Azure-kompatibilis virtuális merevlemez (VHD) | A virtuális gépeket Windows vagy Linux rendszerre kell építeni. További információkért lásd:<br> • [Hozzon létre egy Azure-beli virtuális gép technikai eszközét](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (Windows rendszerű virtuális merevlemezekhez).<br> • Az Azure-ban  [támogatott Linux-disztribúciók](../virtual-machines/linux/endorsed-distros.md) (linuxos virtuális merevlemezek esetén). |
| Ügyfelek általi használat nyomon követése | Az Azure-alkalmazások minden új ajánlatának tartalmaznia kell egy [Azure-partner ügyfél-használati jóváírási](azure-partner-customer-usage-attribution.md) GUID azonosítót is. További információ az ügyfél-használati feladatokról és annak engedélyezéséről: az [Azure-partneri ügyfél használati](azure-partner-customer-usage-attribution.md)feladatának megállapítása. |
| Központi telepítési csomag | Szüksége lesz egy központi telepítési csomagra, amely lehetővé teszi az ügyfeleknek a terv üzembe helyezését. Ha több olyan csomagot hoz létre, amelyek ugyanazt a technikai konfigurációt igénylik, ugyanazt a csomagot használhatja. Részletekért tekintse meg a következő szakaszt: üzembehelyezési csomag. |
|||

> [!NOTE]
> A felügyelt alkalmazásokat az Azure Marketplace-en keresztül kell üzembe helyezni. Ha az ügyfél kommunikációja aggodalomra ad okot, az érdeklődők megosztásának engedélyezése után érdemes megszólítani az érdeklődőket.

## <a name="deployment-package"></a>Központi telepítési csomag

A központi telepítési csomag tartalmazza az ehhez a csomaghoz szükséges sablonfájlokat, valamint a további,. zip-fájlként csomagolt erőforrásokat.

Az összes Azure-alkalmazásnak tartalmaznia kell a következő két fájlt a. zip archívum gyökérkönyvtárában:

- Egy [mainTemplate.js](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template)nevű Resource Manager-sablonfájl. Ez a sablon határozza meg az ügyfél Azure-előfizetésében telepítendő erőforrásokat. A Resource Manager-sablonokra példaként tekintse meg az [Azure Gyorsindítás sablonok](https://azure.microsoft.com/documentation/templates/) katalógusát vagy a megfelelő [GitHub: Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) rövid útmutató sablonok tárházát.
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

A Private-csomagok nem támogatottak a Cloud Solution Provider program (CSP) viszonteladóján keresztül létesített Azure-előfizetések esetében. További információ: [privát ajánlatok a Microsoft kereskedelmi piactéren](private-offers.md).

> [!NOTE]
> Ha közzétesz egy privát csomagot, később megtekintheti a nyilvános láthatóságot. Ha azonban egy nyilvános csomagot tesz közzé, a saját láthatósága nem módosítható.

## <a name="define-pricing"></a>Díjszabás meghatározása

Minden csomag esetében meg kell adnia a havonta érvényes díjat. Ez az ár a megoldás által központilag üzembe helyezett erőforrások esetében felmerülő bármely Azure-infrastruktúra vagy utólagos elszámolású szoftver költségein felül van.

A havi díj mellett a nem standard egységek fogyasztásának díjszabását is megadhatja [mért számlázás](partner-center-portal/azure-app-metered-billing.md)használatával. Ha kívánja, a havonta megadott árat nulla értékre állíthatja, és kizárólag a mért számlázást kell fizetnie.

Az árak USD-ben (USD = Egyesült Államok dollár) lesznek átalakítva az összes kiválasztott piac helyi pénznemére az aktuális árfolyamok alapján, a mentéskor. Dönthet azonban úgy is, hogy az egyes piacokra vonatkozóan beállítja az ügyfelek árát.

## <a name="just-in-time-jit-access"></a>Igény szerinti (JIT) hozzáférés

A JIT-hozzáférés lehetővé teszi, hogy a hibaelhárítás és a karbantartás érdekében emelt szintű hozzáférést kérjen egy felügyelt alkalmazás erőforrásaihoz. Mindig csak olvasási hozzáférése van az erőforrásokhoz, de egy adott időszakra vonatkozóan nagyobb hozzáférés érhető el. További információ: a [Azure Managed Applications igény szerinti hozzáférésének engedélyezése és kérése](../azure-resource-manager/managed-applications/request-just-in-time-access.md).

> [!NOTE]
> Ügyeljen rá, hogy a `createUiDefinition.json` szolgáltatás támogatásához frissítse a fájlt.

## <a name="deployment-mode"></a>Üzembehelyezési mód

A felügyelt alkalmazások **teljes** vagy **növekményes** telepítési mód használatára is konfigurálható. Teljes módban az ügyfél által az alkalmazás újratelepítése a felügyelt erőforráscsoport erőforrásainak eltávolítását eredményezi, ha az erőforrások nincsenek meghatározva a [mainTemplate.js](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). A növekményes módban az alkalmazás újratelepítése változatlanul hagyja a meglévő erőforrásokat. További információ: [Azure Resource Manager telepítési módok](../azure-resource-manager/templates/deployment-modes.md).

## <a name="notification-endpoint-url"></a>Értesítési végpont URL-címe

Opcionálisan megadhat egy HTTPS-webhook-végpontot, amely értesítést kap az összes szifilisz-műveletről a csomag felügyelt alkalmazási példányain.

## <a name="customize-allowed-customer-actions-optional"></a>Engedélyezett felhasználói műveletek testreszabása (nem kötelező)

Opcionálisan megadhatja, hogy az ügyfelek milyen műveleteket hajthatnak végre a felügyelt erőforrásokon az `*/read` alapértelmezés szerint elérhető műveletek mellett.

Ha ezt a lehetőséget választja, meg kell adnia a vezérlési műveleteket vagy az engedélyezett adatműveleteket, vagy mindkettőt. További információ: [Az Azure-erőforrások megtagadási hozzárendeléseinek ismertetése](../role-based-access-control/deny-assignments.md). Az elérhető műveletekért lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md). Ha például engedélyezni szeretné a felhasználóknak a virtuális gépek újraindítását, adja hozzá `Microsoft.Compute/virtualMachines/restart/action` az engedélyezett műveleteket.

## <a name="choose-who-can-manage-the-application"></a>Válassza ki, hogy kik kezelhetik az alkalmazást

Meg kell adnia, hogy kik kezelhetnek felügyelt alkalmazásokat a kiválasztott felhőkben: _nyilvános Azure_ és _Azure Government felhő_. Gyűjtse össze a következő információkat:

- **Azure Active Directory bérlő azonosítója** – az Azure ad-bérlő azonosítója (más néven CÍMTÁR-azonosító), amely tartalmazza azon felhasználók, csoportok vagy alkalmazások identitását, amelyekhez engedélyeket kíván adni. Az Azure AD-bérlő AZONOSÍTÓját a [Azure Active Directory tulajdonságainál](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)találja a Azure Portal.
- **Engedélyek** – adja hozzá a felügyelt erőforráscsoport számára engedélyt adni kívánt felhasználók, csoportok vagy alkalmazások Azure Active Directory objektumazonosítóát. Azonosítsa a felhasználót a résztvevő azonosítója alapján, amely a [Azure Portal Azure Active Directory felhasználók](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)paneljén található.

Minden résztvevő AZONOSÍTÓhoz hozzá kell rendelnie az Azure AD beépített szerepköreit (tulajdonos vagy közreműködő). A kiválasztott szerepkör leírja azokat az engedélyeket, amelyekre a rendszerbiztonsági tag az ügyfél-előfizetésben lévő erőforrásokra vonatkozóan fog rendelkezni. További információ: [Beépített Azure-szerepkörök](../role-based-access-control/built-in-roles.md). A szerepköralapú hozzáférés-vezérléssel (RBAC) kapcsolatos további információkért lásd: Ismerkedés a [RBAC szolgáltatással a Azure Portalban](../role-based-access-control/overview.md).

> [!NOTE]
> Bár az Azure-régiókban akár 100-engedélyt is hozzáadhat, általában könnyebb létrehozni egy Active Directory felhasználói csoportot, és megadnia annak AZONOSÍTÓját a "résztvevő azonosítója" értékkel. Ez lehetővé teszi, hogy a csomag telepítése után további felhasználókat vegyen fel a felügyeleti csoportba, és csökkentse a terv frissítésének szükségességét, hogy további engedélyeket adjon hozzá.

## <a name="policy-settings"></a>Szabályzatbeállítások

Az Azure- [szabályzatokat](../governance/policy/index.yml) alkalmazhatja a felügyelt alkalmazásra, hogy meghatározza a telepített megoldás megfelelőségi követelményeit. A szabályzatdefiníciókról és a paraméterértékek formátumáról tekintse meg a következő dokumentumot: [Azure Policy-minták](../governance/policy/samples/index.md).

Legfeljebb öt házirendet konfigurálhat, és az egyes szabályzatok közül csak egy példányt állíthat be. Egyes házirend-típusokhoz további paraméterek szükségesek.

| Házirendtípus | Szükséges házirend-paraméterek |
| ------------ | ------------- |
| Titkosítás Azure SQL Database | No |
| Azure SQL Server naplózási beállítások | Yes |
| Titkosítás Azure Data Lake Store | No |
| Diagnosztikai beállítások naplózása | Yes |
| Erőforrás-hely megfelelőségének naplózása | No |
|||

Minden hozzáadott házirend-típushoz hozzá kell rendelnie a standard vagy az ingyenes házirend-SKU-t. A naplózási házirendekhez a szabványos SKU szükséges. A szabályzatok nevei legfeljebb 50 karakterből állhatnak.

## <a name="next-steps"></a>Következő lépések

- [Azure-alkalmazások ajánlatának létrehozása a kereskedelmi piactéren](create-new-azure-apps-offer.md)
