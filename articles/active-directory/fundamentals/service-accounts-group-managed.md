---
title: Csoportosan felügyelt szolgáltatásfiókok biztonságossá tétele | Azure Active Directory
description: Útmutató a csoportosan felügyelt szolgáltatásfiókok számítógép-fiókjainak biztonságossá tételéhez.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4c1adddbf4b13f8e299bd656443c9aaab1d55b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644827"
---
# <a name="securing-group-managed-service-accounts"></a>Csoportosan felügyelt szolgáltatásfiókok biztonságossá tétele

A csoportosan felügyelt szolgáltatásfiókok (csoportosan felügyelt szolgáltatásfiókokat) a szolgáltatások biztonságossá tételéhez használt felügyelt tartományi fiókok. a csoportosan felügyelt szolgáltatásfiókokat egyetlen kiszolgálón, vagy egy kiszolgálófarm, például hálózati Load Balancer (NLB) vagy egy Internet Information Services (IIS) kiszolgáló mögötti rendszerekben is futtathatók. Miután konfigurálta a szolgáltatásokat egy gMSA-rendszerbiztonsági tag használatára, az adott fiókhoz tartozó jelszavas kezelést a Windows kezeli.

## <a name="benefits-of-using-gmsas"></a>A csoportosan felügyelt szolgáltatásfiókokat használatának előnyei

a csoportosan felügyelt szolgáltatásfiókokat egyetlen identitási megoldást kínál nagyobb biztonsággal, miközben csökkenti az adminisztrációs terhelést:

* **Erős jelszavak beállítása**. a csoportosan felügyelt szolgáltatásfiókokat 240 bájtos véletlenszerűen generált összetett jelszavakat használ. A gMSA-jelszavak összetettsége és hossza minimálisra csökken annak valószínűsége, hogy egy szolgáltatás feltörte a találgatásos vagy a szótári támadásokat.

* A **kerékpárosok jelszavainak rendszeres** használata. a csoportosan felügyelt szolgáltatásfiókokat a Windowsba, amely 30 naponként módosítja a jelszót. A szolgáltatás-és tartományi rendszergazdáknak már nincs szükségük a jelszó módosítására, illetve a szolgáltatások kimaradásának kezelésére a szolgáltatásfiókok biztonságának megőrzése érdekében. 

* **A kiszolgálói farmok üzembe helyezésének támogatása**. A csoportosan felügyelt szolgáltatásfiókokat több kiszolgálóra való üzembe helyezésének lehetősége lehetővé teszi a terheléselosztási megoldások támogatását, ahol több gazdagép ugyanazt a szolgáltatást futtatja. 

* Az **egyszerűsített kiszolgálói egyszerű szolgáltatásnév (SPN) kezelésének támogatása**. Az egyszerű szolgáltatásnevet a PowerShell használatával állíthatja be a fiók létrehozásakor. Emellett az automatikus SPN-regisztrációt támogató szolgáltatások is megtehetik a gMSA, a megadott gMSA engedélyek megfelelően vannak beállítva. 

## <a name="when-to-use-gmsas"></a>Mikor kell használni a csoportosan felügyelt szolgáltatásfiókokat

A csoportosan felügyelt szolgáltatásfiókokat-t használja előnyben részesített fióktípusként a helyszíni szolgáltatásokhoz, kivéve, ha egy szolgáltatás (például a feladatátvételi fürtszolgáltatás) nem támogatja azt.

> [!IMPORTANT]
> A szolgáltatás éles környezetben történő üzembe helyezése előtt tesztelni kell a szolgáltatást a csoportosan felügyelt szolgáltatásfiókokat. Ehhez állítson be egy tesztkörnyezet-környezetet, és győződjön meg arról, hogy az alkalmazás használhatja a gMSA, és hozzáférhet az elérni kívánt erőforrásokhoz. További információ: [csoportosan felügyelt szolgáltatásfiókok támogatása](/system-center/scom/support-group-managed-service-accounts?view=sc-om-2019).


Ha egy szolgáltatás nem támogatja a csoportosan felügyelt szolgáltatásfiókokat használatát, a következő legjobb lehetőség egy önálló felügyelt szolgáltatásfiók (önállóan felügyelt szolgáltatásfiókot) használata. a sMSAs ugyanazokat a funkciókat biztosítja, mint a gMSA, de csak egyetlen kiszolgálón való üzembe helyezésre szolgálnak.

Ha a szolgáltatás nem használ gMSA vagy önállóan felügyelt szolgáltatásfiókot, akkor a szolgáltatást normál felhasználói fiókként kell konfigurálni. A fiók biztonságának megőrzése érdekében a szolgáltatás-és tartományi rendszergazdáknak meg kell figyelniük az erős jelszavas felügyeleti folyamatokat.

## <a name="assess-the-security-posture-of-gmsas"></a>A csoportosan felügyelt szolgáltatásfiókokat biztonsági helyzetének felmérése

a csoportosan felügyelt szolgáltatásfiókokat eleve biztonságosabbak, mint a normál felhasználói fiókok, amelyek folyamatos jelszavas kezelést igényelnek. Fontos azonban figyelembe venni a csoportosan felügyelt szolgáltatásfiókokat hatókörét, ahogy megtekinti az általános biztonsági állapotot.

A következő táblázat a csoportosan felügyelt szolgáltatásfiókokat használatának lehetséges biztonsági problémáit és enyhítéseit mutatja be.

| Biztonsági problémák| Kezelési lehetőségek |
| - | - |
| a gMSA a Kiemelt csoportok tagja. | Tekintse át a csoporttagságait. Ehhez hozzon létre egy PowerShell-parancsfájlt az összes csoporttagság enumerálásához, majd szűrje az eredő CSV-fájlt a gMSA-fájlok nevével. <br>Távolítsa el a gMSA a Kiemelt csoportok közül.<br> Csak a szolgáltatás futtatásához szükséges jogosultságokat és engedélyeket adja meg a gMSA (forduljon a szolgáltatás gyártójához). 
| a gMSA olvasási/írási hozzáféréssel rendelkezik a bizalmas erőforrásokhoz. | Bizalmas erőforrásokhoz való hozzáférés naplózása. Archiválja a naplókat egy SIEM-re, például az Azure Log Analytics vagy az Azure Sentinel elemzésre. A szükségtelen erőforrás-engedélyek eltávolítása, ha nemkívánatos hozzáférési szint észlelhető. |


## <a name="find-gmsas"></a>Csoportosan felügyelt szolgáltatásfiókokat keresése

Lehet, hogy a szervezete már létrehozta a csoportosan felügyelt szolgáltatásfiókokat. A következő PowerShell-parancsmag futtatásával kérheti le ezeket a fiókokat:

A hatékony működés érdekében a csoportosan felügyelt szolgáltatásfiókokat a felügyelt szolgáltatásfiókok szervezeti egységben (OU) kell lennie.

  
![Képernyőfelvétel a felügyelt szolgáltatásfiók szervezeti egységéről.](./media/securing-service-accounts/secure-gmsa-image-1.png)

A következő parancsokkal megkeresheti azokat a MSAs, amelyek esetleg nem lehetnek ott.

**Az összes szolgáltatásfiók megkeresése, beleértve a csoportosan felügyelt szolgáltatásfiókokat és a sMSAs:**


```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all Managed Service Accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where $_.ObjectClass -eq "msDS-GroupManagedServiceAccount”}
```

## <a name="manage-gmsas"></a>Csoportosan felügyelt szolgáltatásfiókokat kezelése

A csoportosan felügyelt szolgáltatásfiókokat kezeléséhez a következő Active Directory PowerShell-parancsmagokat használhatja:

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> A Windows Server 2012-től kezdődően a *-ADServiceAccount parancsmagok alapértelmezés szerint a csoportosan felügyelt szolgáltatásfiókokat szolgáltatással működnek. További információ a fenti parancsmagok használatáról: [**első lépések csoportosan felügyelt szolgáltatásfiókok**](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)használatával.

## <a name="move-to-a-gmsa"></a>Áthelyezés gMSA
a csoportosan felügyelt szolgáltatásfiókokat a legbiztonságosabb típusú szolgáltatásfiók a helyszíni igényekhez. Ha átléphet az egyikre, a következőt kell tennie:. Emellett érdemes lehet áthelyezni a szolgáltatásokat az Azure-ba és a szolgáltatási fiókjait az Azure Active Directoryhoz.

1.  Győződjön meg arról, hogy a [KDS legfelső szintű kulcsa van telepítve az erdőben](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key). Ezt a műveletet egyszer kell elvégezni.

2. [Hozzon létre egy új gMSA](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

3. Telepítse az új gMSA a szolgáltatást futtató összes gazdagépre.
   > [!NOTE] 
   > A gMSA gazdagépen való létrehozásával és telepítésével kapcsolatos további információkért, mielőtt konfigurálja a szolgáltatást a gMSA használatára, tekintse meg a következőt: [első lépések csoportosan felügyelt szolgáltatásfiókok](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)) használatával.

 
4. Módosítsa a szolgáltatás identitását gMSA, és adja meg az üres jelszót.

5. Ellenőrizze, hogy a szolgáltatás az új gMSA-identitás alatt működik-e.

6. Törölje a régi szolgáltatásfiók-identitást.

 

## <a name="next-steps"></a>Következő lépések
A szolgáltatásfiókok biztonságossá tételéről a következő cikkekben olvashat.

* [A helyszíni szolgáltatásfiókok bemutatása](service-accounts-on-premises.md)

* [Biztonságos csoportosan felügyelt szolgáltatásfiókok](service-accounts-group-managed.md)

* [Önálló felügyelt szolgáltatásfiókok biztonságossá tétele](service-accounts-standalone-managed.md)

* [Számítógép-fiókok biztonságossá tétele](service-accounts-computer.md)

* [Biztonságos felhasználói fiókok](service-accounts-user-on-premises.md)

* [Helyszíni szolgáltatásfiókok szabályozása](service-accounts-govern-on-premises.md)