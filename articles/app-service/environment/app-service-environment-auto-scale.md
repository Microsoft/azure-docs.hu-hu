---
title: Automatikus skálázás v1
description: Automatikus skálázás és App Service Environment v1. Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: btardif
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4f071c0d09fc2fa97eeea45bd82228b7eb8434a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021656"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Automatikus skálázás és App Service Environment v1

> [!NOTE]
> Ez a cikk a App Service Environment v1-es verzióról szól.  A App Service Environment újabb verziója könnyebben használható, és nagyobb teljesítményű infrastruktúrán fut. Ha többet szeretne megtudni az új verzióról, kezdje a [app Service Environment bevezetésével](intro.md).
> 

Azure App Service környezetek támogatják az automatikus *skálázást*. Az egyes munkavégző készleteket mérőszámok vagy ütemezés alapján is átméretezheti.

![Munkavégző készletre vonatkozó autoskálázási beállítások][intro]

Az automatikus skálázás optimalizálja az erőforrás-használatot azáltal, hogy a költségvetés és a betöltési profil méretének megfelelően automatikusan megnöveli és csökkenti egy App Service környezetét.

## <a name="configure-worker-pool-autoscale"></a>A munkavégző készlet autoskálázásának konfigurálása
Az autoskálázás funkciót a munkavégző készlet **Beállítások** lapján érheti el.

![A munkavégző készlet beállítások lapja.][settings-scale]

Innentől kezdve az illesztőfelületnek jól ismertnek kell lennie, mivel az App Service terv skálázásakor megjelenő élmény. 

![Manuális méretezési beállítások.][scale-manual]

Beállíthat egy automatikusan méretezhető profilt is.

![Az autoskálázás beállításai.][scale-profile]

Az autoskálázási profilok hasznosak a méretezési korlátok megadásához. Így egységes teljesítménybeli élményben lehet részük, ha az alsó korlátot (1) és egy előre jelezhető töltési korlátot állít be egy felső határ (2) beállításával.

![Méretezési beállítások a profilban.][scale-profile2]

Miután definiálta a profilt, a profilban definiált határokon belül a munkavégző készletben lévő példányok számának vertikális fel-vagy leskálázásához hozzáadhat új méretezési szabályokat is. Az autoskálázási szabályok mérőszámokon alapulnak.

![Skálázási szabály.][scale-rule]

 A munkavégző készletek vagy előtér-metrikák az autoskálázási szabályok definiálására használhatók. Ezek a metrikák ugyanazok a metrikák, amelyeket az erőforrás panel diagramjaiban figyelheti, vagy riasztásokat állíthat be.

## <a name="autoscale-example"></a>Példa az autoscalere
Az App Service-környezetek méretezése a legjobb forgatókönyvek segítségével ábrázolható.

Ez a cikk az autoscale beállításakor szükséges összes szempontot ismerteti. A cikk végigvezeti a App Service Environmentban üzemeltetett App Service környezetek automatikus skálázásakor felmerülő interakciók lépésein.

### <a name="scenario-introduction"></a>Forgatókönyv bemutatása
Frank a rendszergazda, aki áttelepítette az App Service-környezetbe felügyelt munkaterhelések egy részét.

A App Service környezet kézi méretezésre van konfigurálva a következőképpen:

* **Előtér-végpontok:** 3
* **Worker Pool 1**: 10
* **Worker Pool 2**: 5
* **Worker Pool 3**: 5

A Worker Pool 1 az éles számítási feladatokhoz használatos, a 2. munkavégző készlet és a 3. munkavégző készlet pedig a minőségbiztosítási (QA) és a fejlesztési számítási feladatokhoz használatos.

A QA és a dev App Service tervei kézi méretezésre vannak konfigurálva. Az éles App Service terv automatikus méretezésre van beállítva a terhelés és a forgalom változásainak kezeléséhez.

Frank nagyon ismeri az alkalmazást. Tudják, hogy a betöltés maximális ideje 9:00 és 6:00 PM között van, mivel ez egy üzletági (LOB) alkalmazás, amelyet az alkalmazottak az irodában használnak. A használat az adott napra vonatkozó felhasználók után csökken. A csúcsidőben kívül még mindig van némi terhelés, mivel a felhasználók mobileszközök vagy otthoni számítógépek használatával távolról érhetik el az alkalmazást. Az éles App Service terv már konfigurálva van a CPU-használaton alapuló, az alábbi szabályok alapján történő autoskálázásra:

![A LOB-alkalmazás speciális beállításai.][asp-scale]

| **Autoscale Profile – hétköznapok – App Service terv** | **Autoscale Profile – weekends – App Service terv** |
| --- | --- |
| **Név:** Hétköznap profil |**Név:** Hétvégi profil |
| **Skálázás:** Ütemterv és teljesítmény szabályok |**Skálázás:** Ütemterv és teljesítmény szabályok |
| **Profil:** Hétköznapokon |**Profil:** Hétvégi |
| **Írja be a következőt:** Megismétlődésének |**Írja be a következőt:** Megismétlődésének |
| **Céltartomány:** 5 – 20 példány |**Céltartomány:** 3 – 10 példány |
| **Napok:** Hétfő, kedd, szerda, csütörtök, péntek |**Napok:** Szombat, vasárnap |
| **Kezdés időpontja:** 9:00 |**Kezdés időpontja:** 9:00 |
| **Időzóna:** UTC – 08 |**Időzóna:** UTC – 08 |
|  | |
| **Autoskálázási szabály (vertikális felskálázás)** |**Autoskálázási szabály (vertikális felskálázás)** |
| **Erőforrás:** Éles üzem (App Service Environment) |**Erőforrás:** Éles üzem (App Service Environment) |
| **Metrika:** CPU |**Metrika:** CPU |
| **Művelet:** Nagyobb, mint 60% |**Művelet:** Nagyobb, mint 80% |
| **Időtartam:** 5 perc |**Időtartam:** 10 perc |
| **Idő összesítése:** Átlagos |**Idő összesítése:** Átlagos |
| **Művelet:** Növekedés száma 2 szerint |**Művelet:** Növekedés száma 1 szerint |
| **Lehűlni (perc):** 15 |**Lehűlni (perc):** 20 |
|  | |
| **Autoskálázási szabály (vertikális leskálázás)** |**Autoskálázási szabály (vertikális leskálázás)** |
| **Erőforrás:** Éles üzem (App Service Environment) |**Erőforrás:** Éles üzem (App Service Environment) |
| **Metrika:** CPU |**Metrika:** CPU |
| **Művelet:** Kevesebb mint 30% |**Művelet:** Kevesebb mint 20% |
| **Időtartam:** 10 perc |**Időtartam:** 15 perc |
| **Idő összesítése:** Átlagos |**Idő összesítése:** Átlagos |
| **Művelet:** Szám csökkentése 1 szerint |**Művelet:** Szám csökkentése 1 szerint |
| **Lehűlni (perc):** 20 |**Lehűlni (perc):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Az inflációs ráta App Service tervezése
App Service az autoskálázásra konfigurált csomagok óránkénti maximális száma. Ez az arány az autoskálázási szabályban megadott értékek alapján számítható ki.

A *app Service terv inflációs arányának* megismerése és kiszámítása fontos a app Service környezetek autoskálázása esetében, mert a munkavégző készletek skálázási változásai nem azonnaliek.

A App Service terv inflációs arányának kiszámítása a következőképpen történik:

![App Service az inflációs ráta számításának megtervezése.][ASP-Inflation]

Az autoscale – Felskálázási szabály alapján az üzemi App Service terv hétköznap-profiljához:

![A App Service az inflációs arányt az autoscale-vertikális Felskálázási szabály alapján megtervezheti a hétköznapokon.][Equation1]

Ha az autoscale – vertikális Felskálázási szabály az éles App Service terv hétvégi profiljára vonatkozik, a képlet a következőre lesz feloldva:

![App Service a hétvégek inflációs arányának megtervezése az autoskálázás – vertikális Felskálázási szabály alapján.][Equation2]

Ez az érték kiszámítható a skálázási műveletekhez is.

Az üzemi App Service-csomag hétköznap-profiljára vonatkozó, az autoscale (vertikális leskálázás) szabály alapján a következőképpen néz ki:

![A App Service az inflációs arányt az autoskálázás – leskálázási szabály alapján a hétköznapokra vonatkozóan tervezze meg.][Equation3]

Abban az esetben, ha az autoskálázás – leskálázási szabályt az éles App Service terv hétvégi profiljához szeretné feloldani, a képlet a következőre lesz feloldva:  

![App Service a hétvégek inflációs arányának megtervezése az autoskálázás – leskálázási szabály alapján.][Equation4]

A termelési App Service terv a hét folyamán legfeljebb nyolc példány/óra, a hétvégén pedig négy példány/óra használatával növekedhet. A hét során legfeljebb négy példány/óra, a hétvégén pedig hat példány/óra után szabadíthat fel példányokat.

Ha több App Service-csomag fut egy munkavégző készletben, akkor a *teljes inflációs sebességet* az adott feldolgozó készletben üzemeltetett app Service csomagok inflációs arányának összegeként kell kiszámítani.

![Az összes inflációs ráta kiszámítása a munkavégző készletben üzemeltetett több App Service-csomag esetében.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>A App Service terv inflációs arányának használata a munkavégző készletre vonatkozó autoskálázási szabályok definiálásához
Azokat a munkavégző készleteket, amelyek az autoskálázásra konfigurált App Service csomagokat futtatnak, kapacitás-puffert kell lefoglalni. A puffer lehetővé teszi, hogy az automatikus skálázási műveletek szükség szerint növelje és zsugorodzák a App Service tervet. A minimális puffer a számított teljes App Service terv inflációs rátája.

Mivel App Service a környezet skálázási műveleteinek eltartása némi időbe telik, a változásoknak a skálázási művelet végrehajtása során esetlegesen felmerülő további változásokat is figyelembe kell venniük. Ennek a késésnek a betartása érdekében javasoljuk, hogy a számított összesített App Service terv inflációs sebességét használja az egyes autoskálázási műveletekhez hozzáadott példányok minimális számaként.

Ezekkel az információkkal a frank meghatározhatja a következő autoscale-profilt és-szabályokat:

![ÜZLETÁGI példa a profilokra vonatkozó szabályok autoskálázására.][Worker-Pool-Scale]

| **Autoscale profil – hétköznapok** | **Autoskálázási profil – hétvégek** |
| --- | --- |
| **Név:** Hétköznap profil |**Név:** Hétvégi profil |
| **Skálázás:** Ütemterv és teljesítmény szabályok |**Skálázás:** Ütemterv és teljesítmény szabályok |
| **Profil:** Hétköznapokon |**Profil:** Hétvégi |
| **Írja be a következőt:** Megismétlődésének |**Írja be a következőt:** Megismétlődésének |
| **Céltartomány:** 13 – 25 példány |**Céltartomány:** 6 – 15 példány |
| **Napok:** Hétfő, kedd, szerda, csütörtök, péntek |**Napok:** Szombat, vasárnap |
| **Kezdés időpontja:** 7:00 |**Kezdés időpontja:** 9:00 |
| **Időzóna:** UTC – 08 |**Időzóna:** UTC – 08 |
|  | |
| **Autoskálázási szabály (vertikális felskálázás)** |**Autoskálázási szabály (vertikális felskálázás)** |
| **Erőforrás:** 1. munkavégző készlet |**Erőforrás:** 1. munkavégző készlet |
| **Metrika:** WorkersAvailable |**Metrika:** WorkersAvailable |
| **Művelet:** Kevesebb mint 8 |**Művelet:** Kevesebb mint 3 |
| **Időtartam:** 20 perc |**Időtartam:** 30 perc |
| **Idő összesítése:** Átlagos |**Idő összesítése:** Átlagos |
| **Művelet:** Növekedés száma 8 szerint |**Művelet:** Növekedés száma 3 szerint |
| **Lehűlni (perc):** 180 |**Lehűlni (perc):** 180 |
|  | |
| **Autoskálázási szabály (vertikális leskálázás)** |**Autoskálázási szabály (vertikális leskálázás)** |
| **Erőforrás:** 1. munkavégző készlet |**Erőforrás:** 1. munkavégző készlet |
| **Metrika:** WorkersAvailable |**Metrika:** WorkersAvailable |
| **Művelet:** Nagyobb, mint 8 |**Művelet:** Nagyobb, mint 3 |
| **Időtartam:** 20 perc |**Időtartam:** 15 perc |
| **Idő összesítése:** Átlagos |**Idő összesítése:** Átlagos |
| **Művelet:** Csökkenés száma 2 szerint |**Művelet:** Csökkenés száma 3 szerint |
| **Lehűlni (perc):** 120 |**Lehűlni (perc):** 120 |

A profilban definiált céltartomány a App Service terv + puffer számára a profilban meghatározott minimális példányszám szerint van kiszámítva.

A maximális tartomány a munkavégző készletben üzemeltetett összes App Service-csomag maximális tartományának összege.

A vertikális Felskálázási szabályok számának növelési arányát legalább 1X-re kell állítani az App Service terv inflációs sebességének megtervezéséhez.

A csökkentések száma 1/2X vagy 1X a App Service a skálázási inflációs ráta alapján állítható be.

### <a name="autoscale-for-front-end-pool"></a>Az előtér-készlethez tartozó autoskálázás
Az előtérbeli autoskálázás szabályai egyszerűbbek, mint a munkavégző készleteknél. Elsősorban a  
Győződjön meg arról, hogy a mérés és a hűtési időzítő időtartama azt veszi figyelembe, hogy egy App Service-csomag skálázási műveletei nem azonnaliek.

Ebben a forgatókönyvben Frank tudja, hogy a hibák száma nő, miután az előtér eléri a 80%-os CPU-kihasználtságot, és beállítja az autoskálázási szabályt, hogy a következőképpen növelje a példányokat:

![Előtér-készlet beállításainak kiosztása.][Front-End-Scale]

| **Autoscale profil – előtér** |
| --- |
| **Név:** Autoscale – elülső végek |
| **Skálázás:** Ütemterv és teljesítmény szabályok |
| **Profil:** Mindennap |
| **Írja be a következőt:** Megismétlődésének |
| **Céltartomány:** 3 – 10 példány |
| **Napok:** Mindennap |
| **Kezdés időpontja:** 9:00 |
| **Időzóna:** UTC – 08 |
|  |
| **Autoskálázási szabály (vertikális felskálázás)** |
| **Erőforrás:** Előtér-készlet |
| **Metrika:** CPU |
| **Művelet:** Nagyobb, mint 60% |
| **Időtartam:** 20 perc |
| **Idő összesítése:** Átlagos |
| **Művelet:** Növekedés száma 3 szerint |
| **Lehűlni (perc):** 120 |
|  |
| **Autoskálázási szabály (vertikális leskálázás)** |
| **Erőforrás:** 1. munkavégző készlet |
| **Metrika:** CPU |
| **Művelet:** Kevesebb mint 30% |
| **Időtartam:** 20 perc |
| **Idő összesítése:** Átlagos |
| **Művelet:** Csökkenés száma 3 szerint |
| **Lehűlni (perc):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
