---
title: 'Azure AD Connect: TLS 1,2 kényszerítés Azure Active Directory Connect esetén | Microsoft Docs'
description: Ismerje meg, hogyan kényszerítheti a Azure AD Connect-kiszolgálót csak Transport Layer Security (TLS) 1,2 használatára.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c610eb8fe6caf308117fa7adfc6e2f2a35c6352
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91325895"
---
# <a name="tls-12-enforcement-for-azure-ad-connect"></a>TLS 1,2 kényszerítés a Azure AD Connect

A Transport Layer Security (TLS) protokoll 1,2-es verziója egy olyan titkosítási protokoll, amely biztonságos kommunikáció biztosítására szolgál.  A TLS protokoll elsősorban az adatvédelem és az adatintegritás biztosítását célozza.  A TLS több, a 1,2-es verzióval ellátott iteráción esett át az [RFC 5246](https://tools.ietf.org/html/rfc5246)-ben.  Azure Active Directory Connect verzió 1.2.65.0, és később már teljes mértékben támogatja az Azure-hoz való kommunikációhoz csak a TLS 1,2-et.  Ebből a dokumentumból megtudhatja, hogyan kényszerítheti a Azure AD Connect-kiszolgálót csak a TLS 1,2 használatára.

## <a name="update-the-registry"></a>A beállításjegyzék frissítése
Ahhoz, hogy a Azure AD Connect-kiszolgálót csak a TLS 1,2 használatára kényszerítse, frissíteni kell a Windows Server beállításjegyzékét.  Állítsa be a következő beállításkulcsokat a Azure AD Connect kiszolgálón.

>[!IMPORTANT]
>A beállításjegyzék frissítése után újra kell indítani a Windows Servert, hogy a módosítások érvénybe lépnek.


### <a name="enable-tls-12"></a>TLS 1.2 engedélyezése
- [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\ . NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions" = DWORD: 00000001
  - "Alatt" = DWORD: 0000001
- [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\ . NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions" = DWORD: 00000001
  - "Alatt" = DWORD: 00000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ kiszolgáló]
  - "Engedélyezve" = DWORD: 00000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ kiszolgáló]
  - "DisabledByDefault" = DWORD: 00000000 
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ ügyfél]
  - "Engedélyezve" = DWORD: 00000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ ügyfél]
  - "DisabledByDefault" = DWORD: 00000000

### <a name="powershell-script-to-enable-tls-12"></a>PowerShell-parancsfájl a TLS 1,2 engedélyezéséhez
A következő PowerShell-parancsfájl használatával engedélyezheti a TLS 1,2-et a Azure AD Connect-kiszolgálón.

```powershell
    New-Item 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'Enabled' -value '1' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'DisabledByDefault' -value 0 -PropertyType 'DWord' -Force | Out-Null
    
    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'Enabled' -value '1' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'DisabledByDefault' -value 0 -PropertyType 'DWord' -Force | Out-Null
    Write-Host 'TLS 1.2 has been enabled.'
```

### <a name="disable-tls-12"></a>TLS 1,2 letiltása
- [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\ . NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions" = DWORD: 00000000
  - "Alatt" = DWORD: 0000000
- [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\ . NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions" = DWORD: 00000000
  - "Alatt" = DWORD: 00000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ kiszolgáló]
  - "Engedélyezve" = DWORD: 00000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ kiszolgáló]
  - "DisabledByDefault" = DWORD: 00000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ ügyfél]
  - "Engedélyezve" = DWORD: 00000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ ügyfél]
  - "DisabledByDefault" = DWORD: 00000001 

### <a name="powershell-script-to-disable-tls-12"></a>PowerShell-parancsfájl a TLS 1,2 letiltásához
A következő PowerShell-parancsfájl használatával tilthatja le a TLS 1,2-et a Azure AD Connect-kiszolgálón. \

```powershell
    New-Item 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'Enabled' -value '0' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'DisabledByDefault' -value 1 -PropertyType 'DWord' -Force | Out-Null
    
    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'Enabled' -value '0' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'DisabledByDefault' -value 1 -PropertyType 'DWord' -Force | Out-Null
    Write-Host 'TLS 1.2 has been disabled.'
```

## <a name="next-steps"></a>Következő lépések
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
