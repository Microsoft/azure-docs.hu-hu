---
title: Engedélyezés vagy Letiltás adott szervezetek számára – Azure AD
description: Azt mutatja, hogy a rendszergazda hogyan használhatja a Azure Portal vagy a PowerShell-t egy hozzáférési vagy megtagadási lista beállítására, amely lehetővé teszi vagy letiltja a B2B-felhasználók számára bizonyos tartományokat.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb5a785cba4a560fc15963363d4e5f6b811af97a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99548831"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Adott szervezetek B2B-felhasználók felé irányuló meghívásainak engedélyezése vagy letiltása

Az engedélyezési és a megtagadási lista használatával engedélyezheti vagy letilthatja a VÁLLALATKÖZI felhasználók számára az adott szervezetektől érkező meghívókat. Ha például le szeretné tiltani a személyes e-mail-címek tartományait, beállíthat egy megtagadási listát, amely olyan tartományokat tartalmaz, mint a Gmail.com és a Outlook.com. Ha vállalata más vállalatokkal is rendelkezik, mint például a Contoso.com, a Fabrikam.com és a Litware.com, és csak ezekre a szervezetekre szeretné korlátozni a meghívókat, hozzáadhat Contoso.com, Fabrikam.com és Litware.com is az engedélyezési listához.
  
## <a name="important-considerations"></a>Fontos szempontok

