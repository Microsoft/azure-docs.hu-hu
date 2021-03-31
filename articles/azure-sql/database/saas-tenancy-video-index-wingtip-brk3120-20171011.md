---
title: SaaS SQL-alkalmazás videója
description: Ez a cikk több időpontot indexel a 81 perces videóban a SaaS DB-kiépítési alkalmazás kialakításáról, az Ignite konferencián, amely 2017. október 11-én tartott. Az Önt érdeklő részre ugorhat. Legalább 3 mintát ismertetünk. A fejlesztést és a felügyeletet egyszerűsítő Azure-funkciókat ismertetjük.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.custom: sqldbrb=1
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 8cceeef7e41e8fc5c48b4e67cdb1adf3aa4f02a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89483864"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Több-bérlős SaaS-alkalmazáshoz tartozó, Azure SQL Database használatával indexelt videó
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk egy, a SaaS-kiosztási modellekkel vagy mintázatokkal kapcsolatos 81 perces videó időhelyein jegyzett index. Ebből a cikkből megtudhatja, hogy a videóban visszafelé vagy előre ugorjon, melyre érdekli Önt. A videó ismerteti a több-bérlős adatbázis-alkalmazás főbb tervezési lehetőségeit Azure SQL Databaseon. A videó tartalmaz demókat, a felügyeleti kód bemutatóit, és a tapasztalatok alapján több részletet is megtalál, mint amennyire az írásos dokumentációban lehet.

A videó az írásos dokumentációban található információkat erősíti meg: 
- *Fogalmi:* [több-bérlős SaaS-adatbázisok bérleti mintái][saas-concept-design-patterns-563e]
- *Oktatóanyagok:* [a Wingtip tickets SaaS-alkalmazás][saas-how-welcome-wingtip-app-679t]

A videó és a cikkek a több-bérlős alkalmazások létrehozásának számos fázisát írják le a felhőben Azure SQL Database. A Azure SQL Database speciális funkciói megkönnyítik a több-bérlős alkalmazások fejlesztését és megvalósítását, amelyek megkönnyítik a felügyeletet és a megbízható teljesítményt.

Rendszeresen frissítjük az írott dokumentációt. A videó nem lett szerkesztve vagy frissítve, ezért a részletes részletessége elavult lehet.



## <a name="sequence-of-38-time-indexed-screenshots"></a>38-os idő – indexelt képernyőképek

Ez a szakasz az 38-os beszélgetések időpontját indexeli az 81 perces videóban. A rendszer minden egyes időindexet a videó képernyőképével, és esetenként további információkkal láthat.

Minden egyes index formátuma *PP: SS*. Például a második indexelt idő helye, a megcímkézett **munkamenet célja**, a **0:03:11**-as hozzávetőleges időpontnál kezdődik.


### <a name="compact-links-to-video-indexed-time-locations"></a>A videó-indexelt időpontokra mutató hivatkozások tömörítése

A következő címek a jelen cikk későbbi részében található, a hozzájuk fűzött megjegyzésekre mutató hivatkozásokat tartalmaznak:

