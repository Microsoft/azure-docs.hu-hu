---
title: Az Azure Storage erőforrás-szolgáltató használata a kezelési erőforrások eléréséhez
description: Az Azure Storage erőforrás-szolgáltató egy olyan szolgáltatás, amely hozzáférést biztosít az Azure Storage felügyeleti erőforrásaihoz. Az Azure Storage erőforrás-szolgáltatója olyan erőforrások létrehozására, frissítésére, kezelésére és törlésére használható, mint a Storage-fiókok, a privát végpontok és a fiók-hozzáférési kulcsok.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcf3e9228c8e651efb8f97067f7ba9eead5959db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92789675"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Az Azure Storage erőforrás-szolgáltató használata a kezelési erőforrások eléréséhez

Az Azure Resource Manager az Azure üzembehelyezési és felügyeleti szolgáltatása. Az Azure Storage erőforrás-szolgáltató a Azure Resource Manageron alapuló szolgáltatás, amely hozzáférést biztosít az Azure Storage felügyeleti erőforrásaihoz. Az Azure Storage erőforrás-szolgáltatója olyan erőforrások létrehozására, frissítésére, kezelésére és törlésére használható, mint a Storage-fiókok, a privát végpontok és a fiók-hozzáférési kulcsok. További információ a Azure Resource Managerről: [Azure Resource Manager áttekintése](../../azure-resource-manager/management/overview.md).

Az Azure Storage erőforrás-szolgáltatóval olyan műveleteket hajthat végre, mint például a Storage-fiók létrehozása vagy törlése, illetve az előfizetéshez tartozó Storage-fiókok listájának beolvasása. Az Azure Storage erőforrás-szolgáltatótól érkező kérések engedélyezéséhez használja a Azure Active Directory (Azure AD) szolgáltatást. Ez a cikk azt ismerteti, hogyan rendelhet hozzá engedélyeket a felügyeleti erőforrásokhoz, és olyan példákat mutat be, amelyek bemutatják, hogyan lehet kérelmeket készíteni az Azure Storage erőforrás-szolgáltatón.

## <a name="management-resources-versus-data-resources"></a>Felügyeleti erőforrások és adatforrások

A Microsoft két REST API-t biztosít az Azure Storage-erőforrások használatához. Ezek az API-k az Azure Storage-ban végrehajtható összes művelet alapját képezik. Az Azure Storage REST API lehetővé teszi a Storage-fiókban tárolt adatkezelést, beleértve a blob-, üzenetsor-, fájl-és táblázat-adatmennyiséget. Az Azure Storage erőforrás-szolgáltató REST API lehetővé teszi a Storage-fiókkal és a kapcsolódó erőforrásokkal való munkát.

A blob-adatok olvasását vagy írását kérő kérelem eltérő engedélyeket igényel, mint a felügyeleti műveletet végrehajtó kérelem. Az Azure RBAC részletesen szabályozhatja az engedélyek mindkét típusú erőforráshoz való hozzáférését. Amikor Azure-szerepkört rendel egy rendszerbiztonsági tag számára, győződjön meg arról, hogy megértette, hogy milyen engedélyekkel fog rendelkezni. Részletes útmutató, amely leírja, hogy mely műveletek vannak társítva az egyes Azure-beli beépített [szerepkörökhöz: az Azure beépített szerepkörei](../../role-based-access-control/built-in-roles.md).

Az Azure Storage támogatja az Azure AD-t a blob-és üzenetsor-tárolással kapcsolatos kérések engedélyezéséhez. További információ a blob-és üzenetsor-műveletek Azure-szerepköreiről: a Blobok [és várólisták hozzáférésének engedélyezése Active Directory használatával](storage-auth-aad.md).

## <a name="assign-management-permissions-with-azure-role-based-access-control-azure-rbac"></a>Felügyeleti engedélyek kiosztása Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)

Minden Azure-előfizetéshez tartozik egy társított Azure Active Directory, amely kezeli a felhasználókat, a csoportokat és az alkalmazásokat. A [Microsoft Identity platform](../../active-directory/develop/index.yml)kontextusában egy felhasználót, csoportot vagy alkalmazást is a rendszerbiztonsági tag néven is ismert. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával megadhat hozzáférést az előfizetéshez tartozó erőforrásokhoz egy olyan rendszerbiztonsági tag számára, amely a Active Directoryban van meghatározva.

Amikor Azure-szerepkört rendel egy rendszerbiztonsági tag számára, azt a hatókört is megadja, amelyen a szerepkör által biztosított engedélyek érvényben vannak. Felügyeleti műveletekhez hozzárendelhet egy szerepkört az előfizetés, az erőforráscsoport vagy a Storage-fiók szintjén. Az Azure-szerepköröket hozzárendelheti egy rendszerbiztonsági tag számára a [Azure Portal](https://portal.azure.com/), az [Azure CLI-eszközök](/cli/azure/install-classic-cli), a [PowerShell](/powershell/azure/)vagy az [Azure Storage erőforrás-szolgáltató REST API](/rest/api/storagerp)használatával.

További információ: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md) és a [klasszikus előfizetés-rendszergazdai szerepköröket, az Azure-szerepköröket és az Azure ad-rendszergazdai szerepköröket](../../role-based-access-control/rbac-and-directory-admin-roles.md).

