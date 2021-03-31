---
title: Saját üzemeltetésű átjáró üzembe helyezése a Kubernetes-ben | Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe az Azure API Management saját üzemeltetésű átjáró-összetevőjét a Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 023c2c89b90d6ddc71abc95db325dcdeb7684a2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89500130"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>Saját üzemeltetésű átjáró üzembe helyezése Kubernetesben

Ez a cikk az Azure-API Management saját üzemeltetésű átjáró-összetevőjének Kubernetes-fürtre történő telepítésének lépéseit ismerteti.

## <a name="prerequisites"></a>Előfeltételek

- Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
- Hozzon létre egy Kubernetes-fürtöt.
   > [!TIP]
   > Az [egycsomópontos fürtök](https://kubernetes.io/docs/setup/#learning-environment) fejlesztési és értékelési célokra is jól működnek. A [Kubernetes-tanúsítvánnyal rendelkező](https://kubernetes.io/partners/#conformance) többcsomópontos fürtöket helyszíni vagy Felhőbeli környezetben is használhatja éles számítási feladatokhoz.
- [Saját üzemeltetésű átjáró-erőforrás kiépítése a API Management-példányban](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Üzembe helyezés a Kubernetesben

1. Válassza az **átjárók** lehetőséget az **üzembe helyezés és az infrastruktúra** területen.
2. Válassza ki azt a saját üzemeltetésű átjáró-erőforrást, amelyet központilag telepíteni szeretne.
3. Válassza a **telepítés** lehetőséget.
4. A **jogkivonat** szövegmezőben lévő hozzáférési jogkivonat automatikusan lett létrehozva az alapértelmezett **lejárati** és **titkos kulcs** értékei alapján. Ha szükséges, válasszon értékeket mindkét vezérlőelemben egy új jogkivonat létrehozásához.
5. Válassza a **Kubernetes** lapot a **telepítési parancsfájlok** területen.
6. Válassza a **\<gateway-name\> . YML** fájl hivatkozást, és töltse le a YAML fájlt.
7. Válassza a **Másolás** ikont a **telepítés** szövegmező jobb alsó sarkában, és mentse a `kubectl` parancsokat a vágólapra.
8. Parancsok beillesztése a terminál (vagy parancs) ablakba. Az első parancs létrehoz egy titkos Kubernetes, amely a 4. lépésben létrehozott hozzáférési jogkivonatot tartalmazza. A második parancs a 6. lépésben letöltött konfigurációs fájlt alkalmazza a Kubernetes-fürtre, és elvárja, hogy a fájl az aktuális könyvtárban legyen.
9. Futtassa a parancsokat a szükséges Kubernetes-objektumok létrehozásához az [alapértelmezett névtérben](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) , és indítsa el a saját üzemeltetésű átjáró hüvelyeit a Microsoft Container Registryból letöltött [tároló-rendszerképből](https://aka.ms/apim/sputnik/dhub) .
10. A következő parancs futtatásával ellenőrizze, hogy a telepítés sikeres volt-e. Vegye figyelembe, hogy az összes létrehozandó objektum és a hüvelyek inicializálása is eltarthat egy kis ideig.
    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. A következő parancs futtatásával ellenőrizze, hogy a szolgáltatás sikeresen létrejött-e. Vegye figyelembe, hogy a szolgáltatás IP-címei és portjai eltérőek lesznek.
    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
12. Térjen vissza a Azure Portal, és válassza az **Áttekintés** lehetőséget.
13. Győződjön meg arról, hogy az **állapot** zöld pipa jelölést mutat, majd a YAML fájlban megadott replikák számával egyező csomópontok száma. Ez az állapot azt jelenti, hogy a telepített saját üzemeltetésű átjáró-hüvelyek sikeresen kommunikálnak a API Management szolgáltatással, és normál szívveréssel rendelkeznek.

    ![Átjáró állapota](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> A parancs futtatásával <code>kubectl logs deployment/<gateway-name></code> megtekintheti a naplókat egy véletlenszerűen kiválasztott Pod-ból, ha egynél több van.
> Futtassa a <code>kubectl logs -h</code> parancsot a parancssori kapcsolók teljes készletéhez, például egy adott Pod vagy tároló naplóinak megtekintéséhez.

## <a name="production-deployment-considerations"></a>Éles üzembe helyezési megfontolások

### <a name="access-token"></a>Hozzáférési jogkivonat
Érvényes hozzáférési jogkivonat nélkül a saját üzemeltetésű átjáró nem fér hozzá és nem tölthet le konfigurációs adatait a társított API Management szolgáltatás végpontján. A hozzáférési jogkivonat legfeljebb 30 napig érvényes lehet. Újra kell indítani, és a fürtöt egy friss tokenrel kell konfigurálni manuálisan vagy automatizálással, mielőtt lejár.

Amikor automatizálja a jogkivonat-frissítést, [ezzel a kezelési API-művelettel](/rest/api/apimanagement/2019-12-01/gateway/generatetoken) új tokent hozhatja ki. A Kubernetes-titkok kezelésével kapcsolatos információkért tekintse meg a [Kubernetes webhelyét](https://kubernetes.io/docs/concepts/configuration/secret).

### <a name="namespace"></a>Névtér
A Kubernetes- [névterek](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) segítségével egyetlen fürtöt oszthat meg több csapat, projekt vagy alkalmazás között. A névterek az erőforrások és a nevek hatókörét biztosítják. Ezek társíthatók erőforrás-kvótával és hozzáférés-vezérlési szabályzatokkal.

A Azure Portal parancsokat biztosít a saját üzemeltetésű átjáró-erőforrások létrehozásához az **alapértelmezett** névtérben. Ez a névtér automatikusan létrejön, minden fürtben létezik, és nem törölhető.
Érdemes lehet saját üzemeltetésű átjárót [létrehozni és üzembe helyezni](https://kubernetesbyexample.com/ns/) az éles környezetben található különálló névtérben.

### <a name="number-of-replicas"></a>Replikák száma
Az éles használatra alkalmas replikák minimális száma kettő.

Alapértelmezés szerint a rendszer egy saját üzemeltetésű átjárót helyez üzembe egy **RollingUpdate** -telepítési [stratégiával](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy). Tekintse át az alapértelmezett értékeket, és érdemes explicit módon beállítani a [maxUnavailable](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) és a [maxSurge](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) mezőket, különösen ha magas replikákat használ.

### <a name="container-resources"></a>Tároló erőforrásai
Alapértelmezés szerint a Azure Portalban megadott YAML-fájl nem ad meg tároló-erőforrásra vonatkozó kérelmeket.

Nem lehet megbízhatóan megbecsülni és javasolni a tároló CPU-és memória-erőforrásainak mennyiségét, valamint az adott munkaterhelés támogatásához szükséges replikák számát. Számos tényező játszik, például:

- Adott hardver, amelyen a fürt fut.
- A virtualizáció jelenléte és típusa.
- Az egyidejű ügyfélkapcsolatok száma és sebessége.
- Kérelmek gyakorisága.
- A konfigurált szabályzatok típusa és száma.
- A hasznos adatok mérete, valamint a terhelések pufferelése vagy továbbítása.
- Háttérbeli szolgáltatás késése.

Azt javasoljuk, hogy kiindulási pontként állítsa be az erőforrás-kérelmeket két maggal és 2 GiB-ra. Hajtson végre egy terhelési tesztet, és az eredmények alapján méretezheti fel/ki-vagy bekapcsolja az eredményeket.

### <a name="container-image-tag"></a>Tároló képcímkéje
A Azure Portalban megadott YAML-fájl a **legújabb** címkét használja. Ez a címke mindig a saját üzemeltetésű átjáró-tároló rendszerképének legújabb verziójára hivatkozik.

Érdemes lehet egy adott verziójú címkét használni az éles környezetben, hogy elkerülje a véletlen frissítést egy újabb verzióra.

[Az elérhető címkék teljes listáját letöltheti](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list).

### <a name="dns-policy"></a>DNS-házirend
A DNS-névfeloldás kritikus szerepet játszik a saját üzemeltetésű átjárók számára az Azure-beli függőségekhez való kapcsolódáshoz és az API-hívások háttérbeli szolgáltatásokhoz való küldéséhez.

A Azure Portalban megadott YAML-fájl az alapértelmezett [ClusterFirst](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) -házirendet alkalmazza. Ez a házirend a fürt DNS által nem feloldott névfeloldási kérelmeket eredményez a csomóponttól örökölt felsőbb rétegbeli DNS-kiszolgálóra való továbbításhoz.

A Kubernetes-névfeloldással kapcsolatos további tudnivalókért tekintse meg a [Kubernetes webhelyét](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service). Érdemes lehet a [DNS-házirendet](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) vagy a [DNS-konfigurációt](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) a beállításnak megfelelően testreszabni.

### <a name="external-traffic-policy"></a>Külső forgalmi szabályzat
A `externalTrafficPolicy` [szolgáltatás](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#service-v1-core) objektumának Azure Portal készletek mezőjében megadott YAML-fájl `Local` . Ez megőrzi a hívó IP-címét (a [kérelem kontextusában](api-management-policy-expressions.md#ContextVariables)érhető el), és letiltja a csomópontok közötti terheléselosztást, kiküszöbölve az általa okozott hálózati ugrásokat. Vegye figyelembe, hogy ez a beállítás a forgalom aszimmetrikus eloszlását okozhatja az üzemelő példányok esetében, és a csomópontok száma nem haladhatja meg az átjárók számát.

### <a name="custom-domain-names-and-ssl-certificates"></a>Egyéni tartománynevek és SSL-tanúsítványok

Ha egyéni tartományneveket használ az API Management-végpontokhoz, különösen ha egyéni tartománynevet használ a felügyeleti végponthoz, előfordulhat, hogy frissítenie kell a `config.service.endpoint` **\<gateway-name\> . YAML** fájlban lévő értéket, hogy az alapértelmezett tartománynevet az egyéni tartománynévre cserélje. Győződjön meg arról, hogy a felügyeleti végpont a saját üzemeltetésű átjáró Kubernetes-fürtben található Pod-ból érhető el.

Ebben a forgatókönyvben, ha a felügyeleti végpont által használt SSL-tanúsítványt nem egy jól ismert HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány írta alá, meg kell győződnie arról, hogy a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt megbízhatónak tartja a saját üzemeltetésű átjáró Pod-je.

### <a name="configuration-backup"></a>Konfiguráció biztonsági mentése
Ha szeretne többet megtudni a saját üzemeltetésű átjárók működéséről egy ideiglenes Azure-kapcsolat kimaradása esetén, tekintse meg a saját üzemeltetésű [átjárók áttekintése](self-hosted-gateway-overview.md#connectivity-to-azure)című témakört.

Konfigurálja a helyi tárolási kötetet a saját üzemeltetésű átjáró-tárolóhoz, így a legújabb letöltött konfiguráció biztonsági másolata is maradhat. Ha a kapcsolat nem érhető el, a tárolási kötet újraindításkor használhatja a biztonsági másolatot. A kötet csatlakoztatási útvonalának kötelezőnek kell lennie <code>/apim/config</code> . Tekintse meg a [githubon](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml)látható példát.
A Kubernetes-beli tárolással kapcsolatos további tudnivalókért tekintse meg a [Kubernetes webhelyét](https://kubernetes.io/docs/concepts/storage/volumes/).

### <a name="local-logs-and-metrics"></a>Helyi naplók és mérőszámok
A saját üzemeltetésű átjáró a társított API Management szolgáltatás konfigurációs beállításai alapján telemetria küld a [Azure monitor](api-management-howto-use-azure-monitor.md) és az [Azure Application Insightsnak](api-management-howto-app-insights.md) .
Ha az [Azure-hoz való csatlakozás](self-hosted-gateway-overview.md#connectivity-to-azure) átmenetileg megszakad, a telemetria az Azure-ba való továbbítása megszakad, és a leállás ideje alatt az adatvesztés történik.
Érdemes lehet [helyi figyelést beállítani](how-to-configure-local-metrics-logs.md) , hogy megfigyelje az API-forgalom megfigyelését, és megakadályozza a telemetria elvesztését az Azure-kapcsolat kimaradásai során.

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni a saját üzemeltetésű átjáróról, tekintse meg a saját üzemeltetésű [átjáró áttekintése](self-hosted-gateway-overview.md)című témakört.
