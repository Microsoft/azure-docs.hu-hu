---
title: Kezelői ajánlott eljárások – az Azure Kubernetes Services tároló-rendszerkép kezelése
description: Ismerje meg az Azure Kubernetes Service-ben (ak) található tároló-lemezképek felügyeletére és védelmére vonatkozó ajánlott eljárásokat.
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 1d2f5465356a94b9ad7014e75aa6fe1515411a81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564917"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a tárolók rendszerképének kezeléséhez és biztonságához az Azure Kubernetes szolgáltatásban (ak)

Az Azure Kubernetes szolgáltatásban (ak) lévő alkalmazások fejlesztése és futtatása során kulcsfontosságú szempont a tárolók és a tárolók rendszerképeinek biztonsága. A elavult alapképeket vagy a nem javított alkalmazások futtatókörnyezetét tartalmazó tárolók biztonsági kockázatot és lehetséges támadási vektort tartalmaznak. A kockázatok csökkentése érdekében olyan eszközöket kell integrálni, amelyek a tárolókban lévő problémákat a kiépítési és futtatókörnyezeti szolgáltatásokban keresik és szervizelik. A folyamat korábbi részében a biztonsági rést vagy az elavult alaprendszerképet fogták ki, annál biztonságosabb a fürt. Ebben a cikkben a *tárolók* a tároló-beállításjegyzékben tárolt tároló-lemezképeket és a futó tárolókat jelentik.

Ebből a cikkből megtudhatja, hogyan védheti meg a tárolókat az AK-ban. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Képbiztonsági rések keresése és szervizelése
> * Tároló-lemezképek automatikus elindítása és újbóli üzembe helyezése alaprendszerkép frissítésekor

Emellett elolvashatja a [fürt biztonságával][best-practices-cluster-security] és a [Pod biztonsággal][best-practices-pod-security]kapcsolatos ajánlott eljárásokat is.

A [tárolók biztonsága Security Center][security-center-containers] is használható a tárolók biztonsági rések vizsgálatához.  Az Security Center- [integrációval is Azure Container Registry][security-center-acr] , hogy megvédje a lemezképeket és a beállításjegyzéket a biztonsági rések ellen.

## <a name="secure-the-images-and-run-time"></a>A képek és a futtatási idő védelme

**Ajánlott eljárási útmutató** – a biztonsági rések beolvasása a tárolók rendszerképein, és csak az ellenőrzésen átadott lemezképek telepítése. Rendszeresen frissítse az alaplemezképeket és az alkalmazás-futtatókörnyezetet, majd telepítse újra a számítási feladatokat az AK-fürtben.

A tároló-alapú számítási feladatok bevezetésének egyik aggálya a saját alkalmazások létrehozásához használt lemezképek és futtatókörnyezet biztonságának ellenőrzése. Hogyan gondoskodik arról, hogy ne vezessen be biztonsági réseket a központi telepítések során? Az üzembe helyezési munkafolyamatnak tartalmaznia kell egy olyan folyamatot, amely a [Twistlock][twistlock] vagy az [Aqua][aqua]használatával olyan eszközökkel vizsgálja meg a tárolók lemezképeit, amelyek csak az ellenőrzött lemezképek telepítését teszik lehetővé.

![A tároló lemezképének vizsgálata és szervizelése, érvényesítése és üzembe helyezése](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Egy valós példában a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD) folyamat segítségével automatizálhatja a képek vizsgálatait, ellenőrzését és központi telepítését. Azure Container Registry a biztonsági rések vizsgálatának funkcióit is tartalmazza.

## <a name="automatically-build-new-images-on-base-image-update"></a>Új rendszerképek automatikus létrehozása az alaprendszerkép frissítésével

**Ajánlott eljárási útmutató** – az alaplemezképek alkalmazás-lemezképekhez való használatakor az Automation használatával új rendszerképeket hozhat létre az alaprendszerkép frissítésekor. Mivel az alaplemezképek általában biztonsági javításokat tartalmaznak, frissítheti az alsóbb rétegbeli alkalmazás-tárolók lemezképeit.

Minden alkalommal, amikor egy alaprendszerkép frissül, minden alsóbb rétegbeli tároló lemezképét is frissíteni kell. Ezt az összeállítási folyamatot integrálni kell az ellenőrzési és üzembe helyezési folyamatokkal, például az [Azure][azure-pipelines] -folyamatokba vagy a jenkinsbe. Ezek a folyamatok gondoskodnak arról, hogy az alkalmazások továbbra is fussanak a frissített alapú rendszerképeken. Az alkalmazás-tároló lemezképének ellenőrzése után az AK-beli központi telepítések a legújabb, biztonságos rendszerképek futtatására is frissíthetők.

Azure Container Registry a feladatok a lemezkép frissítésekor is automatikusan frissíthetik a tároló lemezképeit. Ez a funkció lehetővé teszi, hogy kis számú alaplemezképet építsen ki, és rendszeresen frissítse őket a hibák és a biztonsági javítások használatával.

További információ az alapszintű rendszerkép frissítéseiről: [a rendszerkép-buildek automatizálása az alapszintű rendszerkép frissítése Azure Container Registry feladatokkal][acr-base-image-update].

## <a name="next-steps"></a>Következő lépések

Ez a cikk a tárolók biztonságossá tételére koncentrál. Ezen területek némelyikének megvalósításához tekintse meg a következő cikkeket:

* [Rendszerkép-buildek automatizálása Azure Container Registry feladatokkal az alapszintű rendszerkép frissítése során][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/defender-for-container-registries-introduction.md