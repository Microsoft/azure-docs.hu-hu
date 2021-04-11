---
title: Ajánlott eljárások az Azure Kubernetes Service (ak) szolgáltatáshoz
description: A Fürtfelügyelő és a fejlesztői ajánlott eljárások gyűjteménye alkalmazások létrehozásához és kezeléséhez az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 03/09/2021
ms.openlocfilehash: f4bd109ce6827de26b1649380b6f6a2ba7c10e17
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105901"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Fürtözési és fejlesztői ajánlott eljárások az Azure Kubernetes szolgáltatásban (ak) lévő alkalmazások létrehozásához és kezeléséhez

Az alkalmazások az Azure Kubernetes szolgáltatásban (ak) való sikeres kiépítése és futtatása néhány fontos szempont megismerését és megvalósítását igényli, beleértve a következőket:
* Több-bérlős és Scheduler-funkciók.
* A fürt és a pod biztonsága.
* Üzletmenet-folytonosság és vész-helyreállítás. 


Az AK termékcsoport, a mérnöki csapatok és a Field Teams (beleértve a globális fekete öveket [GBBs]) hozzájárult a következő ajánlott eljárások és fogalmak összefoglalásához. Céljuk, hogy segítsenek a fürtök és a fejlesztők számára a fenti megfontolások megismerésében és a megfelelő funkciók megvalósításában.


## <a name="cluster-operator-best-practices"></a>A cluster operátor ajánlott eljárásai

A fürt operátorai együttműködnek az alkalmazás-tulajdonosokkal és a fejlesztővel, hogy megértsék az igényeiket. Ezután az alábbi ajánlott eljárásokat követve igény szerint konfigurálhatja az AK-fürtöket.

**Több-bérlős üzemmód**

* [Ajánlott fürtelkülönítési eljárások](operator-best-practices-cluster-isolation.md)
    * A több-bérlős alapvető összetevőket és a névterek logikai elkülönítését foglalja magában.
* [Ajánlott eljárások a Scheduler alapszintű funkcióihoz](operator-best-practices-scheduler.md)
    * Az erőforrás-kvóták és a pod-megszakítási költségvetések használatát foglalja magában.
* [Ajánlott eljárások a Scheduler speciális funkcióihoz](operator-best-practices-advanced-scheduler.md)
    * Magában foglalja a szennyező elemek használatát és a tolerancia, a csomópont-választókat és az affinitást, valamint a több-Pod affinitást és az affinitást.
* [Ajánlott hitelesítési és engedélyezési eljárások](operator-best-practices-identity.md)
    * Magában foglalja a Azure Active Directory integrációját a Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) használatával, az Azure RBAC és a pod identitások használatával.

**Biztonság**

* [A fürtbiztonság és a frissítések ajánlott eljárásai](operator-best-practices-cluster-security.md)
    * Magában foglalja az API-kiszolgáló hozzáférésének biztosítását, a tárolók hozzáférésének korlátozását, valamint a frissítések és a csomópontok újraindításának kezelését.
* [Ajánlott eljárások a tárolók rendszerképének kezeléséhez és biztonságához](operator-best-practices-container-image-management.md)
    * Magában foglalja a rendszerkép és a futtatókörnyezetek, valamint az automatizált buildek biztonságossá tételét az alapszintű rendszerkép frissítésein.
* [Ajánlott eljárások a pod biztonsághoz](developer-best-practices-pod-security.md)
    * Magában foglalja az erőforrásokhoz való hozzáférés biztosítását, a hitelesítő adatokkal való kitettség korlátozását, valamint a pod-identitások és a digitális kulcstartók használatát.

**Hálózat és tárolás**

* [Ajánlott eljárások hálózati kapcsolathoz](operator-best-practices-network.md)
    * A különböző hálózati modelleket tartalmaz a bejövő és a webalkalmazási tűzfalakkal (WAF), valamint a Node SSH-hozzáférés biztosításával.
* [A tárolás és a biztonsági mentés ajánlott eljárásai](operator-best-practices-storage.md)
    * A tartalmazza a megfelelő tárolási típust és csomópont-méretet, a kötetek dinamikus kiosztását és az adatbiztonsági mentést.

**Nagyvállalati használatra kész számítási feladatok futtatása**

* [Ajánlott eljárások az üzletmenet folytonossága és a vész-helyreállítás terén](operator-best-practices-multi-region.md)
    * Többek között a régió párokat, az Azure Traffic Manager több fürtöt, valamint a tároló lemezképek földrajzi replikálását is magában foglalja.

## <a name="developer-best-practices"></a>Fejlesztői ajánlott eljárások

Fejlesztőként vagy alkalmazás-tulajdonosként egyszerűsítheti a fejlesztési élményt, és meghatározhatja az alkalmazások teljesítményének megkövetelését.

* [Ajánlott erőforrás-kezelési eljárások alkalmazásfejlesztők számára](developer-best-practices-resource-management.md)
    * Magában foglalja a pod-erőforrásokra vonatkozó kérelmek és korlátok definiálását, a fejlesztői eszközök konfigurálását és az alkalmazással kapcsolatos problémák ellenőrzését.
* [Ajánlott eljárások a pod biztonsághoz](developer-best-practices-pod-security.md)
    * Magában foglalja az erőforrásokhoz való hozzáférés biztosítását, a hitelesítő adatokkal való kitettség korlátozását, valamint a pod-identitások és a digitális kulcstartók használatát.

## <a name="kubernetes--aks-concepts"></a>Kubernetes/AK-fogalmak

Az ajánlott eljárások egyes funkcióinak és összetevőinek megismeréséhez a következő fogalmi cikkek is megtekinthetők az Azure Kubernetes szolgáltatásban (ak) található fürtökhöz:

* [A Kubernetes alapvető fogalmai](concepts-clusters-workloads.md)
* [Hozzáférés és identitás](concepts-identity.md)
* [Biztonsági alapelvek](concepts-security.md)
* [Hálózati fogalmak](concepts-network.md)
* [Tárolási lehetőségek](concepts-storage.md)
* [Skálázási beállítások](concepts-scale.md)

## <a name="next-steps"></a>Következő lépések

Ha meg kell kezdenie az AK-t, kövesse az egyik rövid útmutatót, amely az [Azure CLI](kubernetes-walkthrough.md) vagy a [Azure Portal](kubernetes-walkthrough-portal.md)használatával helyez üzembe egy Azure KUBERNETES-szolgáltatási (ak-) fürtöt.
