---
title: (ELAVULT) Azure-beli Kubernetes-fürt webes felhasználói felületének kezelése
description: Az Azure Container Service-ben a Kubernetes webes felhasználói felület használatával
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: c3a79b2e4fab807613a54d2792f5f5b97570293b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309661"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>(ELAVULT) Az Azure Container Service Kubernetes webes felhasználói felület használatával

> [!TIP]
> Ez a cikk, amely a frissített verziót használja Azure Kubernetes Service-ben, lásd: [a Kubernetes webes irányítópultját az Azure Kubernetes Service (AKS)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Előfeltételek
Az útmutató feltételezi, hogy [egy Kubernetes-fürtöt az Azure Container Service használatával létrehozott](container-service-kubernetes-walkthrough.md).


Azt is feltételezi, hogy az Azure CLI és `kubectl` telepített eszközök.

Ha rendelkezik tesztelheti a `az` futtatásával telepített eszköz:

```console
$ az --version
```

Ha nem rendelkezik a `az` eszközt telepítette, az e-mail utasításokat is [Itt](https://github.com/azure/azure-cli#installation).

Ha rendelkezik tesztelheti a `kubectl` futtatásával telepített eszköz:

```console
$ kubectl version
```

Ha nem rendelkezik `kubectl` telepítve, futtatható:

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>Áttekintés

### <a name="connect-to-the-web-ui"></a>Csatlakozás a webes felhasználói felületen
A Kubernetes webes felhasználói felület futtatásával indíthatja el:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Ez kell nyisson meg egy webböngészőt, úgy konfigurálva, hogy a helyi számítógép csatlakozik a Kubernetes webes felhasználói felület proxy biztonságos kommunikációhoz.

### <a name="create-and-expose-a-service"></a>Hozzon létre, és a szolgáltatás elérhetővé
1. A Kubernetes webes felhasználói felületen kattintson **létrehozás** gombra a jobb felső ablakban.

    ![Kubernetes Create UI](./media/container-service-kubernetes-ui/create.png)

    Egy az alkalmazás létrehozásának megkezdéséhez ahol párbeszédpanel megnyílik.

2. Adjon meg a nevet `hello-nginx`. Használja a [ `nginx` a Docker tároló](https://hub.docker.com/_/nginx/) és három replikával a webszolgáltatás üzembe helyezése.

    ![Kubernetes-Podok létrehozása párbeszédpanel](./media/container-service-kubernetes-ui/nginx.png)

3. A **szolgáltatás**válassza **külső** , és adja meg a 80-as porton.

    Ez a beállítás elosztja a három replika forgalmat.

    ![Kubernetes-szolgáltatás létrehozása párbeszédpanel](./media/container-service-kubernetes-ui/service.png)

4. Kattintson a **telepítés** ezek a tárolók és szolgáltatások telepítéséhez.

    ![Kubernetes üzembe helyezése](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>A tárolók megtekintése
Miután rákattintott **telepítés**, a felhasználói felületen jeleníti meg a a szolgáltatás, mint helyez üzembe:

![Kubernetes-állapot](./media/container-service-kubernetes-ui/status.png)

Megtekintheti a kör Kubernetes objektumok állapotát a bal oldalán található a felhasználói felületen **Podok**. Ha egy részlegesen teljes körben, az objektum még telepítés. Az objektum teljes telepítésekor egy zöld pipa jelenik meg:

![Üzembe helyezett Kubernetes](./media/container-service-kubernetes-ui/deployed.png)

Ha minden rendben, kattintson a podokat megszünteti a futó webes szolgáltatás részleteinek megtekintéséhez.

![Kubernetes-Podok](./media/container-service-kubernetes-ui/pods.png)

Az a **Podok** megtekintése, a tárolók a pod, valamint a CPU és memória-erőforrások készregyártásának által használt információkat láthatja:

![Kubernetes-erőforrást](./media/container-service-kubernetes-ui/resources.png)

Ha nem látja az erőforrásokat, szükség lehet Várjon néhány percet a figyelési adatok propagálása.

A tároló a naplók megtekintéséhez kattintson **megtekinthetők a naplófájlok**.

![Kubernetes-naplók](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>A szolgáltatás megtekintése
Mellett futnak a tárolók, a Kubernetes felhasználói felület hozott létre egy külső `Service` ahhoz, hogy a fürtben található tárolókat forgalom terheléselosztó építi ki, amely.

Kattintson a bal oldali navigációs ablaktáblában **szolgáltatások** megtekintéséhez az összes szolgáltatás (lehetnek csak egy).

![Kubernetes Services](./media/container-service-kubernetes-ui/service-deployed.png)

Nézetben megtekintheti a szolgáltatáshoz egy külső végponttal (IP-cím), amely ki van osztva.
Ha az IP-cím gombra kattint, megtekintheti a a terheléselosztó mögött futó Nginx-tároló.

![az nginx-nézet](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>A szolgáltatás átméretezése
Az objektumok megtekintése a felhasználói felületen, mellett szerkesztheti, és a Kubernetes API-t objektumokat frissíteni.

Első lépésként kattintson **központi telepítések** a bal oldali navigációs panelen megtekintheti a szolgáltatás központi telepítését.

Miután a nézet, kattintson a replikakészlethez a, és kattintson **szerkesztése** a felső navigációs sávban:

![Kubernetes Edit](./media/container-service-kubernetes-ui/edit.png)

Szerkessze a `spec.replicas` mezőre, amely `2`, és kattintson a **frissítés**.

Ennek hatására a replikákat eldobásához két egy a podok számát.

 

