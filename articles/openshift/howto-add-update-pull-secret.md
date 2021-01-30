---
title: Red Hat pull-titok hozzáadása vagy frissítése Azure Red Hat OpenShift 4-fürtön
description: Red Hat pull-titok hozzáadása vagy frissítése meglévő 4. x ARO-fürtökhöz
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 05/21/2020
keywords: pull Secret, ARO, openshift, Red Hat
ms.openlocfilehash: b1a8e45a2eab1b7b4c8a5936049f404d5bd3441c
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071086"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>Red Hat pull-titok hozzáadása vagy frissítése Azure Red Hat OpenShift 4-fürtön

Ez az útmutató a Red Hat pull Secret egy meglévő Azure Red Hat OpenShift (ARO) 4. x fürthöz való hozzáadását és frissítését ismerteti.

Ha első alkalommal hoz létre fürtöt, a fürt létrehozásakor felveheti a lekérési titkot. A Red Hat pull secrettel rendelkező ARO-fürtök létrehozásával kapcsolatos további információkért lásd: [Azure Red Hat OpenShift 4-fürt létrehozása](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional).

## <a name="before-you-begin"></a>Előkészületek

Ez az útmutató feltételezi, hogy rendelkezik egy meglévő Azure Red Hat OpenShift 4 fürttel. Győződjön meg arról, hogy rendszergazdai hozzáférése van a fürthöz.

## <a name="prepare-your-pull-secret"></a>A lekéréses titok előkészítése
Ha ARO-fürtöt hoz létre a Red Hat pull Secret hozzáadása nélkül, a rendszer automatikusan létrehoz egy lekérési titkot a fürtön. Azonban ez a lekérési titok nincs teljesen feltöltve.

Ez a szakasz a Red Hat pull Secret-ből származó további értékekkel rendelkező, a lekéréses titkos kód frissítését ismerteti.

1. A következő parancs futtatásával olvassa be a névtérben megnevezett titkot `pull-secret` `openshift-config` , és mentse egy külön fájlba: 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    A kimenetnek az alábbihoz hasonlónak kell lennie. (Vegye figyelembe, hogy a tényleges titkos érték el lett távolítva.)

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. Nyissa meg a [Red Hat OpenShift cluster Manager-portált](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) , és válassza a **lekéréses titok letöltése** lehetőséget. A Red Hat pull Secret a következőhöz hasonlóan fog kinézni. (Vegye figyelembe, hogy a tényleges titkos értékek el lettek távolítva.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. Szerkessze a fürtből kapott lekéréses titkos fájlt a Red Hat pull-titokban található bejegyzések hozzáadásával. 

    > [!IMPORTANT]
    > A `cloud.openshift.com` Red Hat pull Secret bejegyzésének beírásával a fürt a telemetria-adatok Red Hat-ra való küldését is elindíthatja. Csak akkor adja meg ezt a szakaszt, ha telemetria-adatküldést szeretne küldeni. Ellenkező esetben hagyja meg a következő szakaszt.    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > Ne távolítsa el vagy módosítsa a `arosvc.azurecr.io` lekéréses titokból származó bejegyzést. Ez a szakasz szükséges ahhoz, hogy a fürt megfelelően működjön.

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    A végső fájlnak az alábbihoz hasonlóan kell kinéznie. (Vegye figyelembe, hogy a tényleges titkos értékek el lettek távolítva.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. Győződjön meg arról, hogy a fájl érvényes JSON. A JSON számos módon érvényesíthető. A következő példa a jQ használja:

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > Ha egy hiba szerepel a fájlban, az a következőképpen jelenik meg: `parse error` .

## <a name="add-your-pull-secret-to-your-cluster"></a>A lekéréses titkos kód hozzáadása a fürthöz

Futtassa a következő parancsot a lekéréses titok frissítéséhez.

> [!NOTE]
> A parancs futtatása azt eredményezi, hogy a fürtcsomópontok egy-egy frissítést követően újraindulnak. 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

A titok beállítása után készen áll a Red Hat Certified operátorok engedélyezésére.

### <a name="modify-the-configuration-files"></a>A konfigurációs fájlok módosítása

A Red Hat operátorok engedélyezéséhez módosítsa a következő objektumokat.

Először módosítsa a minták kezelője konfigurációs fájlját. Ezután futtassa a következő parancsot a konfigurációs fájl szerkesztéséhez:

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

Módosítsa a `spec.architectures.managementState` és a `status.architecture.managementState` értékét a következőre: `Removed` `Managed` . 

A következő YAML-kódrészlet csak a szerkesztett YAML fájl megfelelő részeit jeleníti meg:

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

Másodszor, futtassa a következő parancsot az operátori központ konfigurációs fájljának szerkesztéséhez:  

```console
oc edit operatorhub cluster -o yaml
```

Módosítsa a `Spec.Sources.Disabled` és a `Status.Sources.Disabled` érték `true` értékét `false` minden olyan forrás esetében, amelyet engedélyezni szeretne.

A következő YAML-kódrészlet csak a szerkesztett YAML fájl megfelelő részeit jeleníti meg:

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

Mentse a fájlt a módosítások alkalmazásához.

## <a name="validate-that-your-secret-is-working"></a>Annak ellenőrzése, hogy a titka működik-e

Miután hozzáadta a lekéréses titkot, és módosítja a megfelelő konfigurációs fájlokat, a fürt frissítése több percet is igénybe vehet. A fürt frissítésének megadásához futtassa a következő parancsot a Certified operátorok és a Red Hat operátorok rendelkezésre álló forrásai megjelenítéséhez:

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

Ha nem látja a Certified operátorokat és a Red Hat operátort, várjon néhány percet, és próbálkozzon újra.

A lekéréses titok frissítésének és megfelelő működésének biztosítása érdekében nyissa meg a OperatorHub, és ellenőrizze, hogy van-e Red Hat ellenőrzött operátor. Ellenőrizze például, hogy elérhető-e a OpenShift-tároló kezelője, és hogy van-e engedélye a telepítésére.

## <a name="next-steps"></a>Következő lépések
A Red Hat pull Secrets szolgáltatással kapcsolatos további információkért lásd: [a képek lekérésével kapcsolatos titkok használata](https://docs.openshift.com/container-platform/4.6/openshift_images/managing_images/using-image-pull-secrets.html).

A Red Hat OpenShift 4-ről további információt a [Red Hat OpenShift Container platform dokumentációjában](https://docs.openshift.com/container-platform/4.6/welcome/index.html)talál.
