---
title: Hangulatelemzés Kubernetes-konfiguráció és a lépések telepítése
titleSuffix: Azure Cognitive Services
description: Hangulatelemzés Kubernetes-konfiguráció és a lépések telepítése
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: ca8d4d725ff25687d1005ddab1964316a147c730
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017862"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>Az Hangulatelemzés tároló üzembe helyezése egy AK-fürtön

1. Nyissa meg az Azure CLI-t, és jelentkezzen be az Azure-ba.

    ```azurecli
    az login
    ```

1. Jelentkezzen be az AK-fürtbe. Cserélje `your-cluster-name` le `your-resource-group` a és a értéket a megfelelő értékekre.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    A parancs futtatása után a következőhöz hasonló üzenetet küld:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Ha több előfizetése is elérhető az Azure-fiókjában, és a `az aks get-credentials` parancs hibával tér vissza, egy gyakori probléma, hogy nem megfelelő előfizetést használ. Állítsa be az Azure CLI-munkamenet kontextusát úgy, hogy ugyanazt az előfizetést használja, mint amelyet az erőforrásokhoz hozott létre, és próbálkozzon újra.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Nyissa meg a választható szövegszerkesztőt. Ez a példa a Visual Studio Code-ot használja.

    ```console
    code .
    ```

1. A szövegszerkesztőben hozzon létre egy " *YAML*" nevű új fájlt, és illessze be az alábbi YAML. Ügyeljen arra, hogy a `billing/value` és a `apikey/value` saját adatait cserélje le.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. Mentse a fájlt, és zárjuk be a szövegszerkesztőt.
1. Futtassa a Kubernetes `apply` parancsot a *hangulat. YAML* fájllal a célként megadott módon:

    ```console
    kubectl apply -f sentiment.yaml
    ```

    Miután a parancs sikeresen alkalmazta a központi telepítési konfigurációt, a következő kimenethez hasonló üzenet jelenik meg:

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Ellenőrizze, hogy telepítve van-e a pod:

    ```console
    kubectl get pods
    ```

    A pod futási állapotának kimenete:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Győződjön meg arról, hogy a szolgáltatás elérhető, és kérje le az IP-címet.

    ```console
    kubectl get services
    ```

    Az *hangulati* szolgáltatás a pod-ban futó állapotának kimenete:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
