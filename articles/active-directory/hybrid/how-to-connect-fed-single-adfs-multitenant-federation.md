---
title: Több Azure AD-egyesítő egyetlen AD FS – Azure
description: Ebből a dokumentumból megtudhatja, hogyan vonhat össze több Azure AD-t egyetlen AD FS-szel.
keywords: federate, ADFS, AD FS, multiple tenants, single AD FS, one ADFS, multi-tenant federation, multi-forest adfs, aad connect, federation, cross-tenant federation
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 956428b6f197912e2ab7c3a94133ed9d59f37749
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279924"
---
# <a name="federate-multiple-instances-of-azure-ad-with-single-instance-of-ad-fs"></a>Több Azure AD-példány összevonása egyetlen AD FS-példánnyal

Egyetlen magas rendelkezésre állású AD FS farm összevonhat több erdőt, ha azok között kétirányú megbízhatósági kapcsolat áll fenn. Ezek az erdők megfelelhetnek ugyanannak az Azure Active Directory-címtárnak, vagy sem. Ez a cikk útmutatást nyújt az összevonás konfigurálásához egyetlen AD FS környezet és egynél több, más Azure AD-címtárra szinkronizáló erdő között.

![Több-bérlős összevonás egyetlen AD FS-szel](./media/how-to-connect-fed-single-adfs-multitenant-federation/concept.png)
 
> [!NOTE]
> Ebben az esetben az eszközvisszaírás és automatikus eszköz-csatlakoztatás nem támogatott.

> [!NOTE]
> Az Azure AD Connect esetünkben nem használható, mivel az Azure AD Connect az egyetlen Azure AD-címtárban lévő tartományok esetén használható az összevonás konfigurálására.

## <a name="steps-for-federating-ad-fs-with-multiple-azure-ad"></a>Az AD FS több Azure AD-címtárral való összevonásának lépései

Vegye figyelembe, hogy a contoso.com tartomány a contoso.onmicrosoft.com Azure Active Directory-címtárban már össze van vonva a contoso.com helyszíni Active Directory-környezetbe telepített helyszíni AD FS-szel. A fabrikam.com a fabrikam.onmicrosoft.com Azure Active Directory-címtár egy tartománya.

## <a name="step-1-establish-a-two-way-trust"></a>1. lépés: A kétirányú megbízhatósági kapcsolat létrehozása
 
Ahhoz, hogy a contoso.com-beli AD FS hitelesíthesse a fabrikam.com-beli felhasználókat, kétirányú megbízhatósági kapcsolatra van szükség a contoso.com és fabrikam.com között. A k kétirányú megbízhatósági kapcsolat létrehozásához kövesse a [cikk](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816590(v=ws.10)) iránymutatását.
 
## <a name="step-2-modify-contosocom-federation-settings"></a>2. lépés: A contoso.com összevonási beállításainak módosítása 
 
Az AD FShoz összevont egyetlen tartományhoz beállított alapértelmezett kiállító a "http \: -//ADFSServiceFQDN/ADFS/Services/Trust", például: `http://fs.contoso.com/adfs/services/trust` . Az Azure Active Directory összevont tartományonként egyedi kiállítót igényel. Mivel ugyanaz az AD FS fog két tartományt összevonni, a kiállító értékét módosítani kell, hogy minden egyes tartományhoz egyedi legyen, amelyet az AD FS az Azure Active Directoryval összevon. 
 
Az AD FS-kiszolgálón nyissa meg az Azure AD PowerShellt (ellenőrizze, hogy telepítve van-e a MSOnline modul), és hajtsa végre a következő lépéseket:
 
Csatlakozzon a contoso.com tartományt tartalmazó Azure Active Directory-címtárhoz Connect-MsolService frissítse a contoso.com összevonási beállításait Update-MsolFederatedDomain -tartománynév.contoso.com –SupportMultipleDomain
 
A tartomány-összevonási beállítás kiállítója a "http \: //contoso.com/ADFS/Services/Trust" értékre változik, és az Azure ad függő entitás megbízhatóságához a kiállítási jogcím szabálya kerül, amely az UPN-utótag alapján állítja ki a megfelelő issuerId értéket.
 
## <a name="step-3-federate-fabrikamcom-with-ad-fs"></a>3. lépés: A fabrikam.com összevonása az AD FS-szel
 
Az Azure AD PowerShell-munkamenetben hajtsa végre a következő lépéseket: Csatlakozzon a fabrikam.com tartományt tartalmazó Azure Active Directory-címtárhoz

```powershell
Connect-MsolService
```
Konvertálja a fabrikam.com felügyelt tartományt összevontra:

```powershell
Convert-MsolDomainToFederated -DomainName fabrikam.com -Verbose -SupportMultipleDomain
```
 
A fenti művelet összevonja a fabrikam.com tartományt ugyanazon AD FS-szel. Mindkét tartományban a Get-MsolDomainFederationSettings használatával ellenőrizheti a tartomány beállításait.

## <a name="next-steps"></a>Következő lépések
[Az Active Directory csatlakoztatása az Azure Active Directoryhoz](whatis-hybrid-identity.md)