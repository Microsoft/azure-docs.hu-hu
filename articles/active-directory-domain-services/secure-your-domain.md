---
title: Biztonságos Azure AD Domain Services | Microsoft Docs
description: Megtudhatja, hogyan tilthatja le a gyenge titkosításokat, a régi protokollokat és az NTLM jelszó-kivonatolási szinkronizálást egy Azure Active Directory Domain Services felügyelt tartományhoz.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: e7b15f79d8b63c13718d309a16eb9b71648699a1
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84734316"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-active-directory-domain-services-managed-domain"></a>Az Azure Active Directory Domain Services felügyelt tartomány biztonságossá tételéhez tiltsa le a gyenge titkosításokat és a jelszó-kivonatolási szinkronizálást

Alapértelmezés szerint a Azure Active Directory Domain Services (Azure AD DS) lehetővé teszi a titkosítások, például az NTLM v1 és a TLS v1 használatát. Ezek a titkosítási funkciók bizonyos örökölt alkalmazásokhoz szükségesek lehetnek, de gyengenak számítanak, és ha nincs rá szükségük, le lehet tiltani őket. Ha Azure AD Connect használatával helyszíni hibrid kapcsolattal rendelkezik, le is tilthatja az NTLM-jelszó kivonatok szinkronizálását.

Ez a cikk bemutatja, hogyan tilthatja le az NTLM v1 és a TLS v1 titkosítást, és letiltja az NTLM-jelszó kivonatának szinkronizálását.

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához a következő erőforrásokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, [hozzon létre és konfiguráljon egy Azure Active Directory Domain Services felügyelt tartományt][create-azure-ad-ds-instance].
* Az Azure PowerShell telepítése és konfigurálása.
    * Ha szükséges, kövesse az utasításokat a [Azure PowerShell modul telepítéséhez és az Azure-előfizetéshez való kapcsolódáshoz](/powershell/azure/install-az-ps).
    * Győződjön meg róla, hogy bejelentkezik az Azure-előfizetésbe a [AzAccount][Connect-AzAccount] parancsmag használatával.
* Az Azure AD PowerShell telepítése és konfigurálása.
    * Ha szükséges, kövesse az [Azure ad PowerShell-modul telepítésére és az Azure ad-hez való kapcsolódásra](/powershell/azure/active-directory/install-adv2)vonatkozó utasításokat.
    * Győződjön meg arról, hogy bejelentkezik az Azure AD-bérlőbe a [AzureAD][Connect-AzureAD] parancsmag használatával.

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Gyenge titkosítási és NTLM-jelszó-kivonatolási szinkronizálás letiltása

A gyenge titkosítási csomagok és az NTLM hitelesítő adatok kivonatának szinkronizálásának letiltásához jelentkezzen be az Azure-fiókjába, majd szerezze be az Azure AD DS-erőforrást a [Get-AzResource][Get-AzResource] parancsmag használatával:

> [!TIP]
> Ha a [Get-AzResource][Get-AzResource] parancs használatával hibaüzenetet kap, hogy a *Microsoft. HRE/DomainServices* erőforrás nem létezik, [emelje a hozzáférést az összes Azure-előfizetés és-felügyeleti csoport kezeléséhez][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Ezután adja meg a *DomainSecuritySettings* a következő biztonsági beállítások konfigurálásához:

1. Tiltsa le az NTLM v1 támogatását.
2. Tiltsa le az NTLM-jelszó kivonatok szinkronizálását a helyszíni AD-ből.
3. Tiltsa le a TLS v1-et.

> [!IMPORTANT]
> A felhasználók és a szolgáltatásfiókok nem hajthatnak végre LDAP egyszerű kötéseket, ha letiltja az NTLM-jelszó kivonatának szinkronizálását az Azure AD DS felügyelt tartományban. Ha LDAP egyszerű kötéseket kell végrehajtania, ne állítsa be a *"SyncNtlmPasswords" = "Letiltva";* biztonsági konfiguráció beállítást a következő parancsban.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Végül alkalmazza a definiált biztonsági beállításokat a felügyelt tartományra a [set-AzResource][Set-AzResource] parancsmag használatával. Az első lépésből válassza ki az Azure AD DS-erőforrást, és az előző lépés biztonsági beállításait.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Néhány percet vesz igénybe, hogy a biztonsági beállítások a felügyelt tartományra legyenek alkalmazva.

## <a name="next-steps"></a>További lépések

További információ a szinkronizálási folyamatról: [objektumok és hitelesítő adatok szinkronizálása felügyelt tartományokban][synchronization].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD