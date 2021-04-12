---
title: Erőforrás-kezelés – ajánlott eljárások
titleSuffix: Azure Kubernetes Service
description: Ismerje meg az Azure Kubernetes Service (ak) erőforrás-kezelésével kapcsolatos ajánlott eljárásokat az alkalmazások fejlesztéséhez
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 2cd2bab05346f66b933512e677f1d38f4514796c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105272"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások az alkalmazások fejlesztői számára az erőforrások kezeléséhez az Azure Kubernetes szolgáltatásban (ak)

Az Azure Kubernetes szolgáltatásban (ak) futó alkalmazások fejlesztése és futtatása során néhány fontos területet figyelembe kell venni. Az alkalmazások központi telepítésének kezelése negatív hatással lehet az Ön által biztosított szolgáltatások végfelhasználói élményére. A siker érdekében vegye figyelembe az egyes ajánlott eljárásokat, amelyeket az AK-beli alkalmazások fejlesztésekor és futtatásakor követheti el.

Ez a cikk a fürt és a számítási feladatok alkalmazások fejlesztői perspektívából történő futtatását ismerteti. További információ az ajánlott felügyeleti gyakorlatokról: a [cluster operátor ajánlott eljárásai az elkülönítéshez és az erőforrás-kezeléshez az Azure Kubernetes szolgáltatásban (ak)][operator-best-practices-isolation]. Ez a cikk a következőket ismerteti:

> [!div class="checklist"]
> * Pod-erőforrásra vonatkozó kérelmek és korlátozások.
> * Alkalmazások fejlesztése és üzembe helyezése a Bridge használatával a Kubernetes és a Visual Studio Code segítségével.
> * Az eszköz használata az üzemelő `kube-advisor` példányokkal kapcsolatos problémák kereséséhez.

## <a name="define-pod-resource-requests-and-limits"></a>Pod-erőforrásokra vonatkozó kérelmek és korlátok meghatározása

> **Útmutatás az ajánlott eljárásokhoz**
> 
> Állítsa be a pod-kérelmeket és-korlátozásokat a YAML-jegyzékekben található összes hüvelyre. Ha az AK-fürt *erőforrás-kvótákat* használ, és nem határozza meg ezeket az értékeket, előfordulhat, hogy a rendszer elutasítja a központi telepítést.

A pod-kérelmek és-korlátok használatával kezelheti a számítási erőforrásokat egy AK-fürtön belül. A pod-kérelmek és-korlátok tájékoztatják a Kubernetes schedulert, amely kiszámítja a pod-hoz hozzárendelni kívánt erőforrásokat.

### <a name="pod-cpumemory-requests"></a>Pod CPU/memória kérelmek
A *Pod-kérelmek* olyan CPU és memória mennyiségét határozzák meg, amelyet a hüvelynek rendszeresen szüksége van.

A pod-specifikációkban **ajánlott eljárás, és nagyon fontos** , hogy ezeket a kérelmeket és korlátokat a fenti információk alapján határozza meg. Ha nem adja meg ezeket az értékeket, a Kubernetes Scheduler nem tudja figyelembe venni azokat az erőforrásokat, amelyekre az alkalmazásoknak szüksége van az ütemezési döntések támogatásához.

Figyelje az alkalmazás teljesítményét a pod-kérések módosításához. 
* Ha alábecsüli a pod-kérelmeket, az alkalmazás a csomópontok feletti ütemezése miatt nem megfelelő teljesítményt kaphat. 
* Ha a kérelmeket túlbecsülik, előfordulhat, hogy az alkalmazás az ütemezettnél nagyobb nehézségekbe ütközik.

### <a name="pod-cpumemory-limits"></a>Pod CPU/memória korlátai * * 
A *Pod-korlátok* a pod által használható CPU és memória maximális mennyiségét határozzák meg. 

* A *memória korlátai* határozzák meg, hogy mely hüvelyek legyenek leállítva, ha a csomópontok instabillá válik, mert nincs elegendő erőforrás. A megfelelő korlátok megadása nélkül a rendszer leállítja a hüvelyeket, amíg az erőforrás-nyomást fel nem emeli. 
* Amíg egy Pod meghaladhatja a *CPU-korlátot* , a rendszer nem fogja leölni a CPU-korlátot. 

A pod-korlátok határozzák meg, hogy egy Pod elvesztette-e az erőforrás-felhasználást. Ha túllépi a korlátot, a rendszer megkeresi a pod jelölést. Ez a viselkedés fenntartja a csomópont állapotát, és kisebb mértékben befolyásolja a csomópontok megosztását. Ha nem állít be egy Pod-korlátot, a rendszer az adott csomópont legmagasabb rendelkezésre álló értékére állítja be.

Ne állítson be magasabb szintű Pod-korlátot, mint amennyit a csomópontok támogatnak. Az egyes AK-csomópontok az alapvető Kubernetes-összetevők számára fenntartott CPU és memória mennyiségét foglalják magukban. Előfordulhat, hogy az alkalmazás túl sok erőforrást próbál használni a csomóponton más hüvelyek sikeres futtatásához.

A nap vagy hét során különböző időpontokban figyelheti az alkalmazás teljesítményét. Határozza meg a maximális igény időpontját, és igazítsa a pod korlátozásokat a maximális igényeknek megfelelő erőforrásokhoz.

> [!IMPORTANT]
>
> A pod-specifikációkban ezeket a kérelmeket és korlátokat a fenti információk alapján határozza meg. Ha nem adja meg ezeket az értékeket, a Kubernetes ütemező nem teszi lehetővé az alkalmazások számára az ütemezési döntések támogatásához szükséges erőforrások könyvelését.

Ha az ütemező egy olyan csomóponton helyez el egy Pod-t, amelynek nincs elegendő erőforrása, az alkalmazás teljesítménye csökken. A fürt rendszergazdáinak olyan névtereken **kell** beállítaniuk az *erőforrás-kvótákat* , amelyekhez erőforrás-kérelmeket és korlátokat kell beállítani. További információ: erőforrás- [kvóták az AK-fürtökön][resource-quotas].

CPU-kérelem vagy-korlát meghatározásakor a rendszer a CPU-egységekben méri az értéket. 
* *1,0* a processzor egy mögöttes virtuális CPU-mag felel meg a csomóponton. 
    * A GPU-k ugyanazt a mérést használják.
* Megadhatja a millicores-ben mért törteket. Például *100m* egy mögöttes vCPU-mag *0,1* .

A következő, az egyetlen NGINX Pod-beli alapszintű példában a pod *100* millió CPU-időt és *128Mi* igényel. A pod erőforrás-korlátai a *250m* CPU és a *256Mi* memória beállítására vannak beállítva:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

További információ az erőforrás-mérésekről és a hozzárendelésekről: [számítási erőforrások kezelése a tárolók számára][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Alkalmazások fejlesztése és hibakeresése egy AK-fürtön

> **Útmutatás az ajánlott eljárásokhoz** 
>
> A fejlesztői csapatoknak egy AK-fürtön kell üzembe helyezniük és hibakeresést végezniük a Bridge használatával a Kubernetes.

A Kubernetes híd használatával közvetlenül egy AK-fürtön fejlesztheti, hibakeresést végezhet és tesztelheti az alkalmazásokat. A csapaton belüli fejlesztők az alkalmazás teljes életciklusán belül dolgozhatnak és tesztelnek. Továbbra is használhatja a meglévő eszközöket, például a Visual studiót vagy a Visual Studio Code-ot a Kubernetes bővítménnyel. 

Az integrált fejlesztési és tesztelési folyamat és a Kubernetes használatával csökkenti a helyi tesztelési környezetek, például a [minikube][minikube]szükségességét. Ehelyett fejlesztheti és tesztelheti egy AK-fürtön, akár biztonságos, akár elkülönített fürtökön. 

> [!NOTE]
> A Kubernetes-hez készült híd a Linux-hüvelyeken és-csomópontokon futó alkalmazásokkal használható.

## <a name="use-the-visual-studio-code-vs-code-extension-for-kubernetes"></a>A Visual Studio Code (VS Code) bővítmény használata a Kubernetes

> **Útmutatás az ajánlott eljárásokhoz** 
>
> YAML-jegyzékek írásakor telepítse és használja a VS Code bővítményt a Kubernetes. Használhatja a bővítményt is az integrált üzembe helyezési megoldáshoz, amely segíthet az alkalmazás tulajdonosai számára, hogy az AK-fürttel nem gyakran kommunikálnak.

A [Kubernetes készült Visual Studio Code-bővítmény][vscode-kubernetes] segíti az alkalmazások fejlesztését és üzembe helyezését az AK-ban. A bővítmény a következőket biztosítja:
* IntelliSense Kubernetes-erőforrásokhoz, Helm-diagramokhoz és-sablonokhoz. 
* A VS code-on belüli Kubernetes-erőforrások tallózása, üzembe helyezése és szerkesztése. 
* Az erőforrás-kérelmek vagy-korlátozások IntelliSense-ellenőrzését a pod-specifikációkban:

    ![A VS Code bővítmény a hiányzó Kubernetes kapcsolatos figyelmeztetésekről](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Az Kube-Advisor alkalmazással kapcsolatos problémák rendszeres keresése

> **Útmutatás az ajánlott eljárásokhoz** 
> 
> A `kube-advisor` fürt hibáinak észleléséhez rendszeresen futtassa a nyílt forráskódú eszköz legújabb verzióját. Az `kube-advisor` erőforrás-kvóták meglévő AK-fürtön való alkalmazása előtt futtassa a parancsot az erőforrás-kérelmeket és korlátozásokat nem tartalmazó hüvelyek megtalálásához.

A [Kube-Advisor][kube-advisor] eszköz egy olyan, AK-beli nyílt forráskódú projekt, amely egy Kubernetes-fürtöt és az azonosított problémákra vonatkozó jelentéseket keres. Az egyik hasznos vizsgálat az erőforrás-kérelmeket nem igénylő hüvelyek azonosítására és a korlátozásokra.

Amíg az `kube-advisor` eszköz jelentést tud készíteni a Windows-és Linux-alkalmazások PodSpecs kapcsolatos erőforrás-kérelmekről és-korlátozásokról, `kube-advisor` azt a Linux Pod-on kell ütemezni. A pod konfigurációjában a [Node választó][k8s-node-selector] használatával ütemezhet egy Pod-t egy adott operációs rendszert futtató csomópont-készleten.

A sok fejlesztői csapatot és alkalmazást üzemeltető AK-fürtben könnyebben nyomon követheti a hüvelyeket az erőforrás-kérelmek és a korlátozások használatával. Ajánlott eljárásként rendszeresen futtasson `kube-advisor` az AK-fürtökön.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatja, hogyan futtathatja a fürtöt és a munkaterheléseket a fürt operátorának szemszögéből. További információ az ajánlott felügyeleti gyakorlatokról: a [cluster operátor ajánlott eljárásai az elkülönítéshez és az erőforrás-kezeléshez az Azure Kubernetes szolgáltatásban (ak)][operator-best-practices-isolation].

Az ajánlott eljárások némelyikének megvalósításához tekintse meg a következő cikkeket:

* [Fejlesztés a Bridge és a Kubernetes között][btk]
* [Az Kube-Advisor problémáinak keresése][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
