---
title: Fontos változások hamarosan Azure Security Center
description: A Azure Security Center változások, amelyekről esetleg tudnia kell, és amelyeket meg kell tervezni
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/20/2021
ms.author: memildin
ms.openlocfilehash: 3307d3aed422c3eab63412388244ef14ef3be699
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750992"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Fontos közelgő változások a Azure Security Center

> [!IMPORTANT]
> Az ezen az oldalon található információk a kiadás előtti termékekre vagy funkciókra vonatkoznak, amelyek jelentős mértékben módosíthatók a kereskedelmi kiadásuk előtt, ha bármikor. A Microsoft nem vállal semmilyen kifejezett vagy hallgatólagos kötelezettségvállalást vagy garanciát az itt megadott információkra.

Ezen az oldalon megismeri a tervezett módosításokat a Security Center. Ismerteti a termék tervezett módosításait, amelyek hatással lehetnek például a biztonsági pontszámra vagy a munkafolyamatra.

Ha a legújabb kibocsátási megjegyzéseket keresi, az Újdonságok a [Azure Security Center.](release-notes.md)


## <a name="planned-changes"></a>Tervezett módosítások

| Tervezett módosítás                                                                                                                                                        | A változás becsült dátuma |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [21 javaslat a biztonsági vezérlők közötti áthelyezéshez](#21-recommendations-moving-between-security-controls)                                                           | 2021. április                |
| [A "Rendszerfrissítések alkalmazása" biztonsági vezérlő elavult két ajánlása](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | 2021. április                |
| [Az ISO 27001 örökölt implementációja új ISO 27001:2013-ra vált](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)          | 2021. június                 |
| [Az AWS javaslatai általánosan elérhetőek lesznek](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     |  2021. augusztus           |
| [Az SQL-adatbesorolási javaslatok fejlesztései](#enhancements-to-sql-data-classification-recommendation)                                                     | 2021. 2. negyedév                   |
|                                                                                                                                                                       |                           |


### <a name="21-recommendations-moving-between-security-controls"></a>21 javaslat a biztonsági vezérlők között 

**A módosítás becsült dátuma:** 2021. április

Az alábbi javaslatok egy másik biztonsági vezérlőbe vannak áthelyezve. A biztonsági vezérlők a kapcsolódó biztonsági javaslatok logikai csoportjai, és tükrözik a sebezhető támadási felületeket. Ez a lépés biztosítja, hogy ezek a javaslatok a legmegfelelőbben szabályozhatóak a célkitűzésük elérése érdekében. 

Megtudhatja, hogy mely javaslatok vannak az egyes biztonsági vezérlőkben a Biztonsági vezérlők és azok javaslatai között.

|Ajánlás |Módosítás és hatás  |
|---------|---------|
|A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon<br>A sebezhetőségi felmérést engedélyezni kell a felügyelt SQL-példányon<br>Az SQL-adatbázisok biztonsági réseit új módon kell orvosolni<br>A virtuális gépeken az SQL-adatbázisok biztonsági réseit helyre kell ásni     |Áthelyezés a biztonsági rések orvoslása (6 pontot ér)<br>a biztonsági konfigurációk szervizlének (4 pontot ér).<br>A környezettől függően ezek a javaslatok kisebb hatással lesznek a pontszámra.|
|Az előfizetéshez egynél több tulajdonosnak kell hozzárendelve lennie<br>Az Automation-fiók változóit titkosítani kell<br>IoT-eszközök – A naplózva folyamat nem küldött eseményeket<br>IoT-eszközök – Az operációs rendszer alapkonfiguráció-ellenőrzési hibája<br>IoT-eszközök – A TLS-rejtjelcsomag frissítése szükséges<br>IoT-eszközök – Portok megnyitása az eszközön<br>IoT-eszközök – Az egyik láncban megengedő tűzfal-szabályzat található<br>IoT-eszközök – A bemeneti láncban megengedett tűzfalszabály található<br>IoT-eszközök – A kimeneti láncban megengedett tűzfalszabály található<br>A diagnosztikai naplók IoT Hub engedélyezni kell<br>IoT-eszközök – Az ügynök nem túl kihasznált üzeneteket küld<br>IoT-eszközök – Az alapértelmezett IP-szűrési szabályzatnak Megtagadásnak kell lennie<br>IoT-eszközök – IP-szűrési szabály nagy IP-címtartomány<br>IoT-eszközök – Az ügynök üzenet-időközeit és méretét módosítani kell<br>IoT-eszközök – Azonos hitelesítési hitelesítő adatok<br>IoT-eszközök – A naplózott folyamat nem küld eseményeket<br>IoT-eszközök – Az operációs rendszer alapkonfigurációját rögzíteni kell|A biztonsági **ajánlott eljárások megvalósítása:**.<br>Ha egy javaslat az Ajánlott biztonsági eljárások megvalósítása biztonsági vezérlőre kerül át, ami nem ér pontot, a javaslat már nem befolyásolja a biztonsági pontszámot.|
|||


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>A "Rendszerfrissítések alkalmazása" biztonsági vezérlő elavult két ajánlása

**A változás becsült dátuma:** 2021. április

A következő két javaslat elavult:

- Frissíteni kell az operációs rendszer verzióját a felhőszolgáltatás szerepkörei számára **–** Alapértelmezés szerint az Azure rendszeresen frissíti a vendég operációs rendszert a szolgáltatás konfigurációjában (.cscfg), például a Windows Server 2016-ban megadott operációsrendszer-család legújabb támogatott rendszerképére.
- **A Kubernetes Servicest** egy nem sebezhető Kubernetes-verzióra kell frissíteni – A javaslatok kiértékelései nem olyan széles körűek, mint szeretnénk. A javaslat aktuális verziója idővel egy továbbfejlesztett verzióra lesz lecserélve, amely jobban igazodik az ügyfél biztonsági igényeihez.


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>Az ISO 27001 örökölt implementációja új ISO 27001:2013-ra van cserélve

Az ISO 27001 régi implementációja el lesz távolítva a Security Center megfelelőségi irányítópultjáról. Ha nyomon követi az ISO 27001 szabványnak az Security Center-nek való megfelelőségét, minden érintett felügyeleti csoporthoz vagy előfizetéshez fel kell vennie az új ISO 27001:2013 szabványt, és a rendszer hamarosan eltávolítja a jelenlegi iso 27001-et az irányítópultról.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Security Center az ISO 27001 örökölt implementációjának eltávolításáról szóló üzenetet megjelenítő jogszabályi megfelelőségi irányítópultot." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Az AWS javaslatai általánosan elérhetőek lesznek

**A módosítás becsült dátuma:** 2021. augusztus

Azure Security Center az Azure, a Amazon Web Services (AWS) és a Google Cloud Platform (GCP) számítási feladatait.

Az AWS Security Hub javaslatai a felhőalapú összekötők bevezetése óta előzetes verzióban voltak elérhetőek. Az Előzetes  verzióként megjelölt javaslatok nem szerepelnek a biztonsági pontszám kiszámításakor, de a lehető legnagyobb mértékben ki kell őket szervizelni, hogy az előzetes verzió időszakának végén hozzájáruljanak a pontszámhoz.

Ezzel a módosítással az AWS-javaslatok két halmaza lesz áttért a GA-be:

- [A Security Hub PCI DSS vezérlői](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [A Security Hub CIS AWS Foundations Benchmark vezérlői](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Ha ezek az adatok gavallán vannak, és az értékelések az AWS-erőforrásokon futnak, az eredmények hatással lesznek az összes több- és hibridfelhő-alapú erőforrás kombinált biztonsági pontszámra.



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Az SQL-adatbesorolási javaslat fejlesztései

**A módosítás becsült dátuma:** 2021. 2. negyedév

Az **SQL-adatbázisok** bizalmas adatait az Adatbesorolási biztonsági vezérlő alkalmazása alatt kell besorolni egy új verzióra, amely jobban igazodik a Microsoft adatbesorolási stratégiájához.  Ennek eredményeképpen a javaslat azonosítója is megváltozik (jelenleg b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Következő lépések

A termék legutóbbi módosításait a Újdonságok a [termékben? Azure Security Center.](release-notes.md)