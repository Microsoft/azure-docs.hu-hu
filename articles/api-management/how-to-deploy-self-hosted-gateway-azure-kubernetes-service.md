---
title: Saját üzemeltetésű átjáró üzembe helyezése az Azure Kubernetes Service-ben | Microsoft Docs
description: Ismerje meg, hogyan helyezheti üzembe az Azure API Management saját üzemeltetésű átjáró összetevőjét az Azure Kubernetes Service-ben
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 02962e9c5be2c4b73d121a53a7b595c573ad6cd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87015221"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Üzembe helyezés az Azure Kubernetes Service-ben

Ez a cikk az Azure-API Management saját üzemeltetésű átjáró-összetevőjének [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/)-be történő üzembe helyezésének lépéseit ismerteti. Ha saját üzemeltetésű átjárót szeretne üzembe helyezni egy Kubernetes-fürtön, tekintse meg ezt a[dokumentumot](how-to-deploy-self-hosted-gateway-kubernetes.md).

## <a name="prerequisites"></a>Előfeltételek

- [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
- [Azure Kubernetes-fürt létrehozása](../aks/kubernetes-walkthrough-portal.md)
- [Átjáró-erőforrás kiépítése a API Management-példányban](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>A saját üzemeltetésű átjáró üzembe helyezése az AK-ban

1. Válassza az **átjárók** lehetőséget az **üzembe helyezés és az infrastruktúra** területen.
2. Válassza ki azt a saját üzemeltetésű átjáró-erőforrást, amelyet telepíteni kíván.
3. Válassza a **telepítés** lehetőséget.
4. Vegye figyelembe, hogy a **jogkivonat** szövegmezőben lévő új jogkivonat automatikusan lett létrehozva az alapértelmezett **lejárati** és **titkos kulcs** értékeinek használatával. Ha szükséges, módosítsa a vagy mindkettőt, és válassza a **Létrehozás** lehetőséget egy új jogkivonat létrehozásához.
5. Győződjön meg arról, hogy az **üzembehelyezési parancsfájlok** területen a **Kubernetes** van kiválasztva.
6. Válassza ki **<Gateway-name>. YML-** fájl hivatkozását a **központi telepítés** mellett a fájl letöltéséhez.
7. Szükség szerint módosítsa a port-hozzárendeléseket és a tároló nevét a YML fájlban.
8. A forgatókönyvtől függően előfordulhat, hogy módosítania kell a [szolgáltatás típusát](../aks/concepts-network.md#services). Az alapértelmezett érték `NodePort`.
9. Válassza a **telepítés** szövegmező jobb oldalán található **Másolás** ikont a `kubectl` parancs vágólapra mentéséhez.
10. Illessze be a parancsot a terminál (vagy a parancs) ablakába. Vegye figyelembe, hogy a parancs elvárja, hogy a letöltött környezeti fájl megtalálható legyen az aktuális könyvtárban.
```console
    kubectl apply -f <gateway-name>.yaml
```
11. Hajtsa végre a parancsot. A parancs arra utasítja az AK-fürtöt, hogy futtassa a tárolót a saját üzemeltetésű átjáró a Microsoft Container Registryból letöltött rendszerképének használatával, és konfigurálja a tárolót a HTTP-(8080-) és a HTTPS-(443-) portok elérhetővé tétele érdekében.
12. Futtassa az alábbi parancsot, és győződjön meg róla, hogy az átjáró Pod fut. Vegye figyelembe, hogy a pod neve eltérő lesz.
```console
kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
```
13. Futtassa az alábbi parancsot az átjáró szolgáltatás futásának vizsgálatához. Vegye figyelembe, hogy a szolgáltatás neve és IP-címei eltérőek lesznek.
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
14. Térjen vissza a Azure Portalhoz, és ellenőrizze, hogy az imént telepített átjáró csomópontja kifogástalan állapotú-e.

> [!TIP]
> A <code>kubectl logs <gateway-pod-name></code> parancs használatával megtekintheti a saját üzemeltetésű átjáró naplójának pillanatképét.

## <a name="next-steps"></a>Következő lépések

* További információ a saját üzemeltetésű átjáróról: [Azure API Management saját üzemeltetésű átjáró – áttekintés](self-hosted-gateway-overview.md)
* További információ az [Azure Kubernetes Service](../aks/intro-kubernetes.md) -ről
* Megtudhatja, [Hogyan konfigurálhat és tartson fenn naplókat a felhőben](how-to-configure-cloud-metrics-logs.md)
* * Ismerje meg, [hogyan konfigurálhatja és megőrizheti a naplókat helyileg](how-to-configure-local-metrics-logs.md)
