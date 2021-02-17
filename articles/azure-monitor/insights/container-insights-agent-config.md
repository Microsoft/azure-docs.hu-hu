---
title: Azure Monitor konfigurálása a containers Agent adatgyűjtéshez | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható a Azure Monitor for containers Agent az stdout/stderr és a környezeti változók naplózási gyűjteményének vezérléséhez.
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: f21b841bc129012b684d2a1c59eb72989fe9e0e0
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561727"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Ügynökadatok gyűjtésének konfigurálása a tárolókhoz készült Azure Monitorban

A tárolók Azure Monitor az stdout, a stderr és a környezeti változókat gyűjtik a felügyelt Kubernetes-fürtökre telepített tároló-munkaterhelésekről a tároló ügynökből. Az ügynök adatgyűjtési beállításainak konfigurálásához létrehozhat egy egyéni Kubernetes-ConfigMaps a felhasználói élmény szabályozása érdekében. 

Ez a cikk bemutatja, hogyan hozhat létre ConfigMap, és hogyan konfigurálhatja az adatgyűjtést a követelmények alapján.

>[!NOTE]
>Az Azure Red Hat OpenShift esetében a sablon ConfigMap-fájlját a *OpenShift-Azure-Logging* névtérben hozza létre a rendszer. 
>

## <a name="configmap-file-settings-overview"></a>A ConfigMap-fájlok beállításai – áttekintés

A sablon ConfigMap fájlja lehetővé teszi, hogy egyszerűen szerkessze a testreszabásokkal anélkül, hogy teljesen létre kellene hoznia. A Kezdés előtt tekintse át a [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) Kubernetes dokumentációját, és ismerkedjen meg a ConfigMaps létrehozásával, konfigurálásával és üzembe helyezésével. Ez lehetővé teszi a stderr és az stdout szűrését a névtérben vagy a teljes fürtön, valamint a fürtben található összes hüvely/csomóponton futó tároló környezeti változóit.

