---
title: Partner-azonosító összekapcsolása a Power apps-fiókokkal az Azure-beli hitelesítő adataival
description: Ez a cikk segítséget nyújt a Microsoft-partnereknek az Azure-beli hitelesítő adataik használatával, hogy segítséget nyújtsanak az ügyfeleknek a Microsoft Power
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.openlocfilehash: adaff7a6b8559fe9604412a44eced6c490231e3c
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103603833"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>Partner-azonosító csatolása a Power apps-fiókokhoz

Ez a cikk segítséget nyújt a Microsoft-partnereknek, akik a Power apps-szolgáltatók, hogy a szolgáltatást a Microsoft Power Apps szolgáltatás ügyfeleihez rendelik. Ha (a Microsoft-partner) felügyeli, konfigurálja és támogatja a Power Apps szolgáltatásait az ügyfél számára, hozzáférhet az ügyfél környezetéhez. Az Azure-beli hitelesítő adataival és a partneri rendszergazdai hivatkozással (PAL) társíthatja a partner hálózati AZONOSÍTÓját a szolgáltatás kézbesítéséhez használt fiók hitelesítő adataival.

A PAL lehetővé teszi a Microsoft számára, hogy azonosítsa és felismerje a Power apps-ügyfelekkel rendelkező partnereket. A Microsoft attribútumai a partner Szervezetének a fiók engedélyei (Power apps szerepkör) és a hatókör (bérlő, Erőforráscsoport, erőforrás) alapján történő használatát használják.

## <a name="get-access-from-your-customer"></a>Hozzáférés kérése az ügyféltől

A partner-azonosító csatolása előtt az ügyfélnek a következő lehetőségek egyikével kell hozzáférést biztosítania a Power apps-erőforrásokhoz:

- **Vendég felhasználó** – az ügyfele felveheti Önt vendég felhasználóként, és hozzárendelhet bármely Power apps-szerepkört. További információkért tekintse meg a [Vendégfelhasználók hozzáadása másik címtárból](../../active-directory/external-identities/what-is-b2b.md) szakaszt.
- **Directory-fiók** – az ügyfél létrehozhat egy felhasználói fiókot saját címtárában, és hozzárendelhet bármely Power apps-szerepkört.
- **Egyszerű szolgáltatásnév** – az ügyfél hozzáadhat egy alkalmazást vagy parancsfájlt a szervezetében a címtárában, és hozzárendelheti a Power apps szerepkört. Az alkalmazás vagy szkript identitása szolgáltatásnévként ismert.
- **Meghatalmazott rendszergazda** – az ügyfél delegálhat egy erőforráscsoportot, hogy a felhasználók a bérlőn belül is működjenek. További információkért lásd [: partnerek: a meghatalmazott rendszergazda](/power-platform/admin/for-partners-delegated-administrator).

## <a name="link-customer-to-a-partner-id"></a>Ügyfél összekapcsolása partner-AZONOSÍTÓval