### <a name="built-in-roles-for-management-operations"></a>Beépített szerepkörök felügyeleti műveletekhez

Az Azure olyan beépített szerepköröket biztosít, amelyek engedélyeket biztosítanak a felügyeleti műveletek meghívásához. Az Azure Storage beépített szerepköröket is biztosít kifejezetten az Azure Storage erőforrás-szolgáltatóval való használatra.

A tárolási felügyeleti műveletek meghívására engedélyt biztosító beépített szerepkörök közé tartoznak a következő táblázatban ismertetett szerepkörök:

|    Azure-szerepkör    |    Description    |    A fiók kulcsaihoz való hozzáférést is tartalmaz?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Tulajdonos** | Képes kezelni az összes tárolási erőforrást és az erőforrásokhoz való hozzáférést.  | Igen, a Storage-fiók kulcsainak megtekintésére és újralétrehozására vonatkozó engedélyeket biztosít. |
| **Közreműködő**  | Kezelheti az összes tárolási erőforrást, de nem kezelheti az erőforrásokhoz való hozzáférést. | Igen, a Storage-fiók kulcsainak megtekintésére és újralétrehozására vonatkozó engedélyeket biztosít. |
| **Olvasó** | Megtekintheti a Storage-fiók adatait, de nem tudja megtekinteni a fiók kulcsait. | Nem. |
| **Tárfiók-közreműködő** | Kezelheti a Storage-fiókot, lekérheti az előfizetéshez tartozó erőforráscsoportok és erőforrások adatait, valamint előfizetési erőforráscsoport-telepítéseket hozhat létre és kezelhet. | Igen, a Storage-fiók kulcsainak megtekintésére és újralétrehozására vonatkozó engedélyeket biztosít. |
| **Felhasználói hozzáférés adminisztrátora** | Kezelheti a Storage-fiókhoz való hozzáférést.   | Igen, lehetővé teszi a rendszerbiztonsági tag számára, hogy engedélyeket rendeljenek magukhoz és másokhoz. |
| **Virtuális gépek közreműködője** | Felügyelheti a virtuális gépeket, de nem azt a Storage-fiókot, amelyhez csatlakoznak.   | Igen, a Storage-fiók kulcsainak megtekintésére és újralétrehozására vonatkozó engedélyeket biztosít. |

A tábla harmadik oszlopa azt jelzi, hogy a beépített szerepkör támogatja-e a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/műveletet**. Ez a művelet engedélyt ad a Storage-fiók kulcsainak olvasására és újbóli előállítására. Az Azure Storage felügyeleti erőforrásainak eléréséhez szükséges engedélyek nem tartalmazzák az adathozzáférésre vonatkozó engedélyeket is. Ha azonban egy felhasználó hozzáfér a fiók kulcsaihoz, akkor a fiókok kulcsaival érheti el az Azure Storage-adatvédelmet a megosztott kulcsos hitelesítésen keresztül.

### <a name="custom-roles-for-management-operations"></a>Egyéni szerepkörök felügyeleti műveletekhez

Az Azure Emellett támogatja az egyéni Azure-szerepkörök definiálását a felügyeleti erőforrásokhoz való hozzáféréshez. További információ az egyéni szerepkörökről: [Egyéni Azure-szerepkörök](../../role-based-access-control/custom-roles.md).

## <a name="code-samples"></a>Kódminták

A következő példákban megtudhatja, hogyan engedélyezheti és hívhatja meg a felügyeleti műveleteket az Azure Storage felügyeleti könyvtáraiban:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager a klasszikus üzemelő példányok ellen

A Resource Manager-alapú és a klasszikus üzemi modell két eltérő módost kínál az Azure-megoldások üzembe helyezésére és felügyeletére. A Microsoft a Azure Resource Manager üzembe helyezési modell használatát javasolja új Storage-fiók létrehozásakor. Ha lehetséges, a Microsoft azt is javasolja, hogy a meglévő klasszikus Storage-fiókokat a Resource Manager-modellel hozza létre újra. Bár a klasszikus üzembe helyezési modellel létrehozhat egy Storage-fiókot, a klasszikus modell kevésbé rugalmas, és végül elavulttá válik.

További információ az Azure-beli üzembe helyezési modellekről: [Resource Manager és klasszikus üzembe helyezés](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Következő lépések

- [Az Azure Resource Manager áttekintése](../../azure-resource-manager/management/overview.md)
- [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md)
- [Az Azure Storage erőforrás-szolgáltató skálázhatósági céljai](scalability-targets-resource-provider.md)