>[!IMPORTANT]
>Az ügynök minimális verziója támogatja az stdout, a stderr és a környezeti változók begyűjtését a tároló munkaterhelésekről ciprod06142019 vagy újabb verzióra. Az ügynök verziójának ellenőrzéséhez a **csomópont** lapon válasszon ki egy csomópontot, majd a Tulajdonságok panelen jegyezze fel az **ügynök Képcímke** tulajdonságának értékét. További információ az ügynök verziójáról és az egyes kiadásokról: [ügynök kibocsátási megjegyzései](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Adatgyűjtés beállításai

A következő táblázat ismerteti az adatgyűjtés vezérléséhez beállítható beállításokat:

| Kulcs | Adattípus | Érték | Leírás |
|--|--|--|--|
| `schema-version` | Karakterlánc (megkülönbözteti a kis-és nagybetűket) | v1 | Ez az ügynök által használt séma-verzió<br> a ConfigMap elemzésekor.<br> A jelenleg támogatott séma verziója v1.<br> Az érték módosítása nem támogatott, és a következő lesz<br> a rendszer elutasította a ConfigMap kiértékelését. |
| `config-version` | Sztring |  | A támogatja a konfigurációs fájl verziószámának nyomon követését a verziókövetés rendszerében/adattárában.<br> A megengedett karakterek maximális száma 10, az összes többi karakter pedig csonkolt. |
| `[log_collection_settings.stdout] enabled =` | Logikai | true (igaz) vagy false (hamis) | Ez szabályozza, ha az stdout-tároló naplójának gyűjteménye engedélyezve van. Ha a értékre van állítva, `true` és a rendszer nem zárja ki a névtereket az stdout log-gyűjteményhez<br> ( `log_collection_settings.stdout.exclude_namespaces` az alábbi beállításnál) az stdout-naplókat a rendszer az összes tárolóból gyűjti össze a fürt összes hüvelye/csomópontja között. Ha nincs megadva a ConfigMaps-ben,<br> az alapértelmezett érték: `enabled = true` . |
| `[log_collection_settings.stdout] exclude_namespaces =` | Sztring | Vesszővel tagolt tömb | Azon Kubernetes-névterek tömbje, amelyek esetében a rendszer nem gyűjti az stdout-naplókat. Ez a beállítás csak akkor érvényes, ha<br> `log_collection_settings.stdout.enabled`<br> értékre van állítva `true` .<br> Ha nincs megadva a ConfigMap-ben, az alapértelmezett érték a<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.stderr] enabled =` | Logikai | true (igaz) vagy false (hamis) | Ez szabályozza, hogy engedélyezve van-e a stderr-tároló naplójának gyűjtése.<br> Ha a értékre van állítva, `true` és a rendszer nem zárja ki a névtereket az stdout log-gyűjteményhez<br> ( `log_collection_settings.stderr.exclude_namespaces` beállítás) a rendszer az összes tárolóból gyűjti össze a stderr-naplókat a fürt összes hüvelye/csomópontja között.<br> Ha nincs megadva a ConfigMaps-ben, az alapértelmezett érték a<br> `enabled = true`. |
| `[log_collection_settings.stderr] exclude_namespaces =` | Sztring | Vesszővel tagolt tömb | Azon Kubernetes-névterek tömbje, amelyek esetében a rendszer nem gyűjti össze a stderr-naplókat.<br> Ez a beállítás csak akkor érvényes, ha<br> `log_collection_settings.stdout.enabled` értékre van állítva `true` .<br> Ha nincs megadva a ConfigMap-ben, az alapértelmezett érték a<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` | Logikai | true (igaz) vagy false (hamis) | Ezzel a beállítással szabályozható a környezeti változók gyűjteménye<br> a fürt összes hüvelye/csomópontja között<br> és alapértelmezett értéke, `enabled = true` Ha nincs megadva<br> a ConfigMaps.<br> Ha a környezeti változók gyűjteménye globálisan engedélyezve van, letilthatja egy adott tárolóhoz<br> a környezeti változó beállításával<br> `AZMON_COLLECT_ENV`Ha a Docker vagy az **env:** szakaszban található [Pod konfigurációs fájljában](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) **Hamis értéket** szeretne beállítani.<br> Ha a környezeti változók gyűjteménye globálisan le van tiltva, akkor a gyűjtemény nem engedélyezhető egy adott tárolónál (azaz a tároló szintjén alkalmazható egyetlen felülbírálás a gyűjtemény letiltása, ha az már engedélyezve van a globálisan.). |
| `[log_collection_settings.enrich_container_logs] enabled =` | Logikai | true (igaz) vagy false (hamis) | Ezzel a beállítással szabályozható a tároló naplójának bővítése a név és a rendszerkép tulajdonságértékek feltöltéséhez.<br> minden, a fürtben található ContainerLog táblázatba írt naplóbeli rekord esetében.<br> Alapértelmezés szerint a `enabled = false` ConfigMap-ben nincs megadva. |
| `[log_collection_settings.collect_all_kube_events]` | Logikai | true (igaz) vagy false (hamis) | Ez a beállítás lehetővé teszi az összes típusú Kube-események gyűjtését.<br> Alapértelmezés szerint a rendszer nem gyűjti a *normál* típusú Kube eseményeket. Ha ez a beállítás be van állítva `true` , a *normál* eseményeket a rendszer már nem szűri, és az összes eseményt összegyűjti.<br> Alapértelmezés szerint ez a következőre van beállítva: `false` . |

### <a name="metric-collection-settings"></a>Metrika-gyűjtemény beállításai

A következő táblázat ismerteti a metrika-gyűjtemények vezérléséhez konfigurálható beállításokat:

