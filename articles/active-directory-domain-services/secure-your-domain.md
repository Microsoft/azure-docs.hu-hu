---
title: Az Azure Active Directory Domain Services felügyelt tartomány Secure |} A Microsoft Docs
description: Felügyelt tartomány védelme
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: e94cd9ca049cfdfd2321ce046714506ed1f23390
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483281"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Az Azure AD Domain Services felügyelt tartomány Secure
Ez a cikk segítséget nyújt a felügyelt tartomány secure. Kapcsolja ki a gyenge titkosító csomag használatát, és NTLM hitelesítő adatok a Jelszókivonat-szinkronizálás letiltása.

## <a name="install-the-required-powershell-modules"></a>A szükséges PowerShell-modulok telepítése

### <a name="install-and-configure-azure-ad-powershell"></a>Az Azure AD PowerShell telepítése és konfigurálása
Kövesse a cikkben szereplő utasításokat [Azure AD PowerShell-modul telepítéséhez és az Azure AD connect](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Az Azure PowerShell telepítése és konfigurálása
Kövesse a cikkben szereplő utasításokat [az Azure PowerShell-modul telepítése és csatlakozás az Azure-előfizetéshez](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>A gyenge titkosító csomagok és NTLM hitelesítő adatok a Jelszókivonat-szinkronizálás letiltása
A következő PowerShell-parancsprogram használata:
1. Tiltsa le a NTLM v1-támogatást a felügyelt tartományban.
2. A helyszíni NTLM-jelszókivonatok szinkronizálásának letiltása AD.
3. Tiltsa le a TLS v1-et a felügyelt tartományban.

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

> [!IMPORTANT]
> Felhasználók (és szolgáltatásfiókok) nem hajtható végre egyszerű LDAP-kötések, ha az Azure AD Domain Services-példányon a Jelszókivonat-szinkronizálás az NTLM le van tiltva.  További információk az NTLM Jelszókivonat-szinkronizálás letiltása, [biztonságossá tétele az Azure AD tartományi szolgáltatásokkal felügyelt tartományban](secure-your-domain.md).
>
>

## <a name="next-steps"></a>További lépések
* [Szinkronizálás az Azure AD tartományi szolgáltatások ismertetése](synchronization.md)
