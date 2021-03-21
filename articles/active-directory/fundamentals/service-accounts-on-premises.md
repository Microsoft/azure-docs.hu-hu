---
title: A Active Directory Service-fiókok bemutatása | Azure Active Directory
description: Bevezetés a Active Directoryban található szolgáltatásfiókok típusaiba és azok biztonságossá tételéhez.
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
ms.openlocfilehash: a9024bc9fbd460f403db2da8a65af1e9bd2e771b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645616"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Active Directory szolgáltatásfiókok bemutatása

A szolgáltatás olyan elsődleges biztonsági identitással rendelkezik, amely meghatározza a helyi és hálózati erőforrások hozzáférési jogosultságait. A Microsoft Win32 szolgáltatás biztonsági környezetét a szolgáltatás elindításához használt szolgáltatásfiók határozza meg. A szolgáltatási fiók a következőket használja:
* szolgáltatás azonosítása és hitelesítése
* a szolgáltatás indítása sikeresen megtörtént
* kód vagy alkalmazás elérése vagy végrehajtása
* folyamat elindítása. 

## <a name="types-of-on-premises-service-accounts"></a>Helyszíni szolgáltatásfiókok típusai

A használati eset alapján felügyelt szolgáltatásfiókot (MSA), számítógépfiókot vagy felhasználói fiókot használhat a szolgáltatás futtatásához. A szolgáltatásokat tesztelni kell annak megerősítéséhez, hogy használhatók-e felügyelt szolgáltatásfiókok. Ha tudja, használjon egyet.

### <a name="group-msa-accounts"></a>MSA-fiókok csoportosítása

A helyszíni környezetben futó szolgáltatások esetében lehetőség van [csoportosan felügyelt szolgáltatásfiókok](service-accounts-group-managed.md) (csoportosan felügyelt szolgáltatásfiókokat) használatára. a csoportosan felügyelt szolgáltatásfiókokat egyetlen identitási megoldást biztosítanak egy kiszolgálófarm vagy egy hálózati terheléselosztó mögött futó szolgáltatáshoz. Emellett egyetlen kiszolgálón futó szolgáltatáshoz is használhatók. [a csoportosan felügyelt szolgáltatásfiókokat meghatározott követelményeknek kell teljesülniük](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)

### <a name="standalone-msa-accounts"></a>Önálló MSA-fiókok

Ha nem tud gMSA használni, használjon [önálló felügyelt szolgáltatásfiókot](service-accounts-standalone-managed.md)(önállóan felügyelt szolgáltatásfiókot). a sMSAs-nek legalább a Windows Server 2008R2 kell lennie. A csoportosan felügyelt szolgáltatásfiókokat-től eltérően a sMSAs csak egyetlen kiszolgálón fut. Ezek a kiszolgálók több szolgáltatására is felhasználhatók.

### <a name="computer-account"></a>Számítógépfiók

Ha nem tud MSA használni, vizsgálja meg a [számítógépfiókok](service-accounts-computer.md)használatát. A LocalSystem fiók egy előre definiált helyi fiók, amely kiterjedt jogosultságokkal rendelkezik a helyi számítógépen, és számítógép-identitásként működik a hálózaton.   
A LocalSystem fiókhoz hozzáférő szolgáltatások hálózati erőforrásként való használata a számítógépfiók hitelesítő adataival <domain_name>formátumban \<computer_name> .

Az NT AUTHORITY\SYSTEM a LocalSystem fiók előre definiált neve. A szolgáltatás elindításához és a szolgáltatás biztonsági környezetének megadásához használható.

> [!NOTE]
> Számítógépfiók használata esetén nem állapítható meg, hogy a számítógépen melyik szolgáltatás használja ezt a fiókot, ezért nem tudja naplózni, hogy melyik szolgáltatás hajtja végre a módosításokat. 

### <a name="user-account"></a>Felhasználói fiók

Ha nem tud MSA használni, vizsgálja meg a [felhasználói fiókok](service-accounts-user-on-premises.md)használatát. A felhasználói fiókok lehetnek tartományi felhasználói fiókok vagy helyi felhasználói fiókok.

A tartományi felhasználói fiókok lehetővé teszik, hogy a szolgáltatás teljes mértékben kihasználhassa a Windows és a Microsoft Active Directory Domain Services szolgáltatás biztonsági funkcióit. A szolgáltatás helyi és hálózati hozzáférést kap a fiókhoz. Emellett minden olyan csoport engedélyével rendelkezik, amelynek tagja a fiók. A tartományi szolgáltatási fiókok támogatják a Kerberos kölcsönös hitelesítését.

Helyi felhasználói fiók (formátumnév: ".\UserName") csak a gazdaszámítógép SAM-adatbázisában létezik; Active Directory Domain Services nem rendelkezik felhasználói objektummal. A tartomány nem tudja hitelesíteni a helyi fiókot. Így a helyi felhasználói fiók biztonsági környezetében futó szolgáltatások nem férnek hozzá a hálózati erőforrásokhoz (kivéve, ha névtelen felhasználó). A helyi felhasználói környezetben futó szolgáltatások nem támogatják a Kerberos kölcsönös hitelesítését, amelyben a szolgáltatást az ügyfeleik hitelesítik. Ezen okok miatt a helyi felhasználói fiókok általában nem megfelelőek a címtár-kompatibilis szolgáltatásokhoz.

