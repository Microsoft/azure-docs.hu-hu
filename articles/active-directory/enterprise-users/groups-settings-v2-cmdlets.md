---
title: PowerShell V2 példák a csoportok kezelésére – Azure AD | Microsoft Docs
description: Ez az oldal PowerShell-példákkal segíti a csoportok kezelését Azure Active Directory
keywords: Azure AD, Azure Active Directory, PowerShell, csoportok, csoport kezelése
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12613362d9fc67dea14b41ece19a34febb489dda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860643"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Azure Active Directory 2-es verziójú parancsmagok a csoport kezeléséhez

> [!div class="op_single_selector"]
> - [Azure Portal](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)
>
>

Ez a cikk példákat tartalmaz arra, hogyan használhatja a PowerShell-t a csoportok kezelésére Azure Active Directory (Azure AD).  Emellett azt is bemutatja, hogyan lehet beállítani az Azure AD PowerShell-modult. Először [le kell töltenie az Azure ad PowerShell-modult](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Az Azure AD PowerShell-modul telepítése

Az Azure AD PowerShell-modul telepítéséhez használja az alábbi parancsokat:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

A következő parancs használatával ellenőrizheti, hogy a modul használatra kész-e:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Most már elkezdheti használni a parancsmagokat a modulban. Az Azure AD-modulban található parancsmagok teljes leírását a [Azure Active Directory PowerShell 2-es verziójának](/powershell/azure/active-directory/install-adv2)online dokumentációjában találja.

> [!NOTE]
> Az Azure AD PowerShell-parancsmagok nem működnek az új PowerShell 7 használatával, mivel a .net Core-on alapul. Tisztában vagyunk azzal, hogy a frissítés folyamatban van. Mostantól javasoljuk, hogy az Azure AD PowerShell-műveletekhez használni kívánt Windows PowerShell 5. x modult használja. 


## <a name="connect-to-the-directory"></a>Kapcsolódás a címtárhoz

A csoportok Azure AD PowerShell-parancsmagokkal történő kezelésének megkezdése előtt össze kell kapcsolni a PowerShell-munkamenetet a felügyelni kívánt címtárral. Használja az alábbi parancsot:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

A parancsmag kérni fogja a címtár eléréséhez használni kívánt hitelesítő adatokat. Ebben a példában a szolgáltatást használjuk karen@drumkit.onmicrosoft.com a bemutató könyvtár eléréséhez. A parancsmag visszaigazolja a munkamenet sikeres csatlakoztatását a címtárhoz:

```powershell
    Account                       Environment Tenant ID
    -------                       ----------- ---------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

Most már elkezdheti a AzureAD-parancsmagok használatát a csoportok kezeléséhez a címtárban.

## <a name="retrieve-groups"></a>Csoportok lekérése

A meglévő csoportok címtárból való lekéréséhez használja a Get-AzureADGroups parancsmagot. 

A címtárban lévő összes csoport lekéréséhez használja a következő parancsmagot paraméterek nélkül:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

A parancsmag az összes csoportot visszaadja a csatlakoztatott címtárban.

A-objectID paraméterrel lekérhet egy adott csoportot, amelyhez meg kell adnia a csoport objectID:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

A parancsmag most visszaadja azt a csoportot, amelynek objectID megegyezik a megadott paraméter értékével:

```powershell
    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

A-Filter paraméter használatával kereshet egy adott csoportot. Ez a paraméter egy ODATA szűrő záradékot használ, és visszaadja az összes olyan csoportot, amely megfelel a szűrőnek, ahogy az alábbi példában is látható:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

> [!NOTE]
> Az Azure AD PowerShell-parancsmagok implementálják a OData lekérdezési szabványt. További információ: **$Filter** a [OData rendszer-lekérdezési beállításokban az OData végpont használatával](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)#BKMK_filter).

## <a name="create-groups"></a>Csoportok létrehozása

Ha új csoportot szeretne létrehozni a címtárban, használja a New-AzureADGroup parancsmagot. Ez a parancsmag létrehoz egy "marketing" nevű új biztonsági csoportot:

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Frissítési csoportok

Meglévő csoport frissítéséhez használja a Set-AzureADGroup parancsmagot. Ebben a példában az "Intune-rendszergazdák" csoport DisplayName tulajdonságát módosítjuk. Először a Get-AzureADGroup parancsmaggal és a szűréssel keresjük meg a csoportot a DisplayName attribútum használatával:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Ezután a Description (Leírás) tulajdonságot az "Intune-eszköz rendszergazdája" értékre változtatjuk:

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Ha már megtalálta a csoportot, a Description (Leírás) tulajdonság frissül, hogy tükrözze az új értéket:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"

    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

## <a name="delete-groups"></a>Csoportok törlése

A címtárból származó csoportok törléséhez használja a Remove-AzureADGroup parancsmagot a következő módon:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Csoporttagság kezelése

### <a name="add-members"></a>Tagok hozzáadása

Ha új tagokat szeretne felvenni egy csoportba, használja a Add-AzureADGroupMember parancsmagot. Ezzel a paranccsal egy tagot adhat hozzá az előző példában használt Intune-rendszergazdák csoportjához:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

A-ObjectId paraméter annak a csoportnak a ObjectID, amelyhez hozzá szeretne adni egy tagot, a-RefObjectId pedig annak a felhasználónak a ObjectID, akit tagként hozzá kíván adni a csoporthoz.

### <a name="get-members"></a>Tagok beolvasása

Egy csoport meglévő tagjainak beszerzéséhez használja a Get-AzureADGroupMember parancsmagot, az alábbi példában látható módon:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Tagok eltávolítása

Ha el szeretné távolítani a csoportba előzőleg hozzáadott tagot, használja a Remove-AzureADGroupMember parancsmagot, ahogy az itt látható:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Tagok ellenőrzése

Egy felhasználó csoport-tagságának ellenőrzéséhez használja a Select-AzureADGroupIdsUserIsMemberOf parancsmagot. Ez a parancsmag annak a felhasználónak a ObjectId, amelynek a csoport tagságát ellenőriznie kell, valamint azoknak a csoportoknak a listáját, amelyeknek a tagságát ellenőriznie kell. A csoportok listáját "Microsoft. Open. AzureAD. Model. GroupIdsForMembershipCheck" típusú összetett változó formájában kell megadni, ezért először létre kell hoznia egy változót a következő típussal:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

A következő lépésben az összetett változó "GroupIds" attribútumának beadásához szükséges groupIds értékeket adja meg:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Ha a ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea rendelkező felhasználó csoport-tagságát szeretné ellenőrizni a $g-ban található csoportokkal, a következőt kell használnia:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

A visszaadott érték azoknak a csoportoknak a listája, amelyeknek ez a felhasználó a tagja. Ezt a módszert alkalmazhatja a névjegyek, csoportok vagy egyszerű szolgáltatások tagságának megadására is a csoportok adott listájához a Select-AzureADGroupIdsContactIsMemberOf, a Select-AzureADGroupIdsGroupIsMemberOf vagy a Select-AzureADGroupIdsServicePrincipalIsMemberOf használatával

## <a name="disable-group-creation-by-your-users"></a>Csoport létrehozásának letiltása a felhasználók számára

Megakadályozhatja, hogy a nem rendszergazda felhasználók biztonsági csoportokat hozzanak létre. A Microsoft Online Directory Services (MSODS) alapértelmezett viselkedése, hogy lehetővé tegye a nem rendszergazda felhasználók számára csoportok létrehozását, függetlenül attól, hogy az önkiszolgáló csoportos felügyelet (CSOPORTKEZELÉS) is engedélyezve van-e. A CSOPORTKEZELÉS beállítás csak a saját alkalmazások hozzáférési paneljén működik.

A csoportok létrehozásának letiltása a nem rendszergazda felhasználók számára:

1. Győződjön meg arról, hogy a nem rendszergazda felhasználók számára engedélyezett a csoportok létrehozása:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Ha visszatér `UsersPermissionToCreateGroupsEnabled : True` , akkor a nem rendszergazda felhasználók létrehozhatnak csoportokat. A funkció letiltása:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Csoportok tulajdonosainak kezelése

A tulajdonosok egy csoportba való felvételéhez használja a Add-AzureADGroupOwner parancsmagot:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

A-ObjectId paraméter annak a csoportnak a ObjectID, amelyhez tulajdonost kíván hozzáadni, a-RefObjectId pedig annak a felhasználónak vagy egyszerű szolgáltatásnak a ObjectID, amelyet a csoport tulajdonosaként szeretne hozzáadni.

Egy csoport tulajdonosainak beolvasásához használja a Get-AzureADGroupOwner parancsmagot:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

A parancsmag a megadott csoport tulajdonosainak (felhasználók és egyszerű szolgáltatások) listáját adja vissza:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Ha el szeretne távolítani egy tulajdonost egy csoportból, használja a Remove-AzureADGroupOwner parancsmagot:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Fenntartott aliasok

Egy csoport létrehozásakor bizonyos végpontok lehetővé teszik a végfelhasználó számára a csoport e-mail-címének részeként használandó mailNickname vagy alias megadását. A következő, magas jogosultságú e-mail-aliasokkal rendelkező csoportokat csak az Azure AD globális rendszergazdája hozhatja létre. 
  
* visszaélés
* felügyelet
* adminisztrátor
* hostmaster
* majordomo
* postamester
* legfelső szintű
* biztonságos
* biztonság
* SSL – rendszergazda
* Webmester

## <a name="group-writeback-to-on-premises-preview"></a>Helyszíni visszaírási (előzetes verzió)

Napjainkban számos csoport továbbra is a helyszíni Active Directory felügyelhető. A Felhőbeli csoportok helyi környezetbe való szinkronizálására irányuló kérések megválaszolásához Microsoft 365 groups visszaírási funkció az Azure AD-hez már elérhető az előzetes verzióhoz.

A Microsoft 365 csoportok létrehozása és kezelése a felhőben történik. A visszaírási funkció lehetővé teszi, hogy a Microsoft 365 csoportokat terjesztési csoportként írja egy Active Directory erdőbe, amelyen az Exchange telepítve van. A helyszíni Exchange-postaládákkal rendelkező felhasználók ezt követően e-maileket küldhetnek és fogadhatnak ezekből a csoportokból. A csoport visszaírási szolgáltatása nem támogatja az Azure AD biztonsági csoportokat vagy terjesztési csoportokat.

További részletekért tekintse meg a [Azure ad Connect Sync szolgáltatás](../hybrid/how-to-connect-syncservice-features.md)dokumentációját.

A Microsoft 365 Group visszaírási az Azure Active Directory (Azure AD) nyilvános előzetes verziója, amely minden fizetős Azure AD-licenccel elérhető. Az előzetes verziókkal kapcsolatos jogi információkhoz tekintse meg a [Microsoft Azure előzetes verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="next-steps"></a>Következő lépések

[Azure Active Directory-parancsmagokkal](/powershell/azure/active-directory/install-adv2)kapcsolatban további Azure Active Directory PowerShell-dokumentációt talál.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
