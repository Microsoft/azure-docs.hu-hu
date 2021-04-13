---
title: Azure AD Connect Cloud kiépítési ügynök gMSA PowerShell-parancsmagjai
description: Megtudhatja, hogyan használhatja a Azure AD Connect Cloud kiépítési ügynököt gMSA PowerShell-parancsmagokkal.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46b5c94c84bdbd0e5bb0d7b08ba9296ebfcd1838
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306058"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Azure AD Connect Cloud kiépítési ügynök gMSA PowerShell-parancsmagjai

A dokumentum célja, hogy leírja a Azure AD Connect Cloud kiépítési ügynök gMSA PowerShell-parancsmagokat. Ezek a parancsmagok lehetővé teszik a szolgáltatásfiók (gmsa) által alkalmazott engedélyek részletesebb részletességét. Alapértelmezés szerint a Azure AD Connect Cloud Sync az alapértelmezett gmsa vagy az egyéni gmsa Azure AD Connecthoz hasonló összes engedélyt alkalmaz. 

Ez a dokumentum a következő parancsmagokat foglalja magában:  

`Set-AADCloudSyncRestrictedPermissions`

`Set-AADCloudSyncPermissions` 

## <a name="how-to-use-the-cmdlets"></a>A parancsmagok használata:  

Ezeknek a parancsmagoknak a használatához a következő előfeltételek szükségesek.

1. Telepítse a kiépítési ügynököt. 
2. A kiépítési ügynök PS-moduljának importálása egy PowerShell-munkamenetbe. 

 ```PowerShell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
 ```
3. Távolítsa el a meglévő engedélyeket.  A szolgáltatásfiók összes meglévő engedélyének eltávolítása, kivéve az önhasználatot: `Set-AADCloudSyncRestrictedPermission` .  

    Ehhez a parancsmaghoz meg kell adni egy nevű paramétert `Credential` , amely átadható, vagy ha az hívása nélkül kéri a rendszer.

    Változó használata  

   `$credential = Get-Credential` 

   Ekkor a rendszer megkéri a felhasználót, hogy adja meg a felhasználónevet és a jelszót. A hitelesítő adatoknak legalább tartományi rendszergazdai jogosultsággal kell rendelkezniük (annak a tartománynak, ahol az ügynök telepítve van), a vállalati rendszergazda is lehet. 

4.  Ezután meghívhatja a parancsmagot a további engedélyek eltávolítására: 
   ```PowerShell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```
5. Vagy egyszerűen hívhat 

   `Set-AADCloudSyncRestrictedPermissions` amely kéri a hitelesítő adatokat. 

 6.  Adja meg az adott engedély típusát.  A hozzáadott engedélyek ugyanazok, mint a Azure AD Connect.  Az engedélyek beállítására vonatkozó példákat a [set-AADCloudSyncPermissions](#using-set-aadcloudsyncpermissions) alább találja.

## <a name="using-set-aadcloudsyncpermissions"></a>Set-AADCloudSyncPermissions használata 
`Set-AADCloudSyncPermissions` a a következő engedélyezési típusokat támogatja, amelyek azonosak a Azure AD Connect által használt engedélyekkel. A következő engedélyezési típusok támogatottak: 

|Engedély típusa|Description|
|-----|-----|
|BasicRead| Lásd: Azure AD Connect [BasicRead](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions) engedélyei|
|PasswordHashSync|Lásd: Azure AD Connect [PasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization) engedélyei|
|PasswordWriteBack|Lásd: Azure AD Connect [PasswordWriteBack](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback) engedélyei|
|HybridExchangePermissions|Lásd: Azure AD Connect [HybridExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment) engedélyei| 
|ExchangeMailPublicFolderPermissions| Lásd: Azure AD Connect [ExchangeMailPublicFolderPermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders-preview) engedélyei| 
|CloudHR| A "teljes hozzáférés" a "leszármazott felhasználói objektumok" és a "felhasználói objektumok létrehozása/törlése" elemre vonatkozik ezen az objektumon és az összes leszármazott objektumon.| 
|Mind|hozzáadja a fenti engedélyeket.| 



A AADCloudSyncPermissions kétféleképpen is használhatja:
- [Bizonyos engedélyek megadása az összes konfigurált tartományhoz](#grant-a-certain-permission-to-all-configured-domains) 
- [Bizonyos engedélyek megadása egy adott tartomány számára](#grant-a-certain-permission-to-a-specific-domain) 
## <a name="grant-a-certain-permission-to-all-configured-domains"></a>Bizonyos engedélyek megadása az összes konfigurált tartományhoz 
Ha bizonyos engedélyeket ad az összes konfigurált tartományhoz, vállalati rendszergazdai fiók használatára lesz szükség.


 ```PowerShell
Set-AADCloudSyncPermissions -PermissionType “Any mentioned above” -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>Bizonyos engedélyek megadása egy adott tartomány számára 
Bizonyos engedélyek adott tartományhoz való megadása megköveteli a felvenni kívánt tartomány legalább tartományi rendszergazdai fiókjának használatát.


 ```PowerShell
Set-AADCloidSyncPermissions -PermissionType “Any mentioned above” -TargetDomain “FQDN of domain” (has to be already configured through wizard) -TargetDomaincredential $credential(same as above) 
```
 

Megjegyzés: 1. A hitelesítő adatoknak legalább vállalati rendszergazdának kell lenniük. 

2. A hitelesítő adatok lehetnek tartományi rendszergazda vagy vállalati rendszergazda is. 

  

