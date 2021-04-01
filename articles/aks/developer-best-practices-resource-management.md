---
title: Erőforrás-kezelés – ajánlott eljárások
titleSuffix: Azure Kubernetes Service
description: Ismerje meg az Azure Kubernetes Service (ak) erőforrás-kezelésével kapcsolatos ajánlott eljárásokat az alkalmazások fejlesztéséhez
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 693cabac616dca8e108a2029c173a5e1b71c2695
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516738"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások az alkalmazások fejlesztői számára az erőforrások kezeléséhez az Azure Kubernetes szolgáltatásban (ak)

Az Azure Kubernetes szolgáltatásban (ak) futó alkalmazások fejlesztése és futtatása során néhány fontos területet figyelembe kell venni. Az alkalmazások központi telepítésének kezelése negatív hatással lehet az Ön által biztosított szolgáltatások végfelhasználói élményére. A sikeres működés érdekében érdemes figyelembe venni néhány ajánlott gyakorlatot, amelyet követve az AK-ban végezheti el az alkalmazások fejlesztését és futtatását.

Ez az ajánlott eljárás a fürt és a számítási feladatok alkalmazások fejlesztői perspektívából való futtatására koncentrál. További információ az ajánlott felügyeleti gyakorlatokról: a [cluster operátor ajánlott eljárásai az elkülönítéshez és az erőforrás-kezeléshez az Azure Kubernetes szolgáltatásban (ak)][operator-best-practices-isolation]. Ez a cikk a következőket ismerteti:

> [!div class="checklist"]
> * Mik a pod-erőforrásokra vonatkozó kérelmek és korlátozások
> * Alkalmazások fejlesztése és üzembe helyezése a Bridge használatával a Kubernetes és a Visual Studio Code segítségével
> * Az eszköz használata az üzemelő `kube-advisor` példányokkal kapcsolatos problémák kereséséhez

## <a name="define-pod-resource-requests-and-limits"></a>Pod-erőforrásokra vonatkozó kérelmek és korlátok meghatározása

**Ajánlott eljárási útmutató** – a pod-kérelmek és-korlátozások beállítása a YAML-jegyzékekben található összes hüvelyre. Ha az AK-fürt *erőforrás-kvótákat* használ, előfordulhat, hogy a rendszer elutasítja a telepítést, ha nem adja meg ezeket az értékeket.

A számítási erőforrások egy AK-fürtön belüli kezelésének elsődleges módja a pod-kérelmek és-korlátok használata. Ezek a kérelmek és korlátok lehetővé teszik, hogy a Kubernetes Scheduler tudja, milyen számítási erőforrásokat kell hozzárendelni a pod-hoz.

* A **Pod CPU-/memória-kérések** határozzák meg a processzor és a memória minimálisan szükséges mennyiségét.
    * Amikor a Kubernetes Scheduler egy Pod-t próbál elhelyezni egy csomóponton, a pod-kérelmek alapján megállapítható, hogy melyik csomópont rendelkezik elegendő erőforrással az ütemezéshez.
    * Ha nem állít be egy Pod-kérést, a rendszer alapértelmezés szerint a megadott korlátot állítja be.
    * Nagyon fontos az alkalmazás teljesítményének figyelése a kérések módosításához. Ha nem áll rendelkezésre elegendő Pod-erőforrásra vonatkozó kérelem, az alkalmazás a csomópont ütemezése miatt lecsökkentett teljesítményt kaphat. Ha a kérelmeket túlbecsülik, előfordulhat, hogy az alkalmazás az ütemezettnél nagyobb nehézségekbe ütközik.
* A **Pod CPU/memória korlátja** a hüvely által használható CPU és memória maximális mennyisége. A memória korlátai segítenek meghatározni, hogy mely hüvelyek legyenek leállítva a csomópont instabilitása miatt, mert nincs elegendő erőforrás. A megfelelő korlátok megadása nélkül a hüvelyek leállnak, amíg az erőforrás-nyomást fel nem emelik. Egy Pod lehet, hogy egy adott időtartamon belül nem lépheti túl a CPU-korlátot, de a rendszer nem fogja leölni a CPU-korlátot. 
    * A pod-korlátok segítenek meghatározni, hogy egy Pod elvesztette-e az erőforrás-felhasználást. Ha túllépi a korlátot, a pod prioritást élvez a csomópont állapotának fenntartása és a csomópontot megosztó hüvelyek hatásának csökkentése érdekében.
    * Ha nem állít be egy Pod-korlátot, a rendszer az adott csomópont legmagasabb rendelkezésre álló értékére állítja be.
    * Ne állítson be olyan Pod-korlátot, amelyik magasabb, mint a csomópontok támogatása. Az egyes AK-csomópontok az alapvető Kubernetes-összetevők számára fenntartott CPU és memória mennyiségét foglalják magukban. Előfordulhat, hogy az alkalmazás túl sok erőforrást próbál használni a csomóponton más hüvelyek sikeres futtatásához.
    * A nap vagy a hét folyamán a különböző időpontokban is figyelemmel kísérheti az alkalmazás teljesítményét. Határozza meg, hogy a maximális igény mikor legyen, és a hüvely korlátainak igazítása az alkalmazás maximális igényeinek kielégítéséhez szükséges erőforrásokhoz.

