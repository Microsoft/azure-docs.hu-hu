---
title: Számítógépfiókok biztonságossá tétele | Azure Active Directory
description: Útmutató a helyszíni számítógépfiókok biztonságossá tételéhez.
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
ms.openlocfilehash: 22faba20cb12ae755f19fe43c295d98f9b364cbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417510"
---
# <a name="securing-computer-accounts"></a>Számítógépfiókok biztonságossá tétele

A számítógépfiók vagy a LocalSystem fiók egy beépített, magas jogosultsági szintű fiók, amely a helyi számítógép gyakorlatilag minden erőforrásához hozzáférést biztosít. Ez a fiók nincs bejelentkezett felhasználói fiókhoz társítva. A LocalSystem hálózati erőforrásként futó szolgáltatások a számítógép távoli kiszolgálókra vonatkozó hitelesítő adatainak bemutatásával. A hitelesítő adatokat a <domain_name>$ formátumban jeleníti meg \<computer_name> . A számítógépfiók előre definiált neve: NT AUTHORITY\SYSTEM. A szolgáltatás elindításához és a szolgáltatás biztonsági környezetének biztosításához használható.

![[4. kép] (.\media\securing-service-accounts\secure-computer-accounts-image-1.png)](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>A számítógépfiók használatának előnyei

A számítógépfiók a következő előnyöket biztosítja.

* **Korlátlan helyi hozzáférés**: a számítógépfiók teljes hozzáférést biztosít a gép helyi erőforrásaihoz.

* **Automatikus jelszavas felügyelet**: a számítógépfiók eltávolítja a jelszavak manuális módosításának szükségességét. Ehelyett ez a fiók Active Directory tagja, és a fiók jelszava automatikusan módosul. Emellett nem kell regisztrálnia az egyszerű szolgáltatásnév nevét a szolgáltatáshoz.

* **Korlátozott hozzáférési jogok a gépen kívül**: a Active Directory Domain Services alapértelmezett Access Control listája (ACL) lehetővé teszi a számítógépfiókok minimális hozzáférését. Ha ezt a szolgáltatást feltörték, csak korlátozottan férhet hozzá a hálózaton lévő erőforrásokhoz.

## <a name="assess-security-posture-of-computer-accounts"></a>A számítógépfiókok biztonsági állapotának felmérése

A számítógép-fiókok használata során felmerülő lehetséges kihívások és a hozzájuk kapcsolódó enyhítések. 

| Problémák| Kezelési lehetőségek |
| - | - |
| A számítógépfiókok törlésre és kikapcsolódásra vannak kitéve, amikor a számítógép elhagyja és újracsatlakozik a tartományhoz.| Ellenőrizze, hogy szükség van-e a számítógép AD-csoportba való hozzáadására és annak ellenőrzésére, hogy melyik számítógépfiók lett hozzáadva egy csoporthoz az ezen a lapon megadott szkriptek használatával.| 
| Ha számítógép-fiókot ad hozzá egy csoporthoz, a számítógépen LocalSystemként futó összes szolgáltatás hozzáférési jogosultságokat kap a csoport számára.| Szelektívnek kell lennie a számítógépfiók csoporttagságok közül. Kerülje a számítógép-fiókok tagjainak bármely tartományi rendszergazdai csoportba való betöltését, mert a társított szolgáltatás teljes hozzáféréssel rendelkezik Active Directory Domain Serviceshoz. |
| Helytelen hálózati alapértelmezett beállítások a LocalSystem számára| Ne tegyük fel, hogy a számítógépfiók az alapértelmezett korlátozott hálózati erőforrásokhoz fér hozzá. Ehelyett körültekintően tekintse meg a csoport tagságát. |
| A LocalSystem-ként futó ismeretlen szolgáltatások| Győződjön meg arról, hogy a LocalSystem fiókban futó összes szolgáltatás a Microsoft szolgáltatásai vagy a harmadik féltől származó megbízható szolgáltatások. |


## <a name="find-services-running-under-the-computer-account"></a>A számítógépfiók alatt futó szolgáltatások keresése

Használja a következő PowerShell-parancsmagot a LocalSystem környezetben futó szolgáltatások kereséséhez

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**Egy adott csoport tagjaihoz tartozó számítógépek fiókjainak megkeresése**

A következő PowerShell-parancsmaggal megkeresheti azokat a számítógép-fiókokat, amelyek egy adott csoport tagjai.

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**Kiemelt csoportok tagjaiként működő számítógépek keresése**

A következő PowerShell-parancsmaggal megkeresheti az Identity Administrator groups (tartományi rendszergazdák, vállalati rendszergazdák, rendszergazdák) tagjaként használt számítógépfiókokat.

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>Áthelyezés a számítógép fiókjaiból

> [!IMPORTANT]
> A számítógépfiókok magas jogosultsági szintű fiókok, és csak akkor használhatók, ha a szolgáltatás korlátlan hozzáférést igényel a helyi erőforrásokhoz a gépen, és nem használhat felügyelt szolgáltatásfiókot (MSA).

* Kérdezze meg a szolgáltatás tulajdonosát, hogy a szolgáltatás futtatható-e egy MSA használatával, és használhat-e egy csoportosan felügyelt szolgáltatásfiókot (gMSA) vagy egy önálló felügyelt szolgáltatásfiókot (önállóan felügyelt szolgáltatásfiókot), ha a szolgáltatás támogatja azt.

* Olyan tartományi felhasználói fiókot használjon, amely csak a szolgáltatás futtatásához szükséges jogosultságokat használja.

## <a name="next-steps"></a>Következő lépések 

A szolgáltatásfiókok biztonságossá tételéről a következő cikkekben olvashat.

* [A helyszíni szolgáltatásfiókok bemutatása](service-accounts-on-premises.md)

* [Biztonságos csoportosan felügyelt szolgáltatásfiókok](service-accounts-group-managed.md)

* [Önálló felügyelt szolgáltatásfiókok biztonságossá tétele](service-accounts-standalone-managed.md)

* [Számítógép-fiókok biztonságossá tétele](service-accounts-computer.md)

* [Biztonságos felhasználói fiókok](service-accounts-user-on-premises.md)

* [Helyszíni szolgáltatásfiókok szabályozása](service-accounts-govern-on-premises.md)

 

 
