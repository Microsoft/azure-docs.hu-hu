---
title: Az Azure Spring Cloud bemutatása
description: Ismerje meg az Azure Spring Cloud funkcióit és előnyeit a Java Spring-alkalmazások üzembe helyezéséhez és kezeléséhez az Azure-ban.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 12/02/2020
ms.author: brendm
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: b7f5d4206140bf2101c10b1cd4ac46d80bdd3342
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881703"
---
# <a name="what-is-azure-spring-cloud"></a>Mi az az Azure Spring Cloud?

Az Azure Spring Cloud megkönnyíti a Spring boot Service-alkalmazások Azure-beli üzembe helyezését a kód módosítása nélkül.  A szolgáltatás felügyeli a Spring Cloud Applications infrastruktúráját, így a fejlesztők a kódra is koncentrálhat.  Az Azure Spring Cloud átfogó monitorozási és diagnosztikai, konfigurálási, szolgáltatás-felderítési, CI/CD-integrációs, kék-zöld üzembe helyezési és egyéb funkciókkal biztosítja az életciklus-kezelést.

## <a name="why-use-azure-spring-cloud"></a>Miért érdemes az Azure Spring Cloud-ot használni?

Az alkalmazások Azure Spring Cloud-ba való üzembe helyezése számos előnnyel jár.  A következőket teheti:
* A meglévő tavaszi alkalmazások hatékony migrálása és a felhő skálázásának és költségeinek kezelése.
* A Spring Cloud Patterns segítségével modernizálhatja alkalmazásait, így javíthatja a gyorsaságot és a gyorsabb kézbesítést.
* Futtasson Java-t a felhőben, és végezzen magasabb szintű használatot bonyolult infrastruktúra nélkül.
* Gyorsan fejlesztheti és helyezheti üzembe a tárolókra bontás-függőségek nélkül.
* Hatékonyan és könnyedén figyelheti a termelési számítási feladatokat.

