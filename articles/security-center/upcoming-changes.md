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
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 6bec9f0a1c22691d818566cec3f59c1ec0f3d3bb
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051616"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>A Azure Security Center fontos jövőbeli módosításai

> [!IMPORTANT]
> Az ezen az oldalon található információk a kiadás előtti termékekhez vagy szolgáltatásokhoz kapcsolódnak, amelyek a kereskedelmi forgalomba hozatal előtt jelentős mértékben módosíthatók, ha még nem. A Microsoft nem vállal semmilyen kifejezett vagy vélelmezett szavatosságot az itt megadott információk tekintetében.

Ezen az oldalon megismerheti a Security Center tervezett módosításokat. Ismerteti a termék tervezett módosításait, amelyek hatással lehetnek a biztonságos pontszámra vagy munkafolyamatokra.

Ha a legújabb kibocsátási megjegyzéseket keresi, a [Azure Security Center újdonságai](release-notes.md)között találhatja meg őket.


## <a name="planned-changes"></a>Tervezett változások

- [Az AWS javaslatai az általánosan elérhetővé tételhez lesznek közzétéve](#recommendations-from-aws-will-be-released-for-general-availability-ga)
- [Két örökölt javaslat nem fog közvetlenül az Azure-beli tevékenység naplóba írni.](#two-legacy-recommendations-will-no-longer-write-data-directly-to-azure-activity-log)
- [A System Updates (rendszerfrissítések alkalmazása) biztonsági ellenőrzésének két javaslata elavult](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Az SQL-adatbesorolásra vonatkozó javaslat továbbfejlesztése](#enhancements-to-sql-data-classification-recommendation)
- [11 Azure Defender-riasztás elavult](#deprecation-of-11-azure-defender-alerts)


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Az AWS javaslatai az általánosan elérhetővé tételhez lesznek közzétéve

**Változás becsült dátuma:** Április 2021

Azure Security Center védi a munkaterheléseket az Azure-ban, a Amazon Web Services (AWS) és a Google Cloud Platformban (GCP).

Az AWS biztonsági hubhoz érkező javaslatok előzetes verzióban érhetők el, mivel a felhőalapú összekötők bevezetése megtörtént. Az **előzetesként** megjelölt javaslatok nem szerepelnek a biztonságos pontszám számításaiban, de ha lehetséges, továbbra is szervizelni kell, hogy ha az előzetes verzió időtartama lejár, a pontszámhoz is hozzájárul.

Ezzel a módosítással az AWS-javaslatok két halmaza kerül át a GA-ra:

- [A Security hub PCI DSS vezérlői](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Biztonsági központ CIS AWS-alapjai – teljesítményteszt-vezérlők](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Ha ezek a GA és a felmérések az AWS-erőforrásokon futnak, az eredmények az összes multi-és hibrid Felhőbeli erőforrás együttes biztonsági pontszámát is befolyásolják. 



### <a name="two-legacy-recommendations-will-no-longer-write-data-directly-to-azure-activity-log"></a>Két örökölt javaslat nem fog közvetlenül az Azure-beli tevékenység naplóba írni. 

**Változás becsült dátuma:** Március 2021

A Security Center szinte minden biztonsági javaslathoz átadja az adatot, hogy Azure Advisor, amely viszont azt írja az [Azure-tevékenység naplójába](../azure-monitor/essentials/activity-log.md).

Két javaslat esetében az adatgyűjtés egyidejűleg közvetlenül az Azure-tevékenység naplójában történik. Ezzel a módosítással a Security Center leállítja a korábbi biztonsági javaslatok adatírását közvetlenül a tevékenységi naplóba. Ehelyett exportáljuk az adatAzure Advisor, ahogy az összes többi javaslat esetében. 

A két örökölt javaslat a következők:
- Az Endpoint Protection állapotával kapcsolatos problémákat fel kell oldani a gépeken
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell

Ha a következő két javaslathoz fér hozzá a tevékenység naplójának "TaskDiscovery típusú javaslat" kategóriájában, akkor ez a továbbiakban nem lesz elérhető.

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>A System Updates (rendszerfrissítések alkalmazása) biztonsági ellenőrzésének két javaslata elavult 

**Változás becsült dátuma:** Március 2021

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