- Létrehozhat egy engedélyezési listát vagy egy megtagadási listát is. Mindkét típusú listát nem lehet beállítani. Alapértelmezés szerint az engedélyezési listán nem szereplő tartományok találhatók a megtagadási listán, és fordítva. 
- Szervezeten belül csak egy házirendet hozhat létre. A házirendet frissítheti, hogy több tartományt is tartalmazzon, vagy törölheti is a szabályzatot egy új létrehozásához. 
- Az engedélyezési vagy megtagadási listához felvehető tartományok számát csak a házirend mérete korlátozza. Ez a korlát a karakterek számára vonatkozik, így rövidebb tartományokhoz vagy kevesebb tartományhoz is tartozhat. A teljes szabályzat maximális mérete 25 KB (25 000 karakter), amely tartalmazza az engedélyezési lista vagy a Megtagadás listát, valamint az egyéb funkciókhoz konfigurált egyéb paramétereket.
- Ez a lista a OneDrive for Business és a SharePoint Online engedélyezési/letiltási listáktól függetlenül működik. Ha korlátozni szeretné az egyes fájlmegosztást a SharePoint Online-ban, be kell állítania egy engedélyezési vagy letiltási listát a OneDrive for Business és a SharePoint Online szolgáltatáshoz. További információ: [korlátozott tartományok megosztása a SharePoint Online-ban és a OneDrive for Business](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- A lista nem vonatkozik azokra a külső felhasználókra, akik már beváltották a meghívót. A lista a lista beállítása után lesz érvényesítve. Ha egy felhasználói meghívás függő állapotban van, és olyan házirendet állít be, amely blokkolja a tartományt, a felhasználó a meghívót beváltó kísérlet sikertelen lesz.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Az engedélyezési vagy letiltási lista szabályzatának beállítása a portálon

Alapértelmezés szerint a **meghívókat bármely tartományba (a legtöbb befogadó)** beállítás engedélyezi. Ebben az esetben bármely szervezettől hívhat meg VÁLLALATKÖZI felhasználókat.

### <a name="add-a-deny-list"></a>Megtagadási lista hozzáadása

Ez a leggyakoribb forgatókönyv, ahol a szervezet szinte bármilyen szervezetet szeretne használni, de meg szeretné akadályozni, hogy a felhasználók az adott tartományokban legyenek meghívva B2B-felhasználóként.

Megtagadási lista hozzáadása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **Azure Active Directory**  >  **felhasználó**  >  **felhasználói beállításait**.
3. A **külső felhasználók** területen válassza a **külső együttműködési beállítások kezelése** lehetőséget.
4. Az **együttműködési korlátozások** területen válassza **a meghívások megtagadása a megadott tartományokra** lehetőséget.
5. A **céltartomány** területen adja meg a blokkolni kívánt tartományok egyikének nevét. Több tartománynál adja meg az egyes tartományokat egy új sorban. Például:

   ![A megtagadási beállítás megjelenítése a hozzáadott tartományokkal](./media/allow-deny-list/DenyListSettings.png)
 
6. Ha elkészült, kattintson a **Mentés** gombra.

Miután beállította a házirendet, ha egy felhasználót egy letiltott tartományból próbál meghívni, megjelenik egy üzenet, amely azt jelzi, hogy a felhasználó tartománya jelenleg le van tiltva a Meghívási szabályzatban.
 
### <a name="add-an-allow-list"></a>Engedélyezési lista hozzáadása

Ez egy szigorúbb konfiguráció, amelyben meghatározott tartományokat állíthat be az engedélyezési listán, és korlátozhatja a meghívókat a nem említett más szervezetekre vagy tartományokra. 

Ha az engedélyezési listát szeretné használni, ügyeljen arra, hogy az üzleti igények teljes körű kiértékeléséhez időt szánjon. Ha a szabályzatot túlságosan korlátozóként teszi meg, a felhasználók dönthetnek úgy, hogy e-mailben küldenek dokumentumokat, vagy más, nem engedélyezett együttműködési módszereket is megkereshetnek.


Engedélyezési lista hozzáadása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **Azure Active Directory**  >  **felhasználó**  >  **felhasználói beállításait**.
3. A **külső felhasználók** területen válassza a **külső együttműködési beállítások kezelése** lehetőséget.
4. Az **együttműködési korlátozások** területen jelölje be **a csak a megadott tartományokra vonatkozó meghívások engedélyezése (a legszigorúbb)** jelölőnégyzetet.
5. A **céltartomány** területen adja meg az engedélyezni kívánt tartományok egyikének nevét. Több tartománynál adja meg az egyes tartományokat egy új sorban. Például:

   ![Az engedélyezési beállítás megjelenítése a hozzáadott tartományokkal](./media/allow-deny-list/AllowListSettings.png)
 
6. Ha elkészült, kattintson a **Mentés** gombra.

Miután beállította a házirendet, ha olyan tartományból próbál meg felhasználót meghívni, amely nem szerepel az engedélyezési listán, megjelenik egy üzenet, amely azt jelzi, hogy a felhasználó tartománya jelenleg le van tiltva a Meghívási szabályzatban.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Váltás az engedélyezéstől a megtagadási listára és fordítva 

Ha az egyik házirendből a másikra vált, a rendszer elveti a meglévő házirend-konfigurációt. A kapcsoló elvégzése előtt készítsen biztonsági másolatot a konfiguráció részleteiről. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Az engedélyezési vagy letiltási lista házirend beállítása a PowerShell használatával

### <a name="prerequisite"></a>Előfeltétel

> [!Note]
> A AzureADPreview modul nem teljes mértékben támogatott modul, mert előzetes verzióban érhető el. 

Az engedélyezési vagy megtagadási lista PowerShell használatával történő beállításához telepítenie kell a Windows PowerShell Azure Active Directory moduljának előzetes verzióját. Konkrétan telepítse a AzureADPreview modul 2.0.0.98 vagy újabb verzióját.

A modul verziójának (és annak telepítésének megtekintése):
 
1. Nyissa meg a Windows PowerShellt emelt szintű felhasználóként (Futtatás rendszergazdaként). 
2. A következő parancs futtatásával ellenőrizze, hogy rendelkezik-e a számítógépen telepített Windows PowerShell Azure Active Directory moduljának bármelyik verziójával:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Ha a modul nincs telepítve, vagy nem rendelkezik a szükséges verzióval, hajtsa végre az alábbi műveletek egyikét:

- Ha a rendszer nem ad vissza eredményt, futtassa a következő parancsot a AzureADPreview modul legújabb verziójának telepítéséhez:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Ha csak a AzureAD modul jelenik meg az eredmények között, futtassa a következő parancsokat a AzureADPreview modul telepítéséhez: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Ha a találatok között csak a AzureADPreview modul látható, de a verziószáma kisebb, mint a 2.0.0.98, futtassa a következő parancsokat a frissítéséhez: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Ha a AzureAD és a AzureADPreview modulok is megjelennek az eredmények között, de a AzureADPreview modul verziója kisebb, mint 2.0.0.98, a frissítéshez futtassa a következő parancsokat: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>A szabályzat konfigurálásához használja a AzureADPolicy-parancsmagokat.

Engedélyezési vagy megtagadási lista létrehozásához használja a [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot. Az alábbi példa bemutatja, hogyan állíthat be egy megtagadási listát, amely letiltja a "live.com" tartományt.

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

A következő példában ugyanaz a példa látható, de a házirend-definíció inline.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Az engedélyezési vagy letiltási lista házirend beállításához használja a [set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot. Például:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

A szabályzat beszerzéséhez használja a [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot. Például:

```powershell
$currentpolicy = Get-AzureADPolicy -All $true | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

A szabályzat eltávolításához használja a [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot. Például:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Következő lépések

- Az Azure AD B2B áttekintése: [Mi az az Azure ad B2B Collaboration?](what-is-b2b.md)
- További információ a feltételes hozzáférésről és a B2B együttműködésről: [feltételes hozzáférés a B2B együttműködés felhasználói](conditional-access.md)számára.