- [1. **(Start)** üdvözlő dia, 0:00:03](#anchor-image-wtip-min00001)
- [2. munkamenet-célkitűzések, 0:03:11](#anchor-image-wtip-min00311)
- [3. napirend, 0:04:17](#anchor-image-wtip-min00417)
- [4. több-bérlős Web App, 0:05:05](#anchor-image-wtip-min00505)
- [5. az alkalmazás webes űrlapja működés közben, 0:05:55](#anchor-image-wtip-min00555)
- [6. bérlői díj (méretezés, elkülönítés, helyreállítás), 0:09:31](#anchor-image-wtip-min00931)
- [7. több-bérlős adatbázis-modellek: előnyök és hátrányok, 0:11:59](#anchor-image-wtip-min01159)
- [8. a hibrid modell a MT/ST, 0:13:01 előnyeit ötvözi](#anchor-image-wtip-min01301)
- [9. egybérlős és több-bérlős: előnyök és hátrányok, 0:16:44](#anchor-image-wtip-min01644)
- [10. a készletek költséghatékonyak a kiszámíthatatlan munkaterhelések számára, 0:19:36](#anchor-image-wtip-min01936)
- [11. az adatbázis-bérlői és hibrid ST/MT-alapú bemutató, 0:20:08](#anchor-image-wtip-min02008)
- [12. élő alkalmazás űrlapja, amely a Dojot, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB és nem egy DBA a szem előtt, 0:28:54](#anchor-image-wtip-min02854)
- [14. MYOB rugalmas készlet használati példája, 0:29:40](#anchor-image-wtip-min02940)
- [15. MYOB és egyéb ISV-i tanulás, 0:31:36](#anchor-image-wtip-min03136)
- [16. minták a E2E SaaS-forgatókönyvben, 0:43:15](#anchor-image-wtip-min04315)
- [17. Canonical Hybrid több-bérlős SaaS-alkalmazás, 0:47:33](#anchor-image-wtip-min04733)
- [18. Wingtip SaaS-minta alkalmazás, 0:48:10](#anchor-image-wtip-min04810)
- [19. az oktatóanyagokban feltárt forgatókönyvek és minták, 0:49:10](#anchor-image-wtip-min04910)
- [20. oktatóanyagok és GitHub-adattár bemutatója, 0:50:18](#anchor-image-wtip-min05018)
- [21. GitHub-tárház – Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. a minták feltárása, 0:56:20](#anchor-image-wtip-min05620)
- [23. bérlők üzembe helyezése és bevezetése, 0:57:44](#anchor-image-wtip-min05744)
- [24. bérlők és alkalmazás-kapcsolatok kiépítés, 0:58:58](#anchor-image-wtip-min05858)
- [25. a felügyeleti parancsfájlok egyetlen bérlő üzembe helyezésének bemutatója, 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell kiépítése és katalogizálása, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * FROM TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. előre nem látható bérlői számítási feladatok kezelése, 1:04:36](#anchor-image-wtip-min10436)
- [29. rugalmas készlet figyelése, 1:06:39](#anchor-image-wtip-min10639)
- [30. a terhelés és a teljesítmény monitorozása, 1:09:42](#anchor-image-wtip-min10942)
- [31. a séma kezelése a skálán, 1:10:33](#anchor-image-wtip-min11033)
- [32. elosztott lekérdezés a bérlői adatbázisok között, 1:12:21](#anchor-image-wtip-min11221)
- [33. a Ticket Generation bemutatója, 1:12:32](#anchor-image-wtip-min11232)
- [34. SSMS alkalmi elemzés, 1:12:46](#anchor-image-wtip-min11246)
- [35. a bérlői adatok kinyerése az Azure szinapszis Analyticsbe, 1:16:32](#anchor-image-wtip-min11632)
- [36. a napi értékesítések eloszlásának gráfja, 1:16:48](#anchor-image-wtip-min11648)
- [37. a becsomagolás és a művelet meghívása, 1:19:52](#anchor-image-wtip-min11952)
- [38. További információ az erőforrásokról, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>A videó megjegyzésekkel ellátott indexelési ideje

Ha bármelyik képernyőképre kattint, a videó pontos időpontjára kerül.


&nbsp; <a name="anchor-image-wtip-min00001"></a>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Start)* üdvözlő dia, 0:00:01

*Ismerkedés a MYOB: Design Patterns for SaaS-alkalmazások Azure SQL Database-BRK3120*

[![Üdvözlő dia][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Title: a MYOB megismerése: tervezési minták az SaaS-alkalmazásokhoz Azure SQL Database
- Bill.Gibson@microsoft.com
- Elsődleges program-kezelő, Azure SQL Database
- Microsoft Ignite Session BRK3120, Orlando, FL USA, október/11 2017


&nbsp; <a name="anchor-image-wtip-min00311"></a>
#### <a name="2-session-objectives-00153"></a>2. munkamenet-célkitűzések, 0:01:53
[![Munkamenet-célkitűzések][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternatív modellek a több-bérlős alkalmazásokhoz, az előnyökkel és hátrányokkal.
- SaaS-minták a fejlesztési, kezelési és erőforrás-költségek csökkentése érdekében.
- Egy minta alkalmazás + szkriptek.
- A Pásti-funkciók és SaaS-minták révén a több-bérlős SaaS-hoz SQL Database egy rugalmasan méretezhető, költséghatékony adatplatformot.


&nbsp; <a name="anchor-image-wtip-min00417"></a>
#### <a name="3-agenda-00409"></a>3. napirend, 0:04:09
[![Program][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"></a>
#### <a name="4-multi-tenant-web-app-00500"></a>4. több-bérlős Web App, 0:05:00
[![Wingtip SaaS-alkalmazás: több-bérlős webes alkalmazás][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"></a>
#### <a name="5-app-web-form-in-action-00539"></a>5. az alkalmazás webes űrlapja működés közben, 0:05:39
[![Az alkalmazás webes űrlapja működés közben][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"></a>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. bérlői díj (méretezés, elkülönítés, helyreállítás), 0:06:58
[![Bérlői díj, méretezés, elkülönítés, helyreállítás][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"></a>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. több-bérlős adatbázis-modellek: előnyök és hátrányok, 0:09:52
[![Több-bérlős adatbázis-modellek: előnyök és hátrányok][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"></a>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. a hibrid modell a MT/ST, 0:12:29 előnyeit ötvözi
[![A hibrid modell az MT/ST előnyeit ötvözi][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"></a>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. egybérlős és több-bérlős: előnyök és hátrányok, 0:13:11
[![Egybérlős és több-bérlős: előnyök és hátrányok][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"></a>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. a készletek költséghatékonyak a kiszámíthatatlan munkaterhelések számára, 0:17:49
[![A készletek nem kiszámítható számítási feladatokhoz költséghatékonyak][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"></a>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. az adatbázis-bérlői és hibrid ST/MT-alapú bemutató, 0:19:59
[![Az adatbázis/bérlő és a hibrid ST/MT bemutatója][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"></a>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. élő alkalmazás űrlapja, amely a Dojot, 0:20:10
[![A Dojot bemutató élő alkalmazás űrlapja][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"></a>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB és nem egy DBA a szem előtt, 0:25:06
[![MYOB és nem egy DBA a szemekben][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"></a>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. MYOB rugalmas készlet használati példája, 0:29:30
[![MYOB rugalmas készlet használati példája][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"></a>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. MYOB és egyéb ISV-i tanulás, 0:31:25
[![MYOB és más ISV-ket tanulhat][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"></a>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. minták a E2E SaaS-forgatókönyvben, 0:31:42
[![Mintázatok E2E SaaS-forgatókönyvbe][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"></a>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Canonical Hybrid több-bérlős SaaS-alkalmazás, 0:46:04
[![Canonical Hybrid több-bérlős SaaS-alkalmazás][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"></a>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Wingtip SaaS-minta alkalmazás, 0:48:01
[![Wingtip SaaS-minta alkalmazás][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"></a>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. az oktatóanyagokban feltárt forgatókönyvek és minták, 0:49:00
[![Az oktatóanyagokban feltárt forgatókönyvek és minták][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"></a>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. oktatóanyagok és GitHub-adattár bemutatója, 0:50:12
[![Bemutató oktatóanyagok és GitHub-tárház][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"></a>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. GitHub-tárház – Microsoft/WingtipSaaS, 0:50:32
[![GitHub-tárház – Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"></a>
#### <a name="22-exploring-the-patterns-05615"></a>22. a minták feltárása, 0:56:15
[![A minták feltárása][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"></a>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. bérlők üzembe helyezése és bevezetése, 0:56:19
[![Bérlők és bevezetések kiépítés][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"></a>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. bérlők és alkalmazás-kapcsolatok kiépítés, 0:57:52
[![Bérlők és alkalmazás-kapcsolatok kiépítés][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"></a>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. a felügyeleti parancsfájlok egyetlen bérlő üzembe helyezésének bemutatója, 0:59:36
[![Egyetlen bérlő kiépítési felügyeleti parancsfájljainak bemutatója][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"></a>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell kiépítése és katalogizálása, 0:59:56
[![PowerShell a létesítéshez és a katalógushoz][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"></a>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * FROM TenantsExtended, 1:03:25
[![T-SQL SELECT * a TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"></a>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. előre nem látható bérlői számítási feladatok kezelése, 1:03:34
[![Előre nem látható bérlői számítási feladatok kezelése][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"></a>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. rugalmas készlet figyelése, 1:06:32
[![Rugalmas készlet figyelése][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"></a>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. a terhelés és a teljesítmény monitorozása, 1:09:37
[![A betöltés és a teljesítmény figyelése][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"></a>
#### <a name="31-schema-management-at-scale-10940"></a>31. a séma kezelése a skálán, 1:09:40
[![Séma-kezelés nagy méretekben][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"></a>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. elosztott lekérdezés a bérlői adatbázisok között, 1:11:18
[![Elosztott lekérdezés a bérlői adatbázisok között][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"></a>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. a Ticket Generation bemutatója, 1:12:28
[![A Ticket Generation bemutatója][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"></a>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. SSMS alkalmi elemzés, 1:12:35
[![SSMS ad hoc elemzés][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"></a>
#### <a name="35-extract-tenant-data-into-azure-synapse-analytics-11546"></a>35. a bérlői adatok kinyerése az Azure szinapszis Analyticsbe, 1:15:46
[![Bérlői adatok kinyerése az Azure szinapszis Analytics szolgáltatásban][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"></a>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. a napi értékesítések eloszlásának gráfja, 1:16:38
[![A napi értékesítés eloszlásának gráfja][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"></a>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. a becsomagolás és a művelet meghívása, 1:17:43
[![Tördelés és művelet meghívása][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"></a>
#### <a name="38-resources-for-more-information-12035"></a>38. További információ az erőforrásokról, 1:20:35
[![További információ az erőforrásokról][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Blogbejegyzés, május 22., 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Fogalmi:* [több-bérlős SaaS-adatbázisok bérleti mintái][saas-concept-design-patterns-563e]

- *Oktatóanyagok:* [a Wingtip tickets SaaS-alkalmazás][saas-how-welcome-wingtip-app-679t]

- GitHub-adattárak a Wingtip tickets SaaS-bérlői alkalmazáshoz:
    - [GitHub-tárház – önálló alkalmazás modellje][github-wingtip-standaloneapp].
    - [GitHub-adattár – db/bérlői modell][github-wingtip-dbpertenant].
    - [GitHub-tárház – több-bérlős adatbázis-modell][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Következő lépések

- [Első oktatóanyag-cikk][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Üdvözlő dia"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Munkamenet-célkitűzések."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Program."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Wingtip SaaS-alkalmazás: több-bérlős webes alkalmazás"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Az alkalmazás webes űrlapja működés közben"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Bérlői díj, méretezés, elkülönítés, helyreállítás"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Több-bérlős adatbázis-modellek: előnyök és hátrányok"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "A hibrid modell az MT/ST előnyeit ötvözi"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Egybérlős és több-bérlős: előnyök és hátrányok"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "A készletek nem kiszámítható számítási feladatokhoz költséghatékonyak"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Az adatbázis/bérlő és a hibrid ST/MT bemutatója"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "A Dojot bemutató élő alkalmazás űrlapja"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB és nem egy DBA a szemekben"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "MYOB rugalmas készlet használati példája"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "MYOB és más ISV-ket tanulhat"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Mintázatok E2E SaaS-forgatókönyvbe"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Canonical Hybrid több-bérlős SaaS-alkalmazás"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Wingtip SaaS-minta alkalmazás"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Az oktatóanyagokban feltárt forgatókönyvek és minták"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Oktatóanyagok és GitHub-tárház bemutatója"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "GitHub-tárház – Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "A minták feltárása"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Bérlők és bevezetések kiépítés"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Bérlők és alkalmazás-kapcsolatok kiépítés"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Egyetlen bérlő kiépítési felügyeleti parancsfájljainak bemutatója"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "PowerShell a létesítéshez és a katalógushoz"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * a TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Előre nem látható bérlői számítási feladatok kezelése"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Rugalmas készlet figyelése"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "A betöltés és a teljesítmény figyelése"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Séma-kezelés nagy méretekben"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Elosztott lekérdezés a bérlői adatbázisok között"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "A Ticket Generation bemutatója"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "SSMS ad hoc elemzés"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Bérlői adatok kinyerése az Azure szinapszis Analytics szolgáltatásban"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "A napi értékesítés eloszlásának gráfja"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Tördelés és művelet meghívása"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "További információ az erőforrásokról"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

