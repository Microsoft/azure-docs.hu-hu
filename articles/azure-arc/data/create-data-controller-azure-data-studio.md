---
title: Adatkezelő létrehozása Azure Data Studioban
description: Adatkezelő létrehozása Azure Data Studioban
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: f2d44cc769e9673eeb75828126f806d2b2308a17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573880"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Adatkezelő létrehozása Azure Data Studioban

Azure Data Studio használatával létrehozhat egy adatvezérlőt a központi telepítés varázsló és a jegyzetfüzetek segítségével.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

- Hozzá kell férnie egy Kubernetes-fürthöz, és a kubeconfig-fájl úgy van konfigurálva, hogy a telepíteni kívánt Kubernetes-fürtre mutasson.
- [Telepítenie kell az ügyféleszközök](install-client-tools.md) , például az  **Azure arc** és a Azure Data Studio bővítmények Azure Data Studio **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** .
- Azure Data Studio-ban be kell jelentkeznie az Azure-ba.  Ehhez írja be a CTRL/Command + SHIFT + P billentyűkombinációt a parancs szövege ablak megnyitásához, majd írja be a következőt: **Azure**.  Válassza **Az Azure: bejelentkezés** lehetőséget.   Egy Azure-fiók hozzáadásához kattintson a jobb felső sarokban található + ikonra a panelen.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>Az Azure arc-adatkezelő létrehozása a központi telepítési varázslóval

Az alábbi lépéseket követve létrehozhat egy Azure arc-adatkezelőt a központi telepítés varázsló segítségével.

1. A Azure Data Studio kattintson a bal oldali navigációs sávon a kapcsolatok fülre.
2. Kattintson a kapcsolatok panel tetején található **...** gombra, és válassza az **új központi telepítés...** lehetőséget.
3. Az új központi telepítés varázslóban válassza az **Azure arc-adatkezelő** elemet, majd kattintson a lap alján található **kiválasztás** gombra.
4. Győződjön meg arról, hogy az előfeltételként szükséges eszközök elérhetők és megfelelnek a szükséges verzióknak. **Kattintson a Tovább gombra**.
5. Használja az alapértelmezett kubeconfig-fájlt, vagy válasszon egy másikat.  Kattintson a **Tovább** gombra.
6. Válassza ki a Kubernetes-fürt környezetét. Kattintson a **Tovább** gombra.
7. A cél Kubernetes-fürttől függően válasszon egy központi telepítési konfigurációs profilt. **Kattintson a Tovább gombra**.
8. Ha az Azure Red Hat OpenShift vagy a Red Hat OpenShift Container platformot használja, alkalmazza a biztonsági környezet korlátozásait. Kövesse a [biztonsági környezeti korlátozás alkalmazása az Azure arc-kompatibilis adatszolgáltatásokra a OpenShift-on](how-to-apply-security-context-constraint.md)című témakör utasításait.

   >[!IMPORTANT]
   >Az Azure Red Hat OpenShift vagy a Red Hat OpenShift Container platformon az adatkezelő létrehozása előtt a biztonsági környezeti korlátozást kell alkalmaznia.

1. Válassza ki a kívánt előfizetést és erőforráscsoportot.
1. Válasszon egy Azure-beli helyet.
   
   Az itt kiválasztott Azure-hely az az Azure-beli hely, ahol az adatvezérlővel és az általa kezelt adatbázis-példányokkal kapcsolatos *metaadatok* lesznek tárolva. Az adatkezelő és az adatbázis példányai ténylegesen a Kubernetes-fürtben jönnek létre, bárhol is legyenek.

10. Válassza ki a megfelelő kapcsolódási módot. További információ a [kapcsolati módokról](./connectivity.md). **Kattintson a Tovább gombra**.

    Ha a közvetlen kapcsolati mód lehetőséget választja, az egyszerű szolgáltatásnév hitelesítő adatai szükségesek az [egyszerű szolgáltatás létrehozása](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)című témakörben leírtak szerint.

11. Adja meg az adatvezérlő nevét, valamint azt a névteret, amelyhez az adatkezelőt létre kívánja hozni.

    A rendszer az adatkezelő és a névtér nevét fogja használni egy egyéni erőforrás létrehozásához a Kubernetes-fürtben, hogy meg kell felelniük a [Kubernetes elnevezési konvencióinak](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).
    
    Ha a névtér már létezik, akkor a rendszer akkor fogja használni, ha a névtér még nem tartalmaz más Kubernetes objektumokat – hüvelyeket stb.  Ha a névtér nem létezik, a rendszer kísérletet tesz a névtér létrehozására.  A névtér Kubernetes-fürtben való létrehozásához a Kubernetes-fürt rendszergazdai jogosultságai szükségesek.  Ha nem rendelkezik Kubernetes-fürt rendszergazdai jogosultságokkal, kérje meg a Kubernetes-fürt rendszergazdáját, hogy hajtsa végre az első néhány lépést az [adatkezelő létrehozása a Kubernetes natív eszközökkel](./create-data-controller-using-kubernetes-native-tools.md) című cikkben, amelyet egy Kubernetes-rendszergazdának kell elvégeznie a varázsló befejezése előtt.


12. Válassza ki azt a tárolási osztályt, amelyben az adatkezelőt telepíteni kívánja. 
13.  Adja meg a felhasználónevet és a jelszót, és erősítse meg az adatkezelő rendszergazdai felhasználói fiókjának jelszavát. Kattintson a **Tovább** gombra.

14. Tekintse át a telepítési konfigurációt.
15. Kattintson a **központi** telepítés elemre a kívánt konfiguráció vagy a **parancsfájl a jegyzetfüzetbe** való telepítéséhez, illetve a központi telepítési utasítások áttekintéséhez, illetve a szükséges módosítások elvégzéséhez, például a tárolási osztályok neveinek vagy szolgáltatásainak módosításához. Kattintson az **összes futtatása** gombra a jegyzetfüzet tetején.

## <a name="monitoring-the-creation-status"></a>A létrehozási állapot figyelése

A vezérlő létrehozása több percet is igénybe vehet. A következő parancsokkal figyelheti a folyamatot egy másik terminál ablakban:

> [!NOTE]
>  Az alábbi parancsok azt feltételezik, hogy létrehozott egy adatkezelőt és egy Kubernetes-névteret az "arc" névvel.  Ha más névtér-vagy adatvezérlő-nevet használt, az "ív" kifejezés helyére a nevét használhatja.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Egy adott Pod létrehozási állapotát is megtekintheti egy, az alábbihoz hasonló parancs futtatásával.  Ez különösen hasznos az esetleges problémák elhárításához.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>A létrehozási problémák elhárítása

Ha bármilyen problémába ütközik a létrehozással kapcsolatban, tekintse meg a [hibaelhárítási útmutatót](troubleshoot-guide.md).
