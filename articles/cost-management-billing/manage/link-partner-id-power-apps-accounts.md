---
title: Partnerazonosító csatolása a Power Apps azure-beli hitelesítő adataival
description: Ez a cikk segítséget nyújt a Microsoft-partnereknek az Azure-beli hitelesítő adataik használatában a Microsoft Power Apps.
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.openlocfilehash: acb22cc4b2a461e476131a83972db3e782425a39
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727707"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>Partnerazonosító csatolása a Power Apps fiókhoz

Ez a cikk segítséget nyújt a Microsoft-partnereknek, akik Power Apps, hogy szolgáltatásaikat a Microsoft Power Apps. Ha Ön (a Microsoft-partner) kezeli, konfigurálja és támogatja Power Apps-szolgáltatásokat az ügyfél számára, hozzáféréssel rendelkezik az ügyfél környezetéhez. Az Azure-beli hitelesítő adatait és egy partner-rendszergazdai hivatkozást (PAL) használva társíthatja a partnerhálózati azonosítóját a szolgáltatás kézbesítése során használt fiók hitelesítő adataival.

A PAL lehetővé teszi, hogy a Microsoft azonosítsa és felismerje az ügyfelekkel Power Apps partnereket. A Microsoft a fiók engedélyei (Power Apps szerepkör) és hatóköre (bérlő, erőforráscsoport, erőforrás) alapján a használatot a partner szervezetének jelöli.

## <a name="get-access-from-your-customer"></a>Hozzáférés kérése az ügyféltől

A partnerazonosító csatolása előtt az ügyfélnek hozzáférést kell Power Apps a saját erőforrásaihoz az alábbi lehetőségek egyikével:

- **Vendégfelhasználó** – Az ügyfél hozzáadhatja Vendégfelhasználóként, és bármilyen Power Apps szerepkört. További információkért tekintse meg a [Vendégfelhasználók hozzáadása másik címtárból](../../active-directory/external-identities/what-is-b2b.md) szakaszt.
- **Címtárfiók** – Az ügyfél létrehozhat egy felhasználói fiókot az Ön számára a saját címtárában, és bármely Power Apps szerepkört.
- **Szolgáltatásnév** – Az ügyfél hozzáadhat egy alkalmazást vagy szkriptet a szervezetből a címtárában, és bármilyen Power Apps hozzárendelhet. Az alkalmazás vagy szkript identitása szolgáltatásnévként ismert.
- **Meghatalmazott rendszergazda –** Az ügyfél delegálhat egy erőforráscsoportot, így a felhasználók a bérlőn belül dolgozhatnak rajta. További információ: [Partnerek számára: Meghatalmazott rendszergazda.](/power-platform/admin/for-partners-delegated-administrator)

## <a name="link-customer-to-a-partner-id"></a>Ügyfél csatolása egy partnerazonosítóhoz

Ha hozzáfér az ügyfél erőforrásaihoz, az Azure Portal, a PowerShell vagy az Azure CLI használatával csatolja az Microsoft Partner Network-azonosítót (MPN-azonosítót) a felhasználói azonosítóhoz vagy a szolgáltatásnévhez. Csatolja a partnerazonosítót az egyes ügyfélbérlőkhöz.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Az Azure Portal használata egy új partnerazonosítóhoz való csatolásához

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Lépjen a [Csatolás partnerazonosítóhoz](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) területre az Azure Portalon.
1. Adja meg [Microsoft Partner Network](https://partner.microsoft.com/) szervezetének azonosítóját. Győződjön meg arról, hogy a partnerprofilban látható társított  **MPN-azonosítót**  használja.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="Képernyőkép a Csatolás partnerazonosítóhoz ablakról." lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. Ha a partnerazonosítót egy másik ügyfélhez is hozzákapcsolja, váltson át a címtárra. A **Címtár váltása alatt** válassza ki a megfelelő címtárat.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="Képernyőkép a Címtár és előfizetés ablakról, ahol címtárat válthat." lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>A PowerShell használata egy új partnerazonosítóhoz való csatolásához

Telepítse az [Az.ManagementPartner Azure PowerShell](https://www.powershellgallery.com/packages/Az.ManagementPartner/) modult.

Jelentkezzen be az ügyfél bérlőjébe a felhasználói fiókkal vagy a szolgáltatásnévvel. További információkért lásd: [Bejelentkezés a PowerShell-lel](/powershell/azure/authenticate-azureps).

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Csatolás az új partnerazonosítóhoz. A partnerazonosító megegyezik szervezet [Microsoft Partner Network](https://partner.microsoft.com/)-azonosítójával. Győződjön meg arról, hogy a partnerprofilban látható társított **MPN-azonosítót**  használja.

```azurepowershell-interactive
new-AzManagementPartner -PartnerId 12345
```

A csatolt partnerazonosító lekérése

```azurepowershell-interactive
get-AzManagementPartner
```

A csatolt partnerazonosító frissítése

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

A csatolt partnerazonosító törlése

```azurepowershell-interactive
remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Az Azure CLI használata egy új partnerazonosítóhoz való csatolásához

Először telepítse az Azure CLI-bővítményt.

```azurecli-interactive
az extension add --name managementpartner
```

Jelentkezzen be az ügyfél bérlőjébe a felhasználói fiókkal vagy a szolgáltatásnévvel. További információkért lásd: [Bejelentkezés az Azure CLI-vel](/cli/azure/authenticate-azure-cli).

```azurecli-interactive
az login --tenant TenantName
```

Csatolás az új partnerazonosítóhoz. A partnerazonosító megegyezik szervezet [Microsoft Partner Network](https://partner.microsoft.com/)-azonosítójával.

```azurecli-interactive
az managementpartner create --partner-id 12345
```

A csatolt partnerazonosító lekérése

```azurecli-interactive
az managementpartner show
```

A csatolt partnerazonosító frissítése

```azurecli-interactive
az managementpartner update --partner-id 12345
```

A csatolt partnerazonosító törlése

```azurecli-interactive
az managementpartner delete --partner-id 12345
```

### <a name="next-steps"></a>További lépések

- A partner [Cost Management + Billing azonosítók](../cost-management-billing-faq.yml) fiókokhoz való csatolásával kapcsolatos kérdésekért és válaszokért olvassa el a gyakori Power Apps kérdéseit és válaszait.
- A hírekért és visszajelzések küldéséhez csatlakozzon a [Microsoft-partnerközösség](https://aka.ms/PALdiscussion) vitafórumához.
- Olvassa el [a Pal-alapú](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf) alkalmazásfejlesztéssel kapcsolatos alacsony kódú alkalmazásfejlesztési speciális specializációval kapcsolatos gyakori kérdéseket Power Apps az alacsony kódú alkalmazásfejlesztés speciális specializációhoz című témakört.