A pod-specifikációkban **ajánlott eljárás, és nagyon fontos** , hogy ezeket a kérelmeket és korlátokat a fenti információk alapján határozza meg. Ha nem adja meg ezeket az értékeket, a Kubernetes Scheduler nem tudja figyelembe venni azokat az erőforrásokat, amelyekre az alkalmazásoknak szüksége van az ütemezési döntések támogatásához.

Ha az ütemező egy olyan csomóponton helyez el egy Pod-t, amelynek nincs elegendő erőforrása, az alkalmazás teljesítménye csökken. Erősen ajánlott a fürt rendszergazdái számára olyan *erőforrás-kvóták* beállítása olyan névtéren, amelyekhez erőforrás-kérelmeket és korlátozásokat kell beállítani. További információ: erőforrás- [kvóták az AK-fürtökön][resource-quotas].

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

Ajánlott **eljárási útmutató** – a fejlesztési csapatoknak egy AK-fürtön kell üzembe helyezniük és hibakeresést végezniük a Bridge használatával a Kubernetes.

A Kubernetes híd használatával közvetlenül egy AK-fürtön fejlesztheti, hibakeresést végezhet és tesztelheti az alkalmazásokat. A csapaton belüli fejlesztők együttműködve készítenek és tesztelnek az alkalmazások életciklusa során. Továbbra is használhatja a meglévő eszközöket, például a Visual studiót vagy a Visual Studio Code-ot. A Bridge-hez olyan Kubernetes-bővítmény van telepítve, amely lehetővé teszi, hogy közvetlenül egy AK-fürtben fejlesszen.

Ez az integrált fejlesztési és tesztelési folyamat a Kubernetes-mel csökkenti a helyi tesztelési környezetek, például a [minikube][minikube]szükségességét. Ehelyett egy AK-fürtön fejlesztheti és tesztelheti. A fürt biztonságossá tétele és elkülönítése a fürt logikai elkülönítése érdekében a névterek használatáról szóló előző szakaszban látható.

A Kubernetes-hez készült híd a Linux-hüvelyeken és-csomópontokon futó alkalmazásokkal használható.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>A Visual Studio Code bővítmény használata a Kubernetes

**Ajánlott eljárási útmutató** – a Kubernetes vs Code-bővítmény telepítése és használata a YAML-jegyzékek írásakor. Használhatja a bővítményt is az integrált üzembe helyezési megoldáshoz, amely segíthet az alkalmazás tulajdonosai számára, hogy az AK-fürttel nem gyakran kommunikálnak.

A [Kubernetes készült Visual Studio Code-bővítmény][vscode-kubernetes] segíti az alkalmazások fejlesztését és üzembe helyezését az AK-ban. A bővítmény IntelliSense-t biztosít a Kubernetes-erőforrásokhoz, valamint a Helm-diagramokhoz és-sablonokhoz. A VS Code-ból a Kubernetes-erőforrások tallózásával, üzembe helyezésével és szerkesztésével is elvégezhető. A bővítmény egy IntelliSense-vizsgálatot is biztosít az erőforrás-kérelmekhez, illetve korlátokat állít be a pod-specifikációkban:

![A VS Code bővítmény a hiányzó Kubernetes kapcsolatos figyelmeztetésekről](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Az Kube-Advisor alkalmazással kapcsolatos problémák rendszeres keresése

**Ajánlott eljárások – útmutató** – rendszeresen futtassa a `kube-advisor` nyílt forráskódú eszköz legújabb verzióját a fürtben felmerülő problémák észlelése érdekében. Ha egy meglévő AK-fürthöz erőforrás-kvótát alkalmaz, `kube-advisor` először futtassa a parancsot az erőforrás-kérelmeket és korlátozásokat nem tartalmazó hüvelyek kereséséhez.

A [Kube-Advisor][kube-advisor] eszköz egy kapcsolódó, AK-beli nyílt forráskódú projekt, amely egy Kubernetes-fürtöt és a megtalált problémákkal kapcsolatos jelentéseket keres. Az egyik hasznos lehetőség az, hogy azonosítsa azokat a hüvelyeket, amelyek nem rendelkeznek erőforrás-kérelmekkel és korlátokkal.

Az Kube-Advisor eszköz jelentést készíthet az erőforrás-kérésekről, valamint a Windows-alkalmazások és a Linux-alkalmazások PodSpecs hiányzó korlátairól, de a Kube-Advisor eszköznek egy linuxos Pod-on kell ütemeznie. Egy Pod-t úgy ütemezhet, hogy egy adott operációs rendszert futtató csomópont-készleten fusson a pod konfigurációjában a [csomópont-választó][k8s-node-selector] használatával.

A sok fejlesztői csapatot és alkalmazást üzemeltető AK-fürtben nehéz lehet a hüvelyek nyomon követése az erőforrás-kérelmek és a korlátok beállítása nélkül. Ajánlott eljárásként rendszeresen futtasson `kube-advisor` az AK-fürtökön.

## <a name="next-steps"></a>Következő lépések

Ez az ajánlott eljárási cikk a fürt és a számítási feladatok fürtözési perspektívából való futtatására koncentrál. További információ az ajánlott felügyeleti gyakorlatokról: a [cluster operátor ajánlott eljárásai az elkülönítéshez és az erőforrás-kezeléshez az Azure Kubernetes szolgáltatásban (ak)][operator-best-practices-isolation].

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
