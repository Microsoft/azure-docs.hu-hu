---
title: PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozása a Kubernetes Tools használatával
description: PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozása a Kubernetes Tools használatával
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: fd1b74d33793c06e586a92cc8b2e8d2d36f4827a
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519962"
---
# <a name="create-a-postgresql-hyperscale-server-group-using-kubernetes-tools"></a>PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozása a Kubernetes Tools használatával

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Létre kell hoznia egy [Azure arc-adatvezérlőt](./create-data-controller.md).

A PostgreSQL nagy kapacitású-kiszolgálócsoport Kubernetes eszközök használatával történő létrehozásához telepítenie kell a Kubernetes-eszközöket.  Az ebben a cikkben szereplő példák az alábbi módon használhatók `kubectl` , de más Kubernetes-eszközökkel, például a Kubernetes-irányítópulttal is használhatók, `oc` vagy `helm` Ha ismeri ezeket az eszközöket és a Kubernetes YAML/JSON-t.

[A kubectl eszköz telepítése](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Áttekintés

A PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozásához létre kell hoznia egy Kubernetes titkot, amely a postgres rendszergazdai felhasználónevét és jelszavát, valamint a PostgreSQL nagy kapacitású-kiszolgálócsoport egyéni erőforrásait tárolja a PostgreSQL-12 vagy PostgreSQL-11 egyéni erőforrás-definíció alapján.

## <a name="create-a-yaml-file"></a>YAML-fájl létrehozása

A [sablon YAML](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/postgresql.yaml) -fájlját kiindulási pontként használhatja a saját egyéni PostgreSQL nagy kapacitású-YAML-fájl létrehozásához.  Töltse le ezt a fájlt a helyi számítógépre, és nyissa meg egy szövegszerkesztőben.  Hasznos egy szövegszerkesztő, például a [vs Code](https://code.visualstudio.com/download) használata, amely támogatja a szintaxis kiemelését és a YAML-fájlok használatát.

Ez egy példa a YAML fájlra:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
kind: Secret
metadata:
  name: pg1-login-secret
type: Opaque
---
apiVersion: arcdata.microsoft.com/v1alpha1
kind: postgresql-12
metadata:
  generation: 1
  name: pg1
spec:
  engine:
    extensions:
    - name: citus
  scale:
    shards: 3
  scheduling:
    default:
      resources:
        limits:
          cpu: "4"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>A bejelentkezés és a jelszó testreszabása.
A titkos Kubernetes a rendszer Base64 kódolású karakterláncként tárolja – egyet a felhasználónévhez és egyet a jelszóhoz.  Base64 kódolást kell végeznie a rendszergazdai bejelentkezés és a jelszó megadásához, és a helyőrző helyére kell helyeznie a és a-t `data.password` `data.username` .  Ne adja meg a `<` `>` sablonban megadott és szimbólumokat.

Az online eszközzel Base64-kódolást használhat a kívánt felhasználónévvel és jelszóval, vagy a platformtól függően beépített CLI-eszközöket is használhat.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

### <a name="customizing-the-name"></a>A név testreszabása

A sablon "PG1" értékkel rendelkezik a name attribútumhoz.  Ezt módosíthatja, de a DNS-elnevezési szabványoknak megfelelő karakternek kell lennie.  A titok nevét is módosítania kell, hogy egyezzen.  Ha például megváltoztatja a PostgreSQL nagy kapacitású-kiszolgáló csoport nevét a "PG2" értékre, a titkos kulcsot a "PG1-login-Secret" értékről a "PG2-login-Secret" névre kell módosítania.

### <a name="customizing-the-engine-version"></a>A motor verziójának testreszabása

Az attribútum szerkesztésével módosíthatja a motor verzióját PostgreSQL-11 vagy PostgreSQL-12 értékűre `kind` .

### <a name="customizing-the-resource-requirements"></a>Az erőforrás-követelmények testreszabása

Igény szerint módosíthatja az erőforrás-követelményeket – a RAM-ot és az alapvető korlátokat és kéréseket.  

> [!NOTE]
> További információ: [Kubernetes erőforrás-szabályozás](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Az erőforrás-korlátokra és-kérelmekre vonatkozó követelmények:
- A magok korlátjának értéke a számlázáshoz **szükséges** .
- A többi erőforrás-kérelem és-korlát nem kötelező.
- Ha meg van adva, a magok korlátjának és a kérésnek pozitív egész számnak kell lennie.
- Ha meg van adva, legalább 1 mag szükséges a magok kéréséhez.
- A memória értékének formátuma a Kubernetes jelölését követi.  

### <a name="customizing-service-type"></a>Szolgáltatás típusának testreszabása

Ha szükséges, a szolgáltatás típusa NodePort módosítható.  A rendszer egy véletlenszerű portszámot rendel hozzá.

### <a name="customizing-storage"></a>A tárterület testreszabása

A tárolási osztályok testreszabhatók a környezetnek megfelelően.  Ha nem biztos benne, hogy mely tárolási osztályok érhetők el, a parancs futtatásával `kubectl get storageclass` megtekintheti őket.  A sablon alapértelmezett értéke "default".  Ez azt jelenti, hogy létezik egy "default" _nevű_ tárolási osztály, amely _nem az alapértelmezett_ tárolási osztály.  Igény szerint módosíthatja a tárterület méretét is.  További információt a [tárolási konfigurációról](./storage-configuration.md)a következő cikkekben olvashat:.

## <a name="creating-the-postgresql-hyperscale-server-group"></a>A PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozása

Most, hogy testre szabta a PostgreSQL nagy kapacitású Server Group YAML-fájlt, a következő parancs futtatásával hozhatja létre a PostgreSQL nagy kapacitású Server-csoportot:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\postgres.yaml
```


## <a name="monitoring-the-creation-status"></a>A létrehozási állapot figyelése

A PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozása néhány percet is igénybe vehet. A következő parancsokkal figyelheti a folyamatot egy másik terminál ablakban:

> [!NOTE]
>  Az alábbi parancsok azt feltételezik, hogy létrehozott egy "PG1" nevű PostgreSQL nagy kapacitású-csoportot, és Kubernetes névteret az "arc" névvel.  Ha más névtér/PostgreSQL nagy kapacitású-kiszolgálócsoport nevét használta, akkor az "arc" és a "PG1" nevet is használhatja a nevükkel.

```console
kubectl get postgresql-12/pg1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Egy adott Pod létrehozási állapotát is megtekintheti egy, az alábbihoz hasonló parancs futtatásával.  Ez különösen hasznos az esetleges problémák elhárításához.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/pg1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>A létrehozási problémák elhárítása

Ha bármilyen problémába ütközik a létrehozással kapcsolatban, tekintse meg a [hibaelhárítási útmutatót](troubleshoot-guide.md).