Az Azure Spring Cloud a Java [Spring boot](https://spring.io/projects/spring-boot) és a ASP.net Core [Steeltoe](https://steeltoe.io/) -alkalmazásokat egyaránt támogatja. A Steeltoe-támogatás jelenleg nyilvános előzetes verzióként érhető el. A nyilvános előzetes verzióban elérhető ajánlatok lehetővé teszik, hogy a hivatalos kiadás előtt új funkciókkal kísérletezzenek. A nyilvános előzetes verzió funkcióit és szolgáltatásait nem éles használatra szánták. További információ: a [Gyakori kérdések](https://azure.microsoft.com/support/faq/) vagy a [support Request](../azure-portal/supportability/how-to-create-azure-support-request.md)fájl.

## <a name="service-overview"></a>A szolgáltatás áttekintése

Az Azure-ökoszisztéma részeként az Azure Spring Cloud egyszerű kötést tesz lehetővé más Azure-szolgáltatásokhoz, többek között a tároláshoz, az adatbázisokhoz, a figyeléshez és egyebekhez.  

  ![Az Azure Spring Cloud áttekintése](media/spring-cloud-principles/azure-spring-cloud-overview.png)

* Az Azure Spring Cloud egy teljes körűen felügyelt szolgáltatás a Spring boot-alkalmazások számára, amely lehetővé teszi az alkalmazások kiépítését és futtatását az infrastruktúra kezelése nélkül.

* Egyszerűen üzembe helyezheti az üvegeket vagy a kódokat, az Azure Spring Cloud pedig automatikusan az alkalmazásait a Spring Service Runtime és a beépített alkalmazások életciklusával fogja csatlakoztatni.

* A figyelés egyszerű. Az üzembe helyezés után nyomon követheti az alkalmazások teljesítményét, javíthatja a hibákat, és gyorsan fejlesztheti az alkalmazásokat. 

* Teljes körű integráció az Azure-beli ökoszisztémákkal és szolgáltatásokkal.

* Az Azure Spring Cloud nagyvállalati használatra kész, teljes körűen felügyelt infrastruktúrával, beépített életciklus-felügyelettel és egyszerű figyeléssel.

## <a name="documentation-overview"></a>Dokumentáció – áttekintés
Ez a dokumentáció azokat a szakaszokat ismerteti, amelyekkel megtudhatja, hogyan kezdheti el az Azure Spring Cloud Services használatát.

* Bevezetés
    * [Az első alkalmazás elindítása](spring-cloud-quickstart.md)
    * [Azure Spring Cloud-szolgáltatás kiépítése](spring-cloud-quickstart-provision-service-instance.md)
    * [A konfigurációs kiszolgáló beállítása]()
    * [Alkalmazások készítése és üzembe helyezése](spring-cloud-quickstart-deploy-apps.md)
    * [Naplók metrikáinak és nyomkövetésének használata](spring-cloud-quickstart-logs-metrics-tracing.md)
* Használati útmutató
    * [Fejlesztés](spring-cloud-tutorial-prepare-app-deployment.md): meglévő Java Spring-alkalmazás előkészítése az Azure Spring Cloud üzembe helyezéséhez. Ha megfelelően van konfigurálva, az Azure Spring Cloud robusztus szolgáltatásokat biztosít a Java Spring Cloud-alkalmazások monitorozásához, méretezéséhez és frissítéséhez.
    * [Üzembe helyezés](spring-cloud-howto-staging-environment.md): átmeneti üzembe helyezés beállítása a kék-zöld üzembe helyezési minta használatával az Azure Spring Cloud-ban. A kék/zöld üzembe helyezés egy Azure DevOps folyamatos kézbesítési minta, amelynek lényege, hogy működésben tart egy meglévő (kék) verziót, miközben üzembe helyez egy új (zöld) verziót.
    * [Alkalmazások konfigurálása](spring-cloud-howto-start-stop-delete.md): az Azure Spring Cloud-alkalmazások elindítása, leállítása és törlése. Egy alkalmazás állapotának módosítása az Azure Spring Cloud-ban az Azure Portal vagy az Azure CLI használatával.
    * [Méretezés](spring-cloud-tutorial-scale-manual.md): bármely Service-alkalmazás méretezése az Azure Spring Cloud irányítópulton a Azure Portalon vagy az autoskálázási beállítások használatával. A nyilvános IP-címek a külső erőforrásokkal, például adatbázisokkal, tárolókkal és kulcstartókkal való kommunikációhoz érhetők el.
    * [Alkalmazások figyelése](spring-cloud-tutorial-distributed-tracing.md): az elosztott nyomkövetési eszközök megkönnyítik az összetett problémák hibakeresését és figyelését. Az Azure Spring Cloud egyesíti a Spring Cloud Sleuth az Azure Application Insightsával. Ez az integráció hatékony elosztott nyomkövetési képességet biztosít a Azure Portal.
    * [Biztonságos alkalmazások](spring-cloud-howto-enable-system-assigned-managed-identity.md): az Azure-erőforrások automatikusan felügyelt identitást biztosítanak Azure Active Directoryban. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban.
    * [Integráció más Azure-szolgáltatásokkal](spring-cloud-tutorial-bind-cosmos.md): a Spring boot-alkalmazások manuális konfigurálása helyett automatikusan köthető a kiválasztott Azure-szolgáltatások az alkalmazásokhoz, például az alkalmazás kötése egy Azure Cosmos db-adatbázishoz.
    * [Automatizálás](spring-cloud-howto-cicd.md): a folyamatos integráció és a folyamatos kézbesítési eszközök lehetővé teszik a meglévő alkalmazások frissítéseinek gyors üzembe helyezését minimális erőfeszítéssel és kockázattal. Az Azure DevOps segítségével rendszerezheti és szabályozhatja ezeket a kulcsfontosságú feladatokat. 
    * [Problémamegoldás](spring-cloud-howto-self-diagnose-solve.md): az Azure Spring Cloud Diagnostics interaktív élményt nyújt az alkalmazások hibakereséséhez. Nem igényel konfigurálást. Ha problémákat tapasztal, az Azure Spring Cloud Diagnostics észleli a problémákat, és útmutatást nyújt a problémák elhárításához és megoldásához.
    * [Migrálás](/azure/developer/java/migration/migrate-spring-boot-to-azure-spring-cloud): meglévő Spring Cloud Application vagy Spring boot-alkalmazás migrálása az Azure Spring Cloud-on való futtatáshoz.

## <a name="next-steps"></a>Következő lépések

Első lépésként fejezze be a [Spring Cloud](spring-cloud-quickstart.md) gyors útmutatóját

A minták a GitHubon érhetők el: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).