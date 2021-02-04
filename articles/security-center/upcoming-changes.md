---
title: Fontos változások a Azure Security Center
description: Előfordulhat, hogy a Azure Security Center közelgő módosításaival tisztában kell lennie a szolgáltatással, és amelyre szükség lehet a tervezéshez
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: a2c29049decc056f0d3c8083d21574456610c124
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555137"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>A Azure Security Center fontos jövőbeli módosításai

> [!IMPORTANT]
> Az ezen az oldalon található információk a kiadás előtti termékekhez vagy szolgáltatásokhoz kapcsolódnak, amelyek a kereskedelmi forgalomba hozatal előtt jelentős mértékben módosíthatók, ha még nem. A Microsoft nem vállal semmilyen kifejezett vagy vélelmezett szavatosságot az itt megadott információk tekintetében.

Ezen az oldalon megismerheti a Security Center tervezett módosításokat. Ismerteti a termék tervezett módosításait, amelyek hatással lehetnek a biztonságos pontszámra vagy munkafolyamatokra.

Ha a legújabb kibocsátási megjegyzéseket keresi, a [Azure Security Center újdonságai](release-notes.md)között találhatja meg őket.


## <a name="planned-changes"></a>Tervezett változások

- [A System Updates (rendszerfrissítések alkalmazása) biztonsági ellenőrzésének két javaslata elavult](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Az SQL-adatbesorolásra vonatkozó javaslat továbbfejlesztése](#enhancements-to-sql-data-classification-recommendation)
- [11 Azure Defender-riasztás elavult](#deprecation-of-11-azure-defender-alerts)

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>A System Updates (rendszerfrissítések alkalmazása) biztonsági ellenőrzésének két javaslata elavult 

**Változás becsült dátuma:** Február 2021

A következő két javaslatot ütemezjük a 2021 februárjában:

- A számítógépeket újra kell **indítani a rendszerfrissítések alkalmazásához**. Ez enyhe hatással lehet a biztonságos pontszámra.
- A **figyelési ügynököt telepíteni kell a gépekre**. Ez a javaslat csak a helyszíni gépekre vonatkozik, és a logikája egy másik javaslatba kerül át, **log Analytics az ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken**. Ez enyhe hatással lehet a biztonságos pontszámra.

Javasoljuk, hogy ellenőrizze a folyamatos exportálási és munkafolyamat-automatizálási konfigurációkat, és ellenőrizze, hogy ezek a javaslatok szerepelnek-e bennük. Emellett az azokat használó irányítópultokat vagy egyéb figyelési eszközöket ennek megfelelően kell frissíteni.

További információ ezekről a javaslatokról a [biztonsági javaslatok hivatkozását ismertető oldalon](recommendations-reference.md)található.


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Az SQL-adatbesorolásra vonatkozó javaslat továbbfejlesztése

**Változás becsült dátuma:** Q2 2021

Az **SQL-adatbázisokban található bizalmas adatokra** **vonatkozó ajánlást a Microsoft** adatbesorolási stratégiájával jobban illeszkedő új verzióra cseréli a rendszer. Ennek eredményeképpen a javaslat azonosítója is módosul (jelenleg b0df6f56-862d-4730-8597-38c0fd4ebd59).


### <a name="deprecation-of-11-azure-defender-alerts"></a>11 Azure Defender-riasztás elavult

**Változás becsült dátuma:** Március 2021

A következő hónapban az alább felsorolt tizenegy Azure Defender-riasztás elavulttá válik.

- Az új riasztások felülírják ezt a két riasztást, és jobb lefedettséget biztosítanak:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | ELŐZETES verzió – a "Get-AzureDomainInfo" függvény futtatása |
    | ARM_MicroBurstRunbook    | ELŐZETES verzió – a "Get-AzurePasswords" függvény futtatása  |
    |                          |                                                                          |

- Ez a kilenc riasztás olyan Azure Active Directory Identity Protection összekötőhöz kapcsolódik, amely már elavult:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Szokatlan bejelentkezési tulajdonságok |
    | AnonymousLogin      | Névtelen IP-cím          |
    | InfectedDeviceLogin | Kártevők társított IP-címe     |
    | ImpossibleTravel    | Szokatlan utazás               |
    | MaliciousIP         | Kártékony IP-cím          |
    | LeakedCredentials   | Kiszivárgott hitelesítő adatok            |
    | PasswordSpray       | Jelszó spray                |
    | LeakedCredentials   | Azure AD-fenyegetések felderítése  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 



## <a name="next-steps"></a>Következő lépések

A termék legutóbbi módosításaival kapcsolatban tekintse [meg az Újdonságok a Azure Security Center?](release-notes.md)című témakört.
