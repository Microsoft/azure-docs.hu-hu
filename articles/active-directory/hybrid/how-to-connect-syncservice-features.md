---
title: A szinkronizálási szolgáltatás funkcióinak és konfigurációjának Azure AD Connect | Microsoft Docs
description: A Azure AD Connect Sync Service szolgáltatás oldalsó funkcióit ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 261ab5d0f039705a2566b7c28ff4c06778bb661a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94410538"
---
# <a name="azure-ad-connect-sync-service-features"></a>Az Azure AD Connect szinkronizálási szolgáltatás jellemzői

Azure AD Connect szinkronizálási funkciója két összetevőből áll:

* A **Azure ad Connect Sync** nevű helyszíni összetevő, más néven a **szinkronizálási motor**.
* Az Azure AD-ben (más néven **Azure ad Connect Sync Service** ) található szolgáltatás

Ez a témakör azt ismerteti, hogyan működik a **Azure ad Connect szinkronizálási szolgáltatásának** következő funkciói, és hogyan konfigurálhatja őket a Windows PowerShell használatával.

Ezeket a beállításokat a [Windows PowerShell Azure Active Directory modulja](/previous-versions/azure/jj151815(v=azure.100))konfigurálja. Töltse le és telepítse a Azure AD Connecttól függetlenül. A jelen témakörben ismertetett parancsmagok bevezetése a [2016 márciusi kiadásban történt (build 9031,1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Ha nem rendelkezik a jelen témakörben ismertetett parancsmagokkal, vagy nem ugyanazt az eredményt hozza létre, akkor győződjön meg arról, hogy a legújabb verziót futtatja.

Az Azure AD-címtár konfigurációjának megtekintéséhez futtassa a következőt: `Get-MsolDirSyncFeatures` .  
![Get-MsolDirSyncFeatures eredmény](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Ezen beállítások közül sokat csak Azure AD Connect módosíthat.

A következő beállításokat állíthatja be `Set-MsolDirSyncFeature` :

| DirSyncFeature | Megjegyzés |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Lehetővé teszi, hogy az objektumok az elsődleges SMTP-címen kívül is csatlakozzanak a userPrincipalName. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Lehetővé teszi, hogy a Szinkronizáló motor frissítse a userPrincipalName attribútumot a felügyelt/licencelt (nem összevont) felhasználók számára. |

Miután engedélyezte a szolgáltatást, nem lehet újra letiltani.

> [!NOTE]
> 2016. augusztus 24-én a szolgáltatás *duplikált attribútumának rugalmassága* alapértelmezés szerint engedélyezve van az új Azure ad-címtárakban. Ez a funkció az ezen dátum előtt létrehozott könyvtárakon is bekerül és engedélyezve lesz. E-mail-értesítést fog kapni, ha a címtár a szolgáltatás engedélyezését kéri.
> 
> 

A következő beállításokat a Azure AD Connect konfigurálja, és a nem módosítható `Set-MsolDirSyncFeature` :

| DirSyncFeature | Megjegyzés |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: az eszköz visszaírási engedélyezése](how-to-connect-device-writeback.md) |
| DirectoryExtensions |[Azure AD Connect Sync: címtárszolgáltatás-bővítmények](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency <br/> DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Lehetővé teszi egy attribútum karanténba helyezését, ha egy másik objektum duplikálása helyett a teljes objektumot nem sikerül az exportálás során. |
| Jelszókivonat szinkronizálása |[Jelszó-kivonatolási szinkronizálás megvalósítása Azure AD Connect szinkronizálással](how-to-connect-password-hash-synchronization.md) |
|Átmenő hitelesítés|[Felhasználói bejelentkezés az Azure Active Directory átmenő hitelesítésével](how-to-connect-pta.md)|
| UnifiedGroupWriteback |Group writeback (Csoportvisszaíró)|
| UserWriteback |Jelenleg nem támogatott. |

## <a name="duplicate-attribute-resiliency"></a>Ismétlődő attribútum rugalmassága

Ahelyett, hogy nem sikerül a duplikált UPN/proxyAddresses rendelkező objektumokat kiépíteni, a duplikált attribútum "karanténba helyezve", és egy ideiglenes érték lesz hozzárendelve. Az ütközés feloldása után az ideiglenes egyszerű felhasználónév automatikusan módosul a megfelelő értékre. További részletek: [identitásszinkronizálás és duplikált attribútum rugalmassága](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>UserPrincipalName – Soft Match

Ha ez a szolgáltatás engedélyezve van, a Soft-Match engedélyezve van az UPN-hez az [elsődleges SMTP-címen](https://support.microsoft.com/kb/2641663)kívül, amely mindig engedélyezve van. A Soft-Match használatával az Azure AD-ben meglévő Felhőbeli felhasználók is megegyeznek a helyszíni felhasználókkal.

Ha meg kell egyeznie a helyszíni AD-fiókokkal a felhőben létrehozott meglévő fiókokkal, és Ön nem használja az Exchange Online-t, akkor ez a funkció hasznos lehet. Ebben az esetben általában nincs oka az SMTP-attribútum beállítása a felhőben.

Ez a szolgáltatás alapértelmezés szerint be van kapcsolva az újonnan létrehozott Azure AD-címtárakhoz. Megtekintheti, hogy a szolgáltatás engedélyezve van-e a következő futtatásával:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Ha ez a funkció nincs engedélyezve az Azure AD-címtárban, akkor a következő futtatásával engedélyezheti:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>UserPrincipalName-frissítések szinkronizálása

Az UserPrincipalName attribútumnak a helyszíni szinkronizálási szolgáltatás használatával történő frissítése le lett tiltva, kivéve, ha mindkét feltétel teljesült:

* A felhasználó felügyelt (nem összevont).
* A felhasználóhoz nincs hozzárendelve licenc.

> [!NOTE]
> Március 2019-tól az összevont felhasználói fiókok UPN-változásainak szinkronizálása engedélyezett.
> 

A funkció engedélyezése lehetővé teszi, hogy a szinkronizálási motor frissítse a userPrincipalName, amikor a helyszínen módosul, és jelszó-kivonatoló szinkronizálást vagy átmenő hitelesítést használ.

Ez a szolgáltatás alapértelmezés szerint be van kapcsolva az újonnan létrehozott Azure AD-címtárakhoz. Megtekintheti, hogy a szolgáltatás engedélyezve van-e a következő futtatásával:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Ha ez a funkció nincs engedélyezve az Azure AD-címtárban, akkor a következő futtatásával engedélyezheti:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

A funkció engedélyezése után a meglévő userPrincipalName-értékek változatlanok maradnak. A helyszíni userPrincipalName-attribútum következő változásakor a felhasználók által végzett normál különbözeti szinkronizálás frissíti az egyszerű felhasználónevet.  

## <a name="see-also"></a>Lásd még

* [Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md)
* A helyszíni [identitások integrálása a Azure Active Directorykal](whatis-hybrid-identity.md).