> [!IMPORTANT]
> A szolgáltatásfiókok nem lehetnek olyan Kiemelt csoportok tagjai, akik biztonsági kockázatot jelenthetnek az emelt szintű csoporttagság esetén. Minden szolgáltatásnak saját szolgáltatásfiókot kell használnia naplózási és biztonsági célokra.

## <a name="choose-the-right-type-of-service-account"></a>Válassza ki a megfelelő típusú szolgáltatásfiókot


| Feltételek| gMSA| Önállóan felügyelt szolgáltatásfiókot| Számítógépfiók| Felhasználói fiók |
| - | - | - | - | - |
| Az alkalmazás egyetlen kiszolgálón fut| Igen| Igen. GMSA használata, ha lehetséges| Igen. MSA használata, ha lehetséges| Igen. Ha lehetséges, használja a MSA-t. |
| Az alkalmazás több kiszolgálón fut| Igen| Nem| Nem. A fiók a kiszolgálóhoz van kötve| Igen. Ha lehetséges, használja a MSA-t. |
| Az alkalmazás a terheléselosztó mögött fut| Igen| Nem| Nem| Igen. Csak akkor használja, ha nem tud gMSA használni |
| Az alkalmazás Windows Server 2008 R2 rendszeren fut| Nem| Igen| Igen. Ha lehetséges, használja a MSA-t.| Igen. Ha lehetséges, használja a MSA-t. |
| Futtatás Windows Server 2012 rendszeren| Igen| Igen. GMSA használata, ha lehetséges| Igen. MSA használata, ha lehetséges| Igen. Ha lehetséges, használja a MSA-t. |
| A szolgáltatásfiók egyetlen kiszolgálóra való korlátozásának követelménye| Nem| Igen| Igen. Önállóan felügyelt szolgáltatásfiókot használata, ha lehetséges| Nem. |


 

### <a name="use-server-logs-and-powershell-to-investigate"></a>A kiszolgálói naplók és a PowerShell használata a vizsgálathoz

A kiszolgálói naplók segítségével meghatározhatja, hogy mely kiszolgálókon és hány kiszolgálón futnak az alkalmazások.

A következő PowerShell-parancs futtatásával lekérheti a Windows Server verziójának listáját a hálózaton lévő összes kiszolgálóról. 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>Helyszíni szolgáltatásfiókok keresése

Javasoljuk, hogy adjon hozzá egy előtagot, például: "SVC". A szolgáltatás fiókjaiként használt összes fiókhoz. Ez az elnevezési konvenció megkönnyíti majd a keresését és kezelését. Vegye figyelembe a szolgáltatás fiókjának és a szolgáltatásfiók tulajdonosának Leírás attribútumát is, ez lehet a csapat aliasa vagy a biztonsági csapat tulajdonosa.

A helyszíni szolgáltatásfiókok megkeresése kulcsfontosságú a biztonságuk biztosításához. Továbbá nehéz lehet a nem MSA fiókok esetében is. Javasoljuk, hogy tekintse át az összes olyan fiókot, amely hozzáféréssel rendelkezik a fontos helyszíni erőforrásokhoz, és határozza meg, hogy mely számítógép-vagy felhasználói fiókok működhetnek szolgáltatásfiókokként. Az alábbi módszerekkel is megkeresheti a fiókokat.

* Az egyes fiókokhoz tartozó cikkek részletesen ismertetik a fióktípus megtalálásának lépéseit. A cikkekre mutató hivatkozásokat ebben a cikkben, a következő lépések című szakaszban találja.

## <a name="document-service-accounts"></a>Document Service-fiókok

Miután megtalálta a szolgáltatási fiókokat a helyszíni környezetben, dokumentálja a következő információkat az egyes fiókokról. 

* A tulajdonos. A fiók fenntartásáért felelős személy.

* A cél. Az alkalmazás, amelyet a fiók képvisel vagy más célra. 

* Jogosultsági hatókörök. Milyen engedélyekkel rendelkezik? Mi a teendő, ha bármelyik csoport tagja?

* Kockázati profil. Mi a kockázata a vállalatnak, ha a fiók biztonsága sérül? Magas kockázat esetén használjon MSA.

* Várható élettartam és rendszeres igazolás. Meddig várható a fiók működése? Milyen gyakran kell ellenőrizni a tulajdonost, és igazolnia kell a folyamatos igényeket?

* Jelszavas biztonság. Felhasználói és helyi számítógépfiókok esetén, ahol a rendszer a jelszót tárolja. Győződjön meg arról, hogy a jelszavak biztonságban vannak, és hogy ki férhet hozzá. Vegye fontolóra [Privileged Identity Management](../privileged-identity-management/pim-configure.md) használatát a tárolt jelszavak védelméhez. 

  

## <a name="next-steps"></a>Következő lépések

A szolgáltatásfiókok biztonságossá tételéről a következő cikkekben olvashat.

* [A helyszíni szolgáltatásfiókok bemutatása](service-accounts-on-premises.md)

* [Biztonságos csoportosan felügyelt szolgáltatásfiókok](service-accounts-group-managed.md)

* [Önálló felügyelt szolgáltatásfiókok biztonságossá tétele](service-accounts-standalone-managed.md)

* [Számítógép-fiókok biztonságossá tétele](service-accounts-computer.md)

* [Biztonságos felhasználói fiókok](service-accounts-user-on-premises.md)

* [Helyszíni szolgáltatásfiókok szabályozása](service-accounts-govern-on-premises.md)

