---
title: Azure Automation futtató fiók kezelése
description: Ez a cikk azt ismerteti, hogyan kezelheti a futtató fiókot a PowerShell vagy a Azure Portal használatával.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.openlocfilehash: f9e99318c526bb935d0e035fdbf59874249390da
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050939"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Azure Automation futtató fiók kezelése

A Azure Automation futtató fiókok hitelesítést biztosítanak a Azure Resource Manager vagy a klasszikus Azure üzemi modell erőforrásainak automatizálási runbookok és egyéb Automation-funkciók használatával történő kezeléséhez. Ez a cikk útmutatást nyújt egy futtató vagy klasszikus futtató fiók kezeléséhez.

Ha többet szeretne megtudni Azure Automation fiók hitelesítéséről és a folyamat-automatizálási forgatókönyvekkel kapcsolatos útmutatásról, tekintse meg az [Automation-fiók hitelesítésének áttekintése](automation-security-overview.md)című cikket.

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Önaláírt tanúsítvány megújítása

A futtató fiókhoz létrehozott önaláírt tanúsítvány a létrehozás dátumától számítva egy évig lejár. A futtató fiók lejárata előtt egy bizonyos ponton meg kell újítania a tanúsítványt. Bármikor megújíthatja, mielőtt lejár.

Az önaláírt tanúsítvány megújításakor a rendszer megőrzi a jelenlegi érvényes tanúsítványt, hogy biztosítsa, hogy a várólistára helyezett vagy aktívan futó runbookok és a futtató fiókkal végzett hitelesítés ne legyen negatív hatással. A tanúsítvány a lejárati dátumáig érvényes marad.

>[!NOTE]
>Ha úgy véli, hogy a futtató fiók biztonsága sérül, törölheti és újból létrehozhatja az önaláírt tanúsítványt.

>[!NOTE]
>Ha úgy állította be a futtató fiókot, hogy a vállalati hitelesítésszolgáltató által kiadott tanúsítványt használja, és az önaláírt tanúsítvány megújítására vonatkozó beállítást használja, a vállalati tanúsítvány egy önaláírt tanúsítvány helyett szerepel.

Az önaláírt tanúsítvány megújításához kövesse az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com).

1. Nyissa meg az Automation-fiókját, és válassza a fiók beállításai szakaszban a **futtató fiókok** elemet.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Az Automation-fiók tulajdonságai panel.":::

1. A **futtató fiókok** tulajdonságai lapon válassza ki a **futtató fiók** vagy a **klasszikus futtató fiók** lehetőséget attól függően, hogy melyik fiókhoz kell megújítani a tanúsítványt.

1. A kiválasztott fiók **Tulajdonságok** lapján válassza a **tanúsítvány megújítása** lehetőséget.

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="A futtató fiók tanúsítványának megújítása.":::

1. A tanúsítvány megújítása során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Futtató fiók engedélyeinek megadása más előfizetésekben

Azure Automation támogatja egyetlen Automation-fiók használatát egyetlen előfizetésből, és a runbookok a különböző előfizetések Azure Resource Manager erőforrásaira vonatkozóan hajtja végre. Ez a konfiguráció nem támogatja a klasszikus Azure-telepítési modellt.

