---
title: Fontos változások a Azure Security Center
description: Előfordulhat, hogy a Azure Security Center közelgő módosításaival tisztában kell lennie a szolgáltatással, és amelyre szükség lehet a tervezéshez
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/18/2021
ms.author: memildin
ms.openlocfilehash: b9a93286b6a546160b6c621d084437f671eab4d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773572"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>A Azure Security Center fontos jövőbeli módosításai

> [!IMPORTANT]
> Az ezen az oldalon található információk a kiadás előtti termékekhez vagy szolgáltatásokhoz kapcsolódnak, amelyek a kereskedelmi forgalomba hozatal előtt jelentős mértékben módosíthatók, ha még nem. A Microsoft nem vállal semmilyen kifejezett vagy vélelmezett szavatosságot az itt megadott információk tekintetében.

Ezen az oldalon megismerheti a Security Center tervezett módosításokat. Ismerteti a termék tervezett módosításait, amelyek hatással lehetnek a biztonságos pontszámra vagy munkafolyamatokra.

Ha a legújabb kibocsátási megjegyzéseket keresi, a [Azure Security Center újdonságai](release-notes.md)között találhatja meg őket.


## <a name="planned-changes"></a>Tervezett változások

| Tervezett módosítás                                                                                                                                                        | Változás becsült dátuma |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [A System Updates (rendszerfrissítések alkalmazása) biztonsági ellenőrzésének két javaslata elavult](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | Március 2021                |
| [11 Azure Defender-riasztás elavult](#deprecation-of-11-azure-defender-alerts)                                                                                   | Március 2021                |
| [21 javaslat a biztonsági vezérlők között](#21-recommendations-moving-between-security-controls)                                                           | Április 2021                |
| [A System Updates (rendszerfrissítések alkalmazása) biztonsági ellenőrzésének két további javaslata elavult](#two-further-recommendations-from-apply-system-updates-security-control-being-deprecated)                                                                                         | Április 2021                |
| [Az AWS javaslatai az általánosan elérhetővé tételhez lesznek közzétéve](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | Április 2021                |
| [Az SQL-adatbesorolásra vonatkozó javaslat továbbfejlesztése](#enhancements-to-sql-data-classification-recommendation)                                                     | Q2 2021                   |
|                                                                                                                                                                       |                           |


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>A System Updates (rendszerfrissítések alkalmazása) biztonsági ellenőrzésének két javaslata elavult 

**Változás becsült dátuma:** Március 2021

A következő két javaslatot ütemezjük a 2021 februárjában:

- A számítógépeket újra kell **indítani a rendszerfrissítések alkalmazásához**. Ez enyhe hatással lehet a biztonságos pontszámra.
- A **figyelési ügynököt telepíteni kell a gépekre**. Ez a javaslat csak a helyszíni gépekre vonatkozik, és a logikája egy másik javaslatba kerül át, **log Analytics az ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken**. Ez enyhe hatással lehet a biztonságos pontszámra.

Javasoljuk, hogy ellenőrizze a folyamatos exportálási és munkafolyamat-automatizálási konfigurációkat, és ellenőrizze, hogy ezek a javaslatok szerepelnek-e bennük. Emellett az azokat használó irányítópultokat vagy egyéb figyelési eszközöket ennek megfelelően kell frissíteni.

További információ ezekről a javaslatokról a [biztonsági javaslatok hivatkozását ismertető oldalon](recommendations-reference.md)található.

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
 




### <a name="21-recommendations-moving-between-security-controls"></a>21 javaslat a biztonsági vezérlők között 

**Változás becsült dátuma:** Április 2021

A következő javaslatok átkerülnek egy másik biztonsági vezérlőbe. A biztonsági vezérlők a kapcsolódó biztonsági javaslatok logikai csoportjai, és a sebezhető támadási felületeket tükrözik. Ez a lépés biztosítja, hogy ezek a javaslatok a legmegfelelőbb módon legyenek kielégítve a célnak. 

Ismerje meg, hogy mely javaslatok szerepelnek a biztonsági ellenőrzésekben és javaslataikban.

|Ajánlás |Változás és hatás  |
|---------|---------|
|A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon<br>A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.<br>Az SQL-adatbázisokra vonatkozó biztonsági réseket új megoldásokkal kell szervizelni<br>A virtuális gépeken található SQL-adatbázisokra vonatkozó biztonsági réseket szervizelni kell     |Váltás a sebezhetőségek javításával (6 pontot ér)<br>a biztonsági konfigurációk szervizelése (4. érték).<br>A környezettől függően ezek a javaslatok kevesebb hatással lesznek a pontszámra.|
|Az előfizetéshez egynél több tulajdonos rendelhető hozzá<br>Az Automation-fiók változóit titkosítani kell<br>IoT-eszközök – a naplózási folyamat leállt az események küldésével<br>IoT-eszközök – az operációs rendszer alapkonfigurációjának érvényesítési hibája<br>IoT-eszközök – TLS titkosítási csomag frissítése szükséges<br>IoT-eszközök – portok megnyitása az eszközön<br>IoT-eszközök – a rendszer az egyik láncban megtalálta a megfelelő tűzfal-házirendet<br>IoT-eszközök – a bemeneti láncban található megengedő tűzfalszabály<br>IoT-eszközök – a kimeneti láncban található megengedő tűzfalszabály<br>A IoT Hub lévő diagnosztikai naplókat engedélyezni kell<br>IoT-eszközök – a kihasználatlan üzeneteket küldő ügynök<br>IoT-eszközök – az alapértelmezett IP-szűrési házirendet meg kell tagadni<br>IoT-eszközök – IP-szűrési szabály nagyméretű IP-címtartomány<br>IoT-eszközök – az ügynök üzenetének intervallumai és mérete módosítva<br>IoT-eszközök – azonos hitelesítési hitelesítő adatok<br>IoT-eszközök – a naplózási folyamat leállt az események küldésével<br>IoT-eszközök – az operációs rendszer (OS) alapkonfigurációjának konfigurálását rögzíteni kell|Áttérés az **ajánlott biztonsági eljárások megvalósítására**.<br>Ha egy javaslat az ajánlott biztonsági eljárások megvalósítása, amely nem ér el pontokat, a javaslat már nem érinti a biztonságos pontszámot.|
|||


### <a name="two-further-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>A System Updates (rendszerfrissítések alkalmazása) biztonsági ellenőrzésének két további javaslata elavult

**Változás becsült dátuma:** Április 2021

A következő két javaslat elavult:

- Az **operációsrendszer-verziót frissíteni kell a Cloud Service-szerepkörökhöz** – alapértelmezés szerint az Azure rendszeresen frissíti a vendég operációs rendszert a szolgáltatás-konfigurációban (. cscfg) megadott operációsrendszer-családban található legújabb támogatott lemezképre, például a Windows Server 2016-es verzióra.
- A **Kubernetes-szolgáltatásokat nem sebezhető Kubernetes-verzióra kell frissíteni** – ez a javaslat értékelések nem olyan széles körűek, mint a számukra. Az ajánlás aktuális verzióját a rendszer végül egy továbbfejlesztett verzióra cseréli le, amely jobban igazodik az ügyfél biztonsági igényeihez.


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Az AWS javaslatai az általánosan elérhetővé tételhez lesznek közzétéve

**Változás becsült dátuma:** Április 2021

Azure Security Center védi a munkaterheléseket az Azure-ban, a Amazon Web Services (AWS) és a Google Cloud Platformban (GCP).

Az AWS biztonsági hubhoz érkező javaslatok előzetes verzióban érhetők el, mivel a felhőalapú összekötők bevezetése megtörtént. Az **előzetesként** megjelölt javaslatok nem szerepelnek a biztonságos pontszám számításaiban, de ha lehetséges, továbbra is szervizelni kell, hogy ha az előzetes verzió időtartama lejár, a pontszámhoz is hozzájárul.

Ezzel a módosítással az AWS-javaslatok két halmaza kerül át a GA-ra:

- [A Security hub PCI DSS vezérlői](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Biztonsági központ CIS AWS-alapjai – teljesítményteszt-vezérlők](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Ha ezek a GA és a felmérések az AWS-erőforrásokon futnak, az eredmények az összes multi-és hibrid Felhőbeli erőforrás együttes biztonsági pontszámát is befolyásolják. 



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Az SQL-adatbesorolásra vonatkozó javaslat továbbfejlesztése

**Változás becsült dátuma:** Q2 2021

Az **SQL-adatbázisokban található bizalmas adatokra** **vonatkozó ajánlást a Microsoft** adatbesorolási stratégiájával jobban illeszkedő új verzióra cseréli a rendszer. Ennek eredményeképpen a javaslat azonosítója is módosul (jelenleg b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Következő lépések

A termék legutóbbi módosításaival kapcsolatban tekintse [meg az Újdonságok a Azure Security Center?](release-notes.md)című témakört.