Ha hozzáfér az ügyfél erőforrásaihoz, használja a Azure Portal, a PowerShell vagy az Azure CLI-t az Microsoft Partner Network-azonosító (MPN-azonosító) a felhasználói AZONOSÍTÓhoz vagy az egyszerű szolgáltatáshoz való csatolásához. A partner AZONOSÍTÓjának összekapcsolása minden ügyfél-Bérlővel.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Az Azure Portal használata egy új partnerazonosítóhoz való csatolásához

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Lépjen a [Csatolás partnerazonosítóhoz](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) területre az Azure Portalon.
1. Adja meg a szervezet [Microsoft Partner Network](https://partner.microsoft.com/) -azonosítóját. Ügyeljen arra, hogy a partneri profilban látható  **társított MPN-azonosítót**  használja.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="Képernyőfelvétel: a partner azonosító ablakára mutató hivatkozás." lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. Ha másik ügyfélhez szeretné kapcsolni a partner AZONOSÍTÓját, váltson át a címtárra. A **váltás könyvtár** alatt válassza ki a megfelelő könyvtárat.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="Képernyőfelvétel: a címtár + előfizetés ablak, ahol átválthatja a könyvtárat." lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>A PowerShell használata egy új partnerazonosítóhoz való csatolásához

Telepítse az az [. ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) Azure PowerShell modult.

Jelentkezzen be az ügyfél bérlője vagy a felhasználói fiókkal, vagy az egyszerű szolgáltatással. További információkért lásd: [Bejelentkezés a PowerShell-lel](/powershell/azure/authenticate-azureps).

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Csatolás az új partnerazonosítóhoz. A partnerazonosító megegyezik szervezet [Microsoft Partner Network](https://partner.microsoft.com/)-azonosítójával. Ügyeljen arra, hogy a partneri profilban látható **társított MPN-azonosítót**  használja.

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

Jelentkezzen be az ügyfél bérlője vagy a felhasználói fiókkal, vagy az egyszerű szolgáltatással. További információkért lásd: [Bejelentkezés az Azure CLI-vel](/cli/azure/authenticate-azure-cli).

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

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

A következő fejezetek a partner-AZONOSÍTÓk Power apps-fiókokhoz való összekapcsolásával kapcsolatos gyakori kérdéseket ismertetik.

### <a name="who-should-link-the-partner-id"></a>Kinek kell összekapcsolnia a partner AZONOSÍTÓját?

Minden olyan felhasználó, aki az ügyfél Power apps-erőforrásain dolgozik, összekapcsolhatja a partner AZONOSÍTÓját a fiókkal. Ideális esetben a PAL-beli társítást a projekt elején kell elvégezni. Azonban akkor is elvégezhető, ha az ügyfél címtárában van hozzáférése.

### <a name="can-a-partner-id-be-changed-after-its-linked"></a>A csatolás után módosítható a partnerazonosító?

Igen. A csatolt partnerazonosító módosítható, hozzáadható vagy eltávolítható. Ilyen eset lehet például, ha a vállalat alkalmazottja elhagyja a szervezetét. Egy másik példa lehet az, amikor egy projekt vagy szerződés az ügyféllel ér véget.

### <a name="what-if-a-user-has-an-account-in-more-than-one-customer-tenant"></a>Mi történik, ha egy felhasználó több ügyfélbérlőben is rendelkezik fiókkal?

A partnerazonosító és a fiók közötti hivatkozás minden ügyfél bérlője számára elkészül. Partnerazonosító csatolása az egyes ügyfélbérlőkben.

### <a name="can-other-partners-or-customers-edit-or-remove-the-link-to-the-partner-id"></a>Más partnerek vagy ügyfelek is szerkeszthetik vagy eltávolíthatják a partnerazonosítóra mutató hivatkozást?

A hivatkozás a felhasználói fiók szintjéhez kapcsolódik. Csak Ön szerkesztheti vagy távolíthatja el a partnerazonosítóra mutató hivatkozást. Az ügyfél vagy más partnerek nem szerkeszthetik vagy távolíthatják el a partnerazonosítóra mutató hivatkozást.

### <a name="which-mpn-id-should-i-use-if-my-company-has-multiple"></a>Melyik MPN-azonosítót kell használnom, ha a vállalat többel is rendelkezik?

Ügyeljen arra, hogy a partnerprofilban szereplő **társított MPN-azonosítót** használja. Ez általában a helyi fiók AZONOSÍTÓjának társítása a szervezettel.

### <a name="how-do-i-explain-pal-to-my-customer"></a>Hogyan megmagyarázni a HAVERoknak az ügyfelem?

A PAL lehetővé teszi a Microsoft számára, hogy azonosítsa és felismerje azokat a partnereket, akik segítséget nyújtanak az ügyfeleknek az üzleti célok elérésében és a Felhőbeli Az ügyfeleknek először meg kell adniuk egy partneri hozzáférést a Power apps-erőforráshoz. A hozzáférés engedélyezése után a partner Microsoft Partner Network azonosítója (MPN-azonosító) társítva van. Ez a társítás segítséget nyújt a Microsoftnak a szolgáltatók megismerésében, valamint az ügyfelek legjobb támogatásához szükséges eszközök és programok pontosításában.

### <a name="what-data-does-pal-collect"></a>Milyen adatokat gyűjt a PAL?

A PAL meglévő hitelesítő adatokkal történő társítása nem küld új ügyféladatokat a Microsoftnak. Olyan információkat biztosít a Microsoftnak, ahol a partner aktívan részt vesz az ügyfél Power apps-környezetében. A Microsoft a fiók engedélyei (Power apps-szerepkör) és a partner által az ügyfél által megadott hatókör (bérlő, Erőforráscsoport, erőforrás) alapján attribútumot adhat az ügyfél-környezettől a partner szervezetnek.

### <a name="does-pal-association-affect-the-security-of-a-customers-power-apps-environment"></a>A PAL-társítás befolyásolja az ügyfél Power apps-környezetének biztonságát?

A PAL társítás csak a partner MPN-AZONOSÍTÓját adja hozzá a már kiépített hitelesítő adatokhoz. Nem változtat semmilyen engedélyt (Power apps-szerepkör), vagy további Power apps-szolgáltatásokat biztosít a partnernek vagy a Microsoftnak.

### <a name="next-steps"></a>További lépések

- A hírekért és visszajelzések küldéséhez csatlakozzon a [Microsoft-partnerközösség](https://aka.ms/PALdiscussion) vitafórumához.
- Olvassa el az alacsony kódú alkalmazásfejlesztés [speciális szakterületét](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf) a PAL-alapú Power apps-társításhoz az alacsony kódú alkalmazásfejlesztés speciális specializációjának kialakításához.
