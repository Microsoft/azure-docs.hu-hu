---
title: SQL felügyelt példány létrehozása a Kubernetes Tools használatával
description: SQL felügyelt példány létrehozása a Kubernetes Tools használatával
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: d23df80a3f80ed96779297bac12ef0ed8d2927d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687923"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>Felügyelt Azure SQL-példány létrehozása a Kubernetes-eszközök használatával

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Létre kell hoznia egy [Azure arc-adatvezérlőt](./create-data-controller.md).

A felügyelt SQL-példányok Kubernetes-eszközök használatával történő létrehozásához telepítenie kell a Kubernetes-eszközöket.  Az ebben a cikkben szereplő példák az alábbi módon használhatók `kubectl` , de más Kubernetes-eszközökkel, például a Kubernetes-irányítópulttal is használhatók, `oc` vagy `helm` Ha ismeri ezeket az eszközöket és a Kubernetes YAML/JSON-t.

[A kubectl eszköz telepítése](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Áttekintés

SQL felügyelt példány létrehozásához létre kell hoznia egy Kubernetes titkot, amely a rendszergazda felhasználónevét és jelszavát biztonságos módon, valamint egy, a sqlmanagedinstance egyéni erőforrás-definícióján alapuló SQL felügyelt példány egyéni erőforrását tárolja.

## <a name="create-a-yaml-file"></a>YAML-fájl létrehozása

A [sablon YAML](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/sqlmi.yaml) -fájlja kiindulási pontként használható a saját egyéni SQL felügyelt példány YAML fájljának létrehozásához.  Töltse le ezt a fájlt a helyi számítógépre, és nyissa meg egy szövegszerkesztőben.  Hasznos egy szövegszerkesztő, például a [vs Code](https://code.visualstudio.com/download) használata, amely támogatja a szintaxis kiemelését és a YAML-fájlok használatát.

Ez egy példa a YAML fájlra:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded user name. 'sa' is not allowed>
kind: Secret
metadata:
  name: sql1-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
  name: sql1
spec:
  limits:
    memory: 4Gi
    vcores: "4"
  requests:
    memory: 2Gi
    vcores: "1"
  service:
    type: LoadBalancer
  storage:
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>A bejelentkezés és a jelszó testreszabása

A titkos Kubernetes a rendszer Base64 kódolású karakterláncként tárolja – egyet a felhasználónévhez és egyet a jelszóhoz.  Base64 kódolást kell végeznie a rendszergazdai bejelentkezés és a jelszó megadásához, és a helyőrző helyére kell helyeznie a következő helyen: `data.password` és `data.username` .  Ne adja meg a `<` `>` sablonban megadott és szimbólumokat.

> [!NOTE]
> Az optimális biztonság érdekében a "SA" érték használata nem engedélyezett a bejelentkezéshez.
> Kövesse a [jelszó bonyolultsága házirendet](/sql/relational-databases/security/password-policy#password-complexity).

Az online eszközzel Base64-kódolást használhat a kívánt felhasználónévvel és jelszóval, vagy a platformtól függően beépített CLI-eszközöket is használhat.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

### <a name="customizing-the-name"></a>A név testreszabása

A sablon "sql1" értékkel rendelkezik a name attribútumhoz.  Ezt módosíthatja, de a DNS-elnevezési szabványoknak megfelelő karakternek kell lennie.  A titok nevét is módosítania kell, hogy egyezzen.  Ha például a felügyelt SQL-példány nevét "sql2" értékre módosítja, akkor a titkos kulcsot a "sql1-login-Secret" értékről "sql2-login-Secret" névre kell módosítania.

### <a name="customizing-the-resource-requirements"></a>Az erőforrás-követelmények testreszabása

Igény szerint módosíthatja az erőforrás-követelményeket – a RAM-ot és az alapvető korlátokat és kéréseket.  

> [!NOTE]
> További információ: [Kubernetes erőforrás-szabályozás](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Az erőforrás-korlátokra és-kérelmekre vonatkozó követelmények:
- A magok korlátjának értéke a számlázáshoz **szükséges** .
- A többi erőforrás-kérelem és-korlát nem kötelező.
- Ha meg van adva, a magok korlátjának és a kérésnek pozitív egész számnak kell lennie.
- Ha meg van adva, a magok kéréséhez legalább 2 mag szükséges.
- A memória értékének formátuma a Kubernetes jelölését követi.  
- Ha meg van adva, legalább 2Gi szükséges a memória-kérelemhez.
- Általános útmutatóként minden 1 mag esetében 4 GB RAM-ot kell használni üzemi használati esetekben.

### <a name="customizing-service-type"></a>Szolgáltatás típusának testreszabása

Ha szükséges, a szolgáltatás típusa NodePort módosítható.  A rendszer egy véletlenszerű portszámot rendel hozzá.

### <a name="customizing-storage"></a>A tárterület testreszabása

A tárolási osztályok testreszabhatók a környezetnek megfelelően.  Ha nem biztos benne, hogy mely tárolási osztályok érhetők el, a parancs futtatásával `kubectl get storageclass` megtekintheti őket.  A sablon alapértelmezett értéke "default".  Ez azt jelenti, hogy létezik egy "default" _nevű_ tárolási osztály, amely _nem az alapértelmezett_ tárolási osztály.  Igény szerint módosíthatja a tárterület méretét is.  További információt a [tárolási konfigurációról](./storage-configuration.md)a következő cikkekben olvashat:.

## <a name="creating-the-sql-managed-instance"></a>Az SQL felügyelt példányának létrehozása

Most, hogy testreszabta az SQL felügyelt példányának YAML-fájlját, a következő parancs futtatásával hozhatja létre az SQL felügyelt példányt:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```


## <a name="monitoring-the-creation-status"></a>A létrehozási állapot figyelése

A felügyelt SQL-példány létrehozása eltarthat néhány percig. A következő parancsokkal figyelheti a folyamatot egy másik terminál ablakban:

> [!NOTE]
>  Az alábbi parancsok azt feltételezik, hogy létrehozta a "sql1" nevű SQL felügyelt példányt és a Kubernetes névteret az "arc" névvel.  Ha más névtér/SQL felügyelt példány nevét használta, az "arc" és a "sqlmi" helyett a saját nevét is használhatja.

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Egy adott Pod létrehozási állapotát is megtekintheti egy, az alábbihoz hasonló parancs futtatásával.  Ez különösen hasznos az esetleges problémák elhárításához.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>A létrehozási problémák elhárítása

Ha bármilyen problémába ütközik a létrehozással kapcsolatban, tekintse meg a [hibaelhárítási útmutatót](troubleshoot-guide.md).

## <a name="next-steps"></a>Következő lépések

[Kapcsolódás az Azure arc használatára képes SQL felügyelt példányhoz](connect-managed-instance.md)
