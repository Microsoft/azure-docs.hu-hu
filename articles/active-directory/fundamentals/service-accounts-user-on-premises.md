---
title: A felhasználó-alapú szolgáltatásfiókok biztonságossá tétele | Azure Active Directory
description: Útmutató a helyszíni felhasználói fiókok biztonságossá tételéhez.
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
ms.openlocfilehash: e484bdda33142024f2067649eaa67042fe7776f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417469"
---
# <a name="securing-user-based-service-accounts-in-active-directory"></a>A felhasználó-alapú szolgáltatásfiókok biztonságossá tétele a Active Directoryban

A helyszíni felhasználói fiókok a hagyományos módszer a Windows rendszeren futó szolgáltatások biztonságossá tételéhez. Ezeket a fiókokat legutóbb akkor használhatja, ha a szolgáltatás nem támogatja a globálisan felügyelt szolgáltatásfiókok (csoportosan felügyelt szolgáltatásfiókokat) és a különálló felügyelt szolgáltatásfiókok (sMSAs) használatát. A használni kívánt fiók legjobb típusának kiválasztásával kapcsolatos információkért tekintse meg a helyszíni szolgáltatásfiókok áttekintése című témakört. Azt is vizsgálja meg, hogy áthelyezheti-e a szolgáltatást egy olyan Azure-szolgáltatásfiók használatára, mint a felügyelt identitás vagy a szolgáltatás elve. 

A helyszíni felhasználói fiókok létrehozhatók a szolgáltatások biztonsági környezetének biztosításához, és a szolgáltatások számára a helyi és hálózati erőforrások eléréséhez szükséges jogosultságokat biztosítanak. A jelszó manuális felügyeletét igénylik, hasonlóan a többi Active Directory (AD) felhasználói fiókhoz. A fiókok biztonságának megőrzése érdekében a szolgáltatás-és tartományi rendszergazdáknak be kell tartaniuk az erős jelszóházirend-folyamatokat.

Ha felhasználói fiókot használ szolgáltatásfiókként, csak egy szolgáltatáshoz használja. Nevezze el úgy, hogy egyértelművé teszi, hogy ez egy szolgáltatásfiók, és melyik szolgáltatáshoz. 

## <a name="benefits-and-challenges"></a>Előnyök és kihívások

Előnyök

A helyszíni felhasználói fiókok a legsokoldalúbb fióktípus a szolgáltatásokkal való használatra. A szolgáltatásfiókokként használt felhasználói fiókokat a normál felhasználói fiókokra vonatkozó szabályzatok szabályozzák. Ezt csak akkor használja, ha nem tud MSA használni. Azt is kiértékeli, hogy a számítógépfiók jobb megoldás-e. 

A helyszíni felhasználói fiókokkal kapcsolatos kihívások

A következő kihívások a helyszíni felhasználói fiókok használatával vannak társítva.

| Problémák| Kezelési lehetőségek |
| - | - |
| A jelszavas felügyelet olyan manuális folyamat, amely gyengébb biztonsági és szolgáltatási állásidőt eredményezhet.| Győződjön meg arról, hogy a jelszó bonyolultságát és a jelszó módosítását egy robusztus folyamat szabályozza, amely erős jelszóval biztosítja a rendszeres frissítéseket. <br> A jelszó-módosítás koordinálása a szolgáltatás jelszavas frissítésével, ami a szolgáltatás leállását eredményezi. |
| A szolgáltatásfiók-ként működő helyszíni felhasználói fiókok azonosítása nehéz lehet.| Dokumentálja és karbantartja a környezetben üzembe helyezett szolgáltatásfiókok rekordjait. <br> Kövesse nyomon a fiók nevét és azon erőforrásokat, amelyekhez hozzá van rendelve. <br> Vegye fontolóra svc_ előtagjának hozzáadását a szolgáltatásfiókokként használt összes felhasználói fiókhoz. |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>Szolgáltatásfiókokként használt helyszíni felhasználói fiókok keresése

A helyszíni felhasználói fiókok ugyanúgy, mint bármely más AD-felhasználói fiókhoz tartoznak. Ezért nehéz lehet megkeresni ezeket a fiókokat, mert nincs egyetlen attribútuma egy olyan felhasználói fióknak, amely szolgáltatásként azonosítja azt. 

Javasoljuk, hogy hozzon létre egy könnyen azonosítható elnevezési konvenciót minden olyan felhasználói fiókhoz, amelyet szolgáltatásfiók használ.

Adja meg például a "Service-" előtagot, és nevezze el a következő szolgáltatást: "Service-HRDataConnector".

