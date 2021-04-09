---
title: Fiók létrehozása a Azure Portalban
description: Megtudhatja, hogyan hozhat létre Azure Batch-fiókot az Azure Portalon nagyméretű párhuzamos számítási feladatok futtatásához a felhőben
ms.topic: how-to
ms.date: 02/23/2021
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36759a0caef41af9307bf621a1b6b634ddf586cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703664"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Batch-fiók létrehozása az Azure Portalon

Ez a témakör bemutatja, hogyan hozhat létre [Azure batch fiókot](accounts.md) a [Azure Portalban](https://portal.azure.com), és válassza ki a számítási forgatókönyvnek megfelelő fiók tulajdonságait. Emellett megtudhatja, hol találhatók a fontos fiók tulajdonságai, például a hozzáférési kulcsok és a fiók URL-címei.

A Batch-fiókokkal és-forgatókönyvekkel kapcsolatos háttérért lásd: [Batch szolgáltatás munkafolyamata és erőforrásai](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Batch-fiók létrehozása

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A Kezdőlap lapon válassza az **erőforrás létrehozása** lehetőséget.

1. A keresőmezőbe írja be a **Batch szolgáltatás** kifejezést. Válassza ki a **Batch szolgáltatást** az eredmények közül, majd válassza a **Létrehozás** lehetőséget.

1. Adja meg a következő adatokat.

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="Képernyőkép az új batch-fiók képernyőjéről.":::

    a. **Előfizetés**: A Batch-fiók létrehozására szolgáló előfizetés. Ha csak egy előfizetéssel rendelkezik, ez alapértelmezés szerint be van jelölve.

    b. **Erőforráscsoport**: Kiválaszthat egy meglévő erőforráscsoportot az új Batch-fiókhoz, vagy újat is létrehozhat.

    c. **Fióknév**: A választott névnek egyedinek kell lennie abban az Azure-régióban, amelyben az új fiókot létrehozza (lásd alább a **Hely** beállítást). A fiók neve csak kisbetűket vagy számokat tartalmazhat, és 3–24 karakter hosszúnak kell lennie.

    d. **Hely**: Az az Azure-régió, amelyben a Batch-fiókot létrehozza. Csak az előfizetése és az erőforráscsoportja által támogatott régiók jelennek meg lehetőségként.

    e. **Storage-fiók**: egy opcionális [Azure Storage-fiók](accounts.md#azure-storage-accounts) , amelyet a Batch-fiókhoz társít. Választhat egy meglévő Storage-fiókot, vagy létrehozhat egy újat. A legjobb teljesítmény érdekében ajánlott az általános célú v2 Storage-fiók használata.

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="Képernyőfelvétel a Storage-fiók létrehozásakor elérhető beállításokról.":::

1. Ha kívánja, válassza a **speciális** lehetőséget az **azonosító típus**, a **nyilvános hálózati hozzáférés** vagy a **készlet kiosztási módjának** megadásához. A legtöbb esetben az alapértelmezett beállítások a legmegfelelőbbek.

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, majd válassza a **Létrehozás** lehetőséget a fiók létrehozásához.

## <a name="view-batch-account-properties"></a>Batch-fiók tulajdonságainak megtekintése

A fiók létrehozása után jelölje ki azt a beállításai és tulajdonságai eléréséhez. Az összes fiókbeállítást és tulajdonságot elérheti a bal oldali menüből.

> [!NOTE]
> A Batch-fiók neve az azonosító, ezért nem módosítható. Ha módosítania kell egy batch-fiók nevét, törölnie kell a fiókot, és létre kell hoznia egy újat a kívánt névvel.

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Képernyőkép a Azure Portal batch-fiók oldaláról.":::

Ha a [Batch API](batch-apis-tools.md#azure-accounts-for-batch-development)-kkal fejleszt egy alkalmazást, szüksége lesz egy fiók URL-címére és kulcsra a Batch-erőforrások eléréséhez. (A Batch támogatja Azure Active Directory hitelesítés használatát is.) A Batch-fiók hozzáférési adatainak megtekintéséhez válassza a **kulcsok** lehetőséget.

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Képernyőkép a Batch-fiókok kulcsairól a Azure Portal.":::

A Batch-fiókkal társított tárfiók nevének és kulcsainak megtekintéséhez kattintson a **Storage-fiók** elemre.

A Batch-fiókra vonatkozó [erőforrás-kvóták](batch-quota-limit.md) megtekintéséhez válassza a **kvóták** lehetőséget.

## <a name="additional-configuration-for-user-subscription-mode"></a>További konfiguráció a felhasználói előfizetés módhoz

Ha felhasználói előfizetési módban szeretne létrehozni Batch-fiókot, végezze el az alábbi kiegészítő lépéseket a fiók létrehozása előtt.

> [!IMPORTANT]
> A Batch-fiókot felhasználói előfizetési módban létrehozó felhasználónak közreműködői vagy tulajdonosi szerepkör-hozzárendeléssel kell rendelkeznie ahhoz az előfizetéshez, amelyben a Batch-fiókot létrehozza.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Az előfizetés elérésének engedélyezése az Azure Batch számára (egyszeri művelet)

Amikor először hoz létre Batch-fiókot felhasználói előfizetés módban, regisztrálja az előfizetését a Batch szolgáltatásban. (Ha ezt már elvégezte, ugorjon a következő szakaszra.)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza a **minden szolgáltatás**  >  **előfizetések** lehetőséget, majd válassza ki a Batch-fiókhoz használni kívánt előfizetést.

1. Az **Előfizetés** lapon válassza az **Erőforrás-szolgáltatók** elemet, majd keressen rá a **Microsoft.Batch** kifejezésre. Ellenőrizze, hogy a **Microsoft.Batch** erőforrás-szolgáltató regisztrálva van-e az előfizetésben. Ha nem, válassza a **regisztráció** hivatkozást a képernyő felső részén.

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="A Microsoft.BatCH erőforrás-szolgáltatót ábrázoló képernyőkép.":::

1. Térjen vissza az **előfizetés** lapra, majd válassza a **hozzáférés-vezérlés (iam)**  >  **szerepkör-hozzárendelések**  >  **Hozzáadás**  >  **szerepkör-hozzárendelés** hozzáadása elemet.

    :::image type="content" source="media/batch-account-create-portal/subscription_iam.png" alt-text="Az előfizetéshez tartozó szerepkör-hozzárendelések oldal képernyőképe.":::

1. A **szerepkör-hozzárendelés hozzáadása** lapon válassza ki a **közreműködő** vagy **tulajdonos** szerepkört, majd keressen rá a Batch API kifejezésre. Keresse meg **Microsoft Azure batch** vagy **MicrosoftAzureBatch** az API megtalálásához. (a **ddbf3205-c6bd-46ae-8127-60eb93363864** a Batch API alkalmazás-azonosítója.)

1. Miután megtalálta a Batch API-t, jelölje ki, majd kattintson a **Mentés** gombra.

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Felhasználói előfizetési módban szükség van egy [Azure Key Vaultra](../key-vault/general/overview.md) . A Key Vaultnak ugyanabban az előfizetésben és régióban kell lennie, mint a létrehozandó batch-fióknak.

1. A [Azure Portal](https://portal.azure.com)kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
1. A keresőmezőbe írja be a **Key Vault** kifejezést. Válassza ki **Key Vault** az eredmények közül, majd válassza a **Létrehozás** lehetőséget.
1. A **Key Vault létrehozása** lapon adja meg a Key Vault nevét, és hozzon létre egy új erőforráscsoportot a Batch-fiókhoz használni kívánt régióban. A többi beállításnál hagyja meg az alapértelmezett értékeket, majd válassza a **Létrehozás** elemre.

Ha a Batch-fiókot felhasználói előfizetési módban hozza létre, adja meg a **felhasználói előfizetést** a készlet kiosztási módjaként, jelölje ki a létrehozott Key Vault, és jelölje be a jelölőnégyzetet, és adja meg Azure batch hozzáférést a Key Vaulthoz.

Ha inkább a Key Vault elérését szeretné manuálisan megadni, lépjen a Key Vault **hozzáférési házirendek** szakaszára, és válassza a **hozzáférési házirend hozzáadása** lehetőséget. Válassza ki a **rendszerbiztonsági tag kiválasztása** és a **Microsoft Azure batch** keresése (alkalmazás-azonosító **ddbf3205-c6bd-46ae-8127-60eb93363864**) melletti hivatkozást. Válassza ki a résztvevőt, majd a legördülő menüben konfigurálja a **titkos engedélyeket** . Azure Batch legalább a **Get**, a **List**, a **set** és a **delete** engedélyeket kell megadni. A **helyreállítható** [törlést engedélyező kulcstartók](../key-vault/general/soft-delete-overview.md)esetében Azure Batch a helyreállítási engedélyt is meg kell adni.

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Képernyőkép a Azure Batch Secret engedélyek kiválasztásáról":::

Válassza a **Hozzáadás** lehetőséget, majd győződjön meg arról, hogy az **Azure Virtual Machines a telepítéshez** és a **Azure Resource Manager a sablonok központi telepítéséhez** jelölőnégyzet be van jelölve a csatolt **Key Vault** erőforráshoz. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="Képernyőkép a hozzáférési szabályzat képernyőről.":::

### <a name="configure-subscription-quotas"></a>Előfizetési kvóták konfigurálása

A felhasználói előfizetés batch-fiókjaihoz manuálisan kell beállítani az [alapszintű kvótákat](batch-quota-limit.md) . Standard szintű Batch fő kvóták nem érvényesek a felhasználói előfizetési módban lévő fiókokra, valamint az [előfizetése kvótái](../azure-resource-manager/management/azure-subscription-service-limits.md) a regionális számítási magok, a sorozatos számítási magok, valamint más erőforrások használata és kikényszerítve.

1. A [Azure Portal](https://portal.azure.com)válassza ki a felhasználói előfizetés mód batch-fiókját a beállítások és tulajdonságok megjelenítéséhez.
1. A bal oldali menüben válassza a **kvóták** lehetőséget a Batch-fiókhoz társított alapvető kvóták megtekintéséhez és konfigurálásához.

## <a name="other-batch-account-management-options"></a>Egyéb Batch-fiókkezelési lehetőségek

Az Azure Portal használata mellett a többek között a következő eszközökkel is létrehozhat és kezelhet Batch-fiókokat:

- [Batch – PowerShell-parancsmagok](batch-powershell-cmdlets-get-started.md)
- [Azure CLI](batch-cli-get-started.md)
- [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- Megismerheti a Batch-kompatibilis alkalmazások [Batch .NET ügyfélkönyvtárral](quick-run-dotnet.md) vagy [Python](quick-run-python.md) segítségével való fejlesztésének alapjait. Ezek a rövid útmutató végigvezeti egy olyan minta alkalmazáson, amely a Batch szolgáltatás használatával több számítási csomóponton hajt végre munkaterhelést, és az Azure Storage-t használja a munkaterhelés-fájlok előkészítéséhez és lekéréséhez.