| Kulcs | Adattípus | Érték | Leírás |
|--|--|--|--|
| `[metric_collection_settings.collect_kube_system_pv_metrics] enabled =` | Logikai | true (igaz) vagy false (hamis) | Ez a beállítás lehetővé teszi az állandó kötetek (PV) használati metrikáinak gyűjtését a Kube-System névtérben. Alapértelmezés szerint a rendszer nem gyűjti össze a Kube-rendszernévtérben lévő állandó kötetekkel rendelkező állandó kötetek használati metrikáit. Ha ez a beállítás be van állítva, a rendszer az `true` összes névtérhez tartozó PV használati metrikákat gyűjti. Alapértelmezés szerint ez a következőre van beállítva: `false` . |

A ConfigMaps egy globális lista, és csak egy ConfigMap alkalmazható az ügynökre. A gyűjtemények nem rendelkezhetnek más ConfigMaps.

## <a name="configure-and-deploy-configmaps"></a>A ConfigMaps konfigurálása és telepítése

A következő lépések végrehajtásával konfigurálja és telepítheti a ConfigMap konfigurációs fájlját a fürtön.

1. Töltse le a [sablon CONFIGMAP YAML fájlt](https://aka.ms/container-azm-ms-agentconfig) , és mentse azt tároló-Keresztesné Gréczi Ágnes-MS-agentconfig. YAML néven. 

   > [!NOTE]
   > Ez a lépés nem szükséges az Azure Red Hat OpenShift használata esetén, mert a ConfigMap-sablon már létezik a fürtön.

2. Szerkessze a ConfigMap YAML-fájlt a testreszabásokkal az stdout, a stderr és/vagy a környezeti változók összegyűjtéséhez. Ha a ConfigMap YAML-fájlt szerkeszti az Azure Red Hat OpenShift, először futtassa a parancsot a `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` fájl megnyitásához egy szövegszerkesztőben.

    - Ha ki szeretné zárni az stdout log Collection adott névtereit, a kulcs/érték konfigurálásához a következő példát kell használni: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]` .
    
    - A környezeti változók egy adott tárolóhoz való letiltásához állítsa be a kulcs/érték beállítást a `[log_collection_settings.env_var] enabled = true` változó globális begyűjtésének engedélyezéséhez, majd kövesse az [itt](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) leírt lépéseket az adott tároló konfigurációjának befejezéséhez.
    
    - A stderr-naplók fürtre kiterjedő letiltásához konfigurálja a kulcsot/értéket a következő példa használatával: `[log_collection_settings.stderr] enabled = false` .

3. Az Azure Red Hat OpenShift eltérő fürtök esetén a következő kubectl parancs futtatásával hozza létre a ConfigMap: az `kubectl apply -f <configmap_yaml_file.yaml>` Azure Red Hat OpenShift kívüli fürtökön. 
    
    Példa: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Az Azure Red Hat OpenShift mentse a módosításokat a szerkesztőben.

A konfiguráció módosítása több percet is igénybe vehet, mielőtt érvénybe lépnek, és a fürtben lévő összes omsagent-hüvely újra fog indulni. Az újraindítás az összes omsagent-hüvely működés közbeni újraindítása, és nem minden újraindítási idő. Az újraindítások végeztével megjelenik egy üzenet, amely az alábbihoz hasonló, és az eredményt tartalmazza: `configmap "container-azm-ms-agentconfig" created` .

## <a name="verify-configuration"></a>Konfiguráció ellenőrzése

Annak ellenőrzéséhez, hogy a konfiguráció sikeresen alkalmazva lett-e az Azure Red Hat OpenShift eltérő fürtre, a következő paranccsal tekintheti át a naplókat egy ügynök pod: `kubectl logs omsagent-fdf58 -n kube-system` . Ha konfigurációs hibák vannak a omsagent hüvelyből, a kimenet az alábbihoz hasonló hibákat jelenít meg:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

A konfigurációs módosítások alkalmazásával kapcsolatos hibák a felülvizsgálathoz is elérhetők. A konfigurációs változások további hibaelhárításához a következő lehetőségek érhetők el:

- Egy ügynök Pod-naplóiból ugyanazzal a `kubectl logs` paranccsal. 

    >[!NOTE]
    >Ez a parancs nem alkalmazható az Azure Red Hat OpenShift-fürtre.
    > 

- Élő naplókból. Az élő naplók az alábbihoz hasonló hibákat mutatnak:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- A Log Analytics munkaterület **KubeMonAgentEvents** táblájában. Az adatküldés óránként, a konfigurációs *hibák súlyossága miatt.* Ha nincsenek hibák, a táblázatban szereplő *bejegyzés súlyossági adatokkal* fog rendelkezni, ami nem jelent hibát. A **címkék** tulajdonság további információkat tartalmaz a pod és a Container azonosítóról, amelyen a hiba történt, valamint az első előfordulást, az utolsó előfordulást és a számlálást is az elmúlt órában.

- Az Azure Red Hat OpenShift ellenőrizze a omsagent naplóit, ha a **ContainerLog** táblában ellenőrzi, hogy a OpenShift naplózási gyűjteménye engedélyezve van-e.

Miután kijavította a hibát (ka) t az Azure Red Hat OpenShift eltérő fürtökön lévő ConfigMap, mentse a YAML-fájlt, és alkalmazza a frissített ConfigMaps a következő parancs futtatásával: `kubectl apply -f <configmap_yaml_file.yaml` . Az Azure Red Hat OpenShift szerkessze és mentse a frissített ConfigMaps a következő parancs futtatásával:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Frissített ConfigMap alkalmazása

Ha már telepített egy ConfigMap az Azure Red Hat OpenShift eltérő fürtökön, és egy újabb konfigurációval szeretné frissíteni, akkor szerkesztheti a korábban használt ConfigMap-fájlt, majd alkalmazhatja ugyanazt a parancsot, mint korábban `kubectl apply -f <configmap_yaml_file.yaml` . Az Azure Red Hat OpenShift szerkessze és mentse a frissített ConfigMaps a következő parancs futtatásával:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

A konfiguráció módosítása több percet is igénybe vehet, mielőtt érvénybe lépnek, és a fürtben lévő összes omsagent-hüvely újra fog indulni. Az újraindítás az összes omsagent-hüvely működés közbeni újraindítása, és nem minden újraindítási idő. Az újraindítások végeztével megjelenik egy üzenet, amely az alábbihoz hasonló, és az eredményt tartalmazza: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verifying-schema-version"></a>Séma verziójának ellenőrzése

A konfigurációs sémák támogatott verziói a omsagent Pod-on található Pod megjegyzésként (Schema-Versions) érhetők el. Ezeket a következő kubectl-paranccsal tekintheti meg: `kubectl describe pod omsagent-fdf58 -n=kube-system`

A kimenet az alábbihoz hasonlóan fog megjelenni a Megjegyzés sémája – verziók:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Következő lépések

- A tárolók Azure Monitor nem tartalmaz előre meghatározott riasztásokat. Tekintse át a [teljesítménnyel kapcsolatos riasztások létrehozása a Azure monitor for containers](./container-insights-log-alerts.md) szolgáltatással című témakört, amelyből megtudhatja, hogyan hozhat létre ajánlott riasztásokat magas CPU-és memóriahasználat esetén a DevOps vagy működési folyamatok és eljárások támogatásához

- A figyelés lehetővé teszi, hogy az AK-beli vagy hibrid fürtök és a rajtuk futó munkaterhelések állapotának és erőforrás-felhasználásának összegyűjtésével [hogyan használhatók](container-insights-analyze.md) a Azure monitor a tárolók számára.

- Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a fürtök riasztásának, megjelenítésének vagy elemzésének kiértékeléséhez és testreszabásához.