Az alábbi mutatók némelyikével megkeresheti ezeket a szolgáltatási fiókokat, azonban előfordulhat, hogy az összes ilyen fiók nem található.

* Delegálásra megbízható fiókok.

* Egyszerű szolgáltatásnév nevű fiókok.

* Azok a fiókok, amelyek jelszavának beállítása soha nem jár le.

A következő PowerShell-parancsok futtatásával megkeresheti a szolgáltatásokhoz létrehozott helyszíni felhasználói fiókokat.

### <a name="find-accounts-trusted-for-delegation"></a>Delegálásra megbízható fiókok keresése

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

### <a name="find-accounts-with-service-principle-names"></a>A szolgáltatási elv nevét tartalmazó fiókok keresése

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

 

### <a name="find-accounts-with-passwords-set-to-never-expire"></a>Olyan fiókok keresése, amelyekben a jelszó beállítása soha nem jár le

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```


A bizalmas erőforrásokhoz való hozzáférést is naplózhatja, és archiválhatja a naplókat a biztonsági információk és az Event Management (SIEM) rendszerbe. Az Azure Log Analytics vagy az Azure Sentinel használatával megkeresheti és elemezheti a fiókokat, illetve elvégezheti a szolgáltatások használatát.

## <a name="assess-security-of-on-premises-user-accounts"></a>Helyszíni felhasználói fiókok biztonságának felmérése

A következő feltételekkel állapíthatja meg, hogy a szolgáltatás fiókjaiban milyen helyszíni felhasználói fiókokat használ a rendszer:

* Mi a jelszó-kezelési házirend?

* A fiók tagja valamelyik Kiemelt csoportnak?

* A fiók rendelkezik írási/olvasási hozzáféréssel a fontos erőforrásokhoz?

### <a name="mitigate-potential-security-issues"></a>A potenciális biztonsági problémák enyhítése

A következő táblázat az esetleges biztonsági problémákat és a helyszíni felhasználói fiókok megfelelő megoldásait mutatja be.

| Biztonsági problémák| Kezelési lehetőségek |
| - | - |
| Jelszókezelés|* Gondoskodjon arról, hogy a jelszó bonyolultsága és a jelszó módosítása olyan robusztus folyamattal legyen szabályozva, amely az erős jelszóval kapcsolatos követelményekkel biztosítja a rendszeres frissítéseket. <br> * A szolgáltatás leállásának minimalizálásához jelszó-módosítással koordinálja a jelszót. |
| A fiók a Kiemelt csoportok tagja.| Csoporttagság áttekintése. Távolítsa el a fiókot a Kiemelt csoportok közül. Csak a szolgáltatás futtatásához szükséges jogokat és engedélyeket adja meg a fiók számára (forduljon a szolgáltatás gyártójához). Előfordulhat például, hogy meg tudja tagadni a helyi bejelentkezést, vagy megtagadja az interaktív bejelentkezést. |
| A fiók olvasási/írási hozzáféréssel rendelkezik a bizalmas erőforrásokhoz.| Bizalmas erőforrásokhoz való hozzáférés naplózása. Archiválja a naplókat egy SIEM-re (Azure Log Analytics vagy Azure Sentinel) elemzésre. Erőforrás-engedélyek szervizelése, ha nemkívánatos hozzáférési szint észlelhető. |


## <a name="move-to-more-secure-account-types"></a>Áthelyezés a biztonságosabb fiókok típusaiba

A Microsoft nem javasolja, hogy az ügyfelek szolgáltatásfiókokként használják a helyi felhasználói fiókokat. Az ilyen típusú fiókot használó bármely szolgáltatás esetében mérje fel, hogy egy gMSA vagy önállóan felügyelt szolgáltatásfiókot használatára van-e beállítva.

Emellett kiértékelheti, hogy maga a szolgáltatás áthelyezhető-e az Azure-ba, hogy a biztonságosabb szolgáltatásfiók-típusok is használhatók legyenek. 

## <a name="next-steps"></a>Következő lépések
A szolgáltatásfiókok biztonságossá tételéről a következő cikkekben olvashat.

* [A helyszíni szolgáltatásfiókok bemutatása](service-accounts-on-premises.md)

* [Biztonságos csoportosan felügyelt szolgáltatásfiókok](service-accounts-group-managed.md)

* [Önálló felügyelt szolgáltatásfiókok biztonságossá tétele](service-accounts-standalone-managed.md)

* [Számítógép-fiókok biztonságossá tétele](service-accounts-computer.md)

* [Biztonságos felhasználói fiókok](service-accounts-user-on-premises.md)

* [Helyszíni szolgáltatásfiókok szabályozása](service-accounts-govern-on-premises.md)

 