A futtató fiók szolgáltatásnevet a másik előfizetésben lévő [közreműködő](../role-based-access-control/built-in-roles.md#contributor) szerepkörhöz, vagy szigorúbb engedélyekhez rendeli hozzá. További információ: [szerepköralapú hozzáférés-vezérlés](automation-role-based-access-control.md) Azure Automationban. Ha a futtató fiókot a másik előfizetésben lévő szerepkörhöz szeretné rendelni, akkor a feladatot végrehajtó felhasználói fióknak a **tulajdonos** szerepkör tagjának kell lennie az előfizetésben.

> [!NOTE]
> Ez a konfiguráció csak egy szervezet több előfizetését támogatja egy közös Azure AD-bérlő használatával.

A futtató fiók engedélyeinek megadása előtt fel kell jegyeznie a hozzárendelni kívánt szolgáltatásnév megjelenítendő nevét.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az Automation-fiókban válassza a **futtató fiókok** lehetőséget a **Fiókbeállítások** területen.
1. Válassza az Azure-beli **futtató fiók** lehetőséget.
1. Másolja vagy jegyezze fel a **megjelenítendő név** értékét az Azure-beli **futtató fiók** lapon.

A szerepkör-hozzárendelések hozzáadásának részletes lépéseiért tekintse meg az alábbi cikkeket a használni kívánt módszertől függően.

* [Azure szerepkör-hozzárendelés hozzáadása a Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Azure szerepkör-hozzárendelés hozzáadása a Azure PowerShell használatával](../role-based-access-control/role-assignments-powershell.md)
* [Azure szerepkör-hozzárendelés hozzáadása az Azure CLI használatával](../role-based-access-control/role-assignments-cli.md)
* [Azure szerepkör-hozzárendelés hozzáadása a REST API használatával](..//role-based-access-control/role-assignments-rest.md)

Miután hozzárendelte a futtató fiókot a szerepkörhöz, a runbook adja meg az `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` előfizetési környezet használatára vonatkozó beállítását. További információ: [set-AzContext](/powershell/module/az.accounts/set-azcontext).

## <a name="limit-run-as-account-permissions"></a>Futtató fiók engedélyeinek korlátozása

Az Azure-beli erőforrásokhoz való automatizálás célzásának szabályozásához futtathatja a [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) szkriptet. Ez a parancsfájl módosítja a meglévő futtató fiók egyszerű szolgáltatását egy egyéni szerepkör-definíció létrehozásához és használatához. A szerepkör a [Key Vault](../key-vault/index.yml)kivételével minden erőforráshoz rendelkezik engedéllyel.

>[!IMPORTANT]
>A **Update-AutomationRunAsAccountRoleAssignments.ps1** -parancsfájl futtatása után a Runbookok a futtató fiókokkal való Key Vault a továbbiakban nem működik. A szkript futtatása előtt tekintse át a runbookok a fiókjában, hogy meghívja a Azure Key Vault. A Key Vault Azure Automation runbookok való elérésének engedélyezéséhez hozzá kell [adnia a futtató fiókot Key Vault engedélyeihez](#add-permissions-to-key-vault).

Ha továbbra is meg kell határoznia, hogy mit tehet a futtató szolgáltatás, az `NotActions` Egyéni szerepkör-definíció eleméhez más erőforrástípusok is hozzáadhatók. Az alábbi példa korlátozza a hozzáférését a következőhöz: `Microsoft.Compute/*` . Ha ezt az erőforrástípust hozzáadja `NotActions` a szerepkör-definícióhoz, a szerepkör nem fog tudni hozzáférni a számítási erőforrásokhoz. További információ a szerepkör-definíciókkal kapcsolatban: [Az Azure-erőforrások szerepkör-definícióinak megismerése](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Megadhatja, hogy a futtató fiók által használt egyszerű szolgáltatásnév a **közreműködő** szerepkörhöz vagy egy egyénihez van-e rendelve.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az Automation-fiókját, és válassza a fiók beállításai szakaszban a **futtató fiókok** elemet.
1. Válassza az Azure-beli **futtató fiók** lehetőséget.
1. Válassza ki a **szerepkört** a használatban lévő szerepkör-definíció megkereséséhez.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Ellenőrizze a futtató fiók szerepkört." lightbox="media/manage-runas-account/verify-role-expanded.png":::

A futtató fiókok által használt szerepkör-definíciót több előfizetés vagy Automation-fiók esetében is meghatározhatja. Ezt a PowerShell-galéria [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) parancsfájljának használatával teheti meg.

### <a name="add-permissions-to-key-vault"></a>Engedélyek hozzáadása a Key Vaulthoz

Engedélyezheti Azure Automation annak ellenőrzését, hogy a Key Vault és a futtató fiók egyszerű szolgáltatása egyéni szerepkör-definíciót használ-e. A következőket kell tennie:

* Engedélyek megadása Key Vault számára.
* Adja meg a hozzáférési házirendet.

A PowerShell-galéria [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) parancsfájllal engedélyezheti a futtató fiók engedélyeit Key Vault. A Key Vault engedélyeinek beállításával kapcsolatos további részletekért tekintse meg [a Key Vault hozzáférési szabályzatának hozzárendelését](../key-vault/general/assign-access-policy-powershell.md) ismertető témakört.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>A futtató fiókok konfigurációs problémáinak elhárítása

Előfordulhat, hogy a futtató vagy klasszikus futtató fiókhoz szükséges konfigurációs elemeket törölték vagy helytelenül hozták létre a kezdeti beállítás során. A helytelen konfiguráció lehetséges példányai a következők:

* Tanúsítványobjektum
* Kapcsolatobjektum
* A futtató fiók el lett távolítva a közreműködő szerepkörből
* Egyszerű szolgáltatás vagy alkalmazás az Azure AD-ben

Az ilyen hibás konfigurációs példányok esetében az Automation-fiók észleli a módosításokat, és *hiányos* állapotot jelenít meg a fiók futtató fiókok tulajdonságok paneljén.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Hiányos a futtató fiók konfigurációja.":::

Ha kiválasztja a futtató fiókot, a fiók tulajdonságai ablaktábla a következő hibaüzenetet jeleníti meg:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Ezeket a futtató fiókok hibáit gyorsan megoldhatja a futtató fiók [törlésével](delete-run-as-account.md) és [újbóli létrehozásával](create-run-as-account.md) .

## <a name="next-steps"></a>Következő lépések

* [Alkalmazás-objektumok és egyszerű szolgáltatások objektumai](../active-directory/develop/app-objects-and-service-principals.md).
* [Az Azure Cloud Services tanúsítványok áttekintése](../cloud-services/cloud-services-certs-create.md).
* Futtató fiók létrehozásához vagy újbóli létrehozásához tekintse meg [a futtató fiók létrehozása](create-run-as-account.md)című témakört.
* Ha már nincs szüksége futtató fiók használatára, tekintse meg [a futtató fiók törlése](delete-run-as-account.md)című témakört.