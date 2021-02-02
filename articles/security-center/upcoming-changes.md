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
ms.openlocfilehash: d5de16c8156762a229d6c707080bc197dc206a7c
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475590"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>A Azure Security Center fontos jövőbeli módosításai

> [!IMPORTANT]
> Az ezen az oldalon található információk a kiadás előtti termékekhez vagy szolgáltatásokhoz kapcsolódnak, amelyek a kereskedelmi forgalomba hozatal előtt jelentős mértékben módosíthatók, ha még nem. A Microsoft nem vállal semmilyen kifejezett vagy vélelmezett szavatosságot az itt megadott információk tekintetében.

Ezen az oldalon megismerheti a Security Center tervezett módosításokat. Ismerteti a termék tervezett módosításait, amelyek hatással lehetnek a biztonságos pontszámra vagy munkafolyamatokra.

Ha a legújabb kibocsátási megjegyzéseket keresi, a [Azure Security Center újdonságai](release-notes.md)között találhatja meg őket.


## <a name="planned-changes"></a>Tervezett változások

- [A Kubernetes munkaterhelés-védelmi javaslatai hamarosan elérhetők lesznek általánosan elérhetővé (GA)](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- [A System Updates (rendszerfrissítések alkalmazása) biztonsági ellenőrzésének két javaslata elavult](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Az SQL-adatbesorolásra vonatkozó javaslat továbbfejlesztése](#enhancements-to-sql-data-classification-recommendation)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>A Kubernetes munkaterhelés-védelmi javaslatai hamarosan elérhetők lesznek általánosan elérhetővé (GA)

**Változás becsült dátuma:** Február 2021

A [Kubernetes-munkaterhelések védelme](kubernetes-workload-protections.md) című cikkben ismertetett Kubernetes munkaterhelés-védelmi javaslatok jelenleg előzetes verzióban érhetők el. Habár egy javaslat előzetes verzióban érhető el, nem jelenik meg az erőforrás állapota, és nem szerepel a biztonságos pontszám számításában.

A javaslatok hamarosan elérhetők lesznek általánosan elérhetővé (GA), így a pontszám kiszámítása is *megtörténik.* Ha már nem szervizelte őket, ez a biztonsági pontszám enyhe kihatását okozhatja.

Ha lehetséges, javítsa őket (ha szeretné megismerni, hogyan javíthatja az [ajánlásokat a Azure Security Center](security-center-remediate-recommendations.md)).

A Kubernetes munkaterhelés-védelmi javaslatai a következők:

- A Kubernetes Azure Policy bővítményét telepíteni és engedélyezni kell a fürtökön
- A tároló CPU-és memória-korlátozásait kényszeríteni kell
- Az emelt szintű tárolókat el kell kerülni
- Nem módosítható (csak olvasható) rendszerindító fájlrendszert kell kikényszeríteni a tárolók számára
- A jogosultság-eszkalációs tárolót el kell kerülni
- A tárolók futtatását root felhasználóként el kell kerülni
- A bizalmas gazdagépek névtereit megosztó tárolókat el kell kerülni
- A minimálisan privilegizált Linux-funkciókat kell kikényszeríteni a tárolók számára
- A pod HostPath mennyiségi csatlakoztatások használatát egy ismert listára kell korlátozni
- A tárolóknak csak az engedélyezett portok figyelésére kell figyelniük
- A szolgáltatásoknak csak a megengedett portok figyelésére kell figyelniük
- A gazdagép hálózatkezelésének és portjainak használatát korlátozni kell
- A tárolók AppArmor-profiljának felülbírálását vagy letiltását korlátozni kell
- A tároló lemezképeit csak a megbízható kibocsátásiegység-forgalmi jegyzékből kell telepíteni             

További információ ezekről a javaslatokról a [Kubernetes-munkaterhelések elleni védelemben](kubernetes-workload-protections.md).

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>A System Updates (rendszerfrissítések alkalmazása) biztonsági ellenőrzésének két javaslata elavult 

**Változás becsült dátuma:** Február 2021

A következő két javaslatot ütemezjük a 2021 februárjában:

- A számítógépeket újra kell **indítani a rendszerfrissítések alkalmazásához**. Ez enyhe hatással lehet a biztonságos pontszámra.
- A **figyelési ügynököt telepíteni kell a gépekre**. Ez a javaslat csak a helyszíni gépekre vonatkozik, és a logikája egy másik javaslatba kerül át, **log Analytics az ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken**. Ez enyhe hatással lehet a biztonságos pontszámra.

Javasoljuk, hogy ellenőrizze a folyamatos exportálási és munkafolyamat-automatizálási konfigurációkat, és ellenőrizze, hogy ezek a javaslatok szerepelnek-e bennük. Emellett az azokat használó irányítópultokat vagy egyéb figyelési eszközöket ennek megfelelően kell frissíteni.

További információ ezekről a javaslatokról a [biztonsági javaslatok hivatkozását ismertető oldalon](recommendations-reference.md)található.


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Az SQL-adatbesorolásra vonatkozó javaslat továbbfejlesztése

**Változás becsült dátuma:** Q2 2021

Az **SQL-adatbázisokban a bizalmas adatokra** vonatkozó ajánlás aktuális **verzióját a Microsoft adatbesorolási** stratégiájával jobban illeszkedő új verzióra cseréli a rendszer. Ennek eredményeképpen:

- A javaslat a továbbiakban nem lesz hatással a biztonságos pontszámra
- A biztonsági vezérlő ("adatbesorolás alkalmazása") már nem lesz hatással a biztonságos pontszámra
- Az ajánlás azonosítója is módosul (jelenleg b0df6f56-862d-4730-8597-38c0fd4ebd59)



## <a name="next-steps"></a>Következő lépések

A termék legutóbbi módosításaival kapcsolatban tekintse [meg az Újdonságok a Azure Security Center?](release-notes.md)című témakört.
