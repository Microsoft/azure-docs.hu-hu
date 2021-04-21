---
title: Fiók Azure Automation kezelése
description: Ez a cikk bemutatja, hogyan kezelheti a futó fiókot a PowerShell vagy a Azure Portal.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e440a27c8f7778c800148feb5bec76ca5a48f4f5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833921"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Fiók Azure Automation kezelése

A felhőben futó Azure Automation biztosítják a hitelesítést a Azure Resource Manager vagy a klasszikus Azure-beli üzembe helyezési modell erőforrásainak kezeléséhez Automation-runbookok és egyéb Automation-funkciók használatával. Ez a cikk útmutatást nyújt a futó vagy klasszikus futtatású fiókok kezeléséhez.

A fiókhitelesítéssel és Azure Automation folyamatautomatizálási forgatókönyvekkel kapcsolatos útmutatásért lásd: [Automation-fiókhitelesítés áttekintése.](automation-security-overview.md)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Önaírt tanúsítvány megújítása

A fiókhoz létrehozott önaírt tanúsítvány a létrehozás dátumának egy évét járja le. A futó fiók lejárata előtt meg kell újítania a tanúsítványt. A lejárata előtt bármikor megújíthatja.

Amikor megújítja az önaírt tanúsítványt, a rendszer megőrzi az aktuális érvényes tanúsítványt, hogy a rendszer ne befolyásolja hátrányosan a várólistán lévő vagy aktívan futó és a futtató fiókkal hitelesítést futtató runbookokat. A tanúsítvány a lejárati dátumáig érvényes marad.

>[!NOTE]
>Ha úgy gondolja, hogy a fiók biztonsága sérült, törölheti és újra létrehozhatja az önaírt tanúsítványt.

>[!NOTE]
>Ha úgy konfigurálta a run as fiókot, hogy a vállalati hitelesítésszolgáltató által kiadott tanúsítványt használja, és az önaírt tanúsítvány megújítására használható beállítást használja, a vállalati tanúsítványt egy önaírt tanúsítvány váltja fel.

A következő lépésekkel újítsa meg az önaírt tanúsítványt.

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com).

1. A fiókbeállítások szakaszban válassza a Futtatás mint **fiókok** lehetőséget az Automation-fiókjában.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Automation-fiók tulajdonságok panele.":::

1. A Run **As Accounts** (Futtatott fiókok) tulajdonságlapon válassza a Run **As Account** (Futtatás mint fiók) vagy a Classic Run As Account (Klasszikus run **As Account)** lehetőséget attól függően, hogy melyik fiókhoz kell megújítani a tanúsítványt.

1. A kiválasztott **fiók Tulajdonságok** lapján válassza a Tanúsítvány **megújítása lehetőséget.**

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="A futó fiók tanúsítványának megújítása.":::

1. A tanúsítvány megújítása során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Run As account permissions in other subscriptions (Run As-fiókengedélyek megadása más előfizetésben)

Azure Automation támogatja egyetlen Automation-fiók egyetlen előfizetésből történő futtatását, valamint a runbookok futtatását Azure Resource Manager erőforrásokon több előfizetésben. Ez a konfiguráció nem támogatja a klasszikus Azure-beli üzembe helyezési modellt.

A másik előfizetésben a [](../role-based-access-control/built-in-roles.md#contributor) közreműködői szerepkört vagy szigorúbb engedélyeket kell hozzárendelni a run as account service principal (Futtatás fiók szolgáltatásnév) szerepkörhöz. További információ: [Szerepköralapú hozzáférés-vezérlés a](automation-role-based-access-control.md) Azure Automation. A másik előfizetésben a szerepkörhöz való hozzárendeléshez a feladatot végző felhasználói fióknak  az előfizetés Tulajdonos szerepkörének tagjának kell lennie.

> [!NOTE]
> Ez a konfiguráció csak egy közös Azure AD-bérlőt használó szervezet több előfizetését támogatja.

Mielőtt engedélyeket ad a futó fióknak, először fel kell vennie a hozzárendelendő szolgáltatásnév megjelenítendő nevét.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az Automation-fiókjában válassza a **Fiókbeállítások** alatt a **Fiókfiókok lehetőséget.**
1. Válassza **az Azure-beli fiók lehetőséget.**
1. Másolja vagy jegyezze fel a **Megjelenítendő név értékét** az **Azure-beli futtatás fiókja oldalon.**

A szerepkör-hozzárendelések hozzáadásának részletes lépéseit a használni kívánt módszertől függően az alábbi cikkekben talál.

* [Azure-szerepkörök hozzárendelése a Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Azure-szerepkörök hozzárendelése Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Azure-szerepkörök hozzárendelése az Azure CLI használatával](../role-based-access-control/role-assignments-cli.md)
* [Azure-szerepkörök hozzárendelése a REST API](..//role-based-access-control/role-assignments-rest.md)

Miután hozzárendeli a szerepkörhöz a runbookban a fiókot, adja meg, hogy milyen előfizetési környezetet `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` szeretne használni. További információ: [Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

## <a name="limit-run-as-account-permissions"></a>A futó fiók engedélyeinek korlátozása

Az Automation Azure-beli erőforrásokra való célzásának [](https://aka.ms/AA5hug8) szabályozásához futtassa aUpdate-AutomationRunAsAccountRoleAssignments.ps1szkriptet. Ez a szkript módosítja a meglévő run as-account szolgáltatásnév egyéni szerepkör-definíció létrehozása és használata érdekében. A szerepkör az kivételével minden erőforráshoz [rendelkezik Key Vault.](../key-vault/index.yml)

>[!IMPORTANT]
>AUpdate-AutomationRunAsAccountRoleAssignments.ps1 **futtatása** után azok a runbookok, amelyek a Key Vault keresztül férnek hozzá a fiókokhoz, többé nem működnek. A szkript futtatása előtt tekintse át a fiókjában futó runbookokat, és ellenőrizze, hogy vannak-e Azure Key Vault. A runbookok Key Vault hozzáférésének Azure Automation a runbookok futtatásához, hozzá kell adni a Key Vault-fiók [engedélyét.](#add-permissions-to-key-vault)

Ha tovább kell korlátoznia, hogy mire képes a futó szolgáltatásnév, más erőforrástípusokat is hozzáadhat az egyéni `NotActions` szerepkör-definíció elemhez. Az alábbi példa a hozzáférését `Microsoft.Compute/*` korlátozza. Ha ezt az erőforrástípust hozzáadja a szerepkör-definícióhoz, a szerepkör nem fog tudni hozzáférni egyetlen `NotActions` számítási erőforráshoz sem. További információ a szerepkör-definíciókról: [Az Azure-erőforrások szerepkör-definíciói.](../role-based-access-control/role-definitions.md)

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Meghatározhatja, hogy a fiók által használt szolgáltatásnév a Közreműködő szerepkörhöz vagy egy egyéni szerepkörhöz van-e rendelve. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A fiókbeállítások szakaszban válassza a Futtatás mint **fiókok** lehetőséget az Automation-fiókjában.
1. Válassza **az Azure-beli fiók lehetőséget.**
1. Válassza **a Szerepkör** lehetőséget a használt szerepkör-definíció megtalálásához.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Ellenőrizze a run as account (Futtatás fiók) szerepkört." lightbox="media/manage-runas-account/verify-role-expanded.png":::

Meghatározhatja azt a szerepkör-definíciót is, amelyet a több előfizetéshez vagy Automation-fiókhoz használt run as fiókok használnak. Ehhez használja aCheck-AutomationRunAsAccountRoleAssignments.ps1[ szkriptet ](https://aka.ms/AA5hug5) a PowerShell-galéria.

### <a name="add-permissions-to-key-vault"></a>Engedélyek hozzáadása a Key Vault

Engedélyezheti a Azure Automation, hogy Key Vault és a Key Vault szolgáltatásnév egyéni szerepkör-definíciót használ-e. A következőt kell:

* Adjon engedélyeket a Key Vault.
* Állítsa be a hozzáférési szabályzatot.

Az alkalmazás [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) szkripttel PowerShell-galéria adhat a Key Vault. A [hozzáférés-Key Vault](../key-vault/general/assign-access-policy-powershell.md) beállításával kapcsolatos további részletekért lásd: Key Vault hozzáférési szabályzat Key Vault hozzárendelése.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>A futó fiókok hibás konfigurációval kapcsolatos problémáinak megoldása

Előfordulhat, hogy a kezdeti beállítás során a run as vagy a classic run as fiókhoz szükséges egyes konfigurációs elemek törölve vagy nem megfelelően hoztak létre. A hibás konfiguráció lehetséges példányai a következők:

* Tanúsítványobjektum
* Kapcsolatobjektum
* A közreműködői szerepkörből eltávolított futó fiók
* Egyszerű szolgáltatás vagy alkalmazás az Azure AD-ben

Az ilyen hibás konfigurációt tartalmazó példányok esetében az Automation-fiók  észleli a módosításokat, és Hiányos állapotot jelenít meg a fiókHoz a Futtatott fiókok tulajdonságpanelen.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Hiányos futtatási fiókkonfiguráció.":::

Amikor kiválasztja a fiókot, a fióktulajdonságok panelen a következő hibaüzenet jelenik meg:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Ezeket a fiókkal kapcsolatos problémákat gyorsan [](create-run-as-account.md) megoldhatja [a](delete-run-as-account.md) fiók törlésével és újra létrehozásával.

## <a name="next-steps"></a>Következő lépések

* [Alkalmazásobjektumok és szolgáltatásnév-objektumok.](../active-directory/develop/app-objects-and-service-principals.md)
* [Tanúsítványok áttekintése a Azure Cloud Services.](../cloud-services/cloud-services-certs-create.md)
* A futtatásként használt fiók létrehozásához vagy újra létrehozásához tekintse meg [a következőt: Run As account (Futtatás fiók létrehozása).](create-run-as-account.md)
* Ha már nincs szüksége egy futtatott fiókra, tekintse meg a következőt: [Run As account (Futtatás fiók törlése).](delete-run-as-account.md)
