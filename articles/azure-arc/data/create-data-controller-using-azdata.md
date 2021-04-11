---
title: Adatvezérlő létrehozása az Azure-beli adatparancssori felület (azdata) használatával
description: Hozzon létre egy Azure arc-adatkezelőt egy tipikus, többcsomópontos Kubernetes-fürtön, amelyet már létrehozott az Azure-beli adatparancssori felület (azdata) használatával.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 04/07/2021
ms.topic: how-to
ms.openlocfilehash: f7bc90f2748d230ad50868cff5d7a8f7b69d850a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029539"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>Azure arc-adatkezelő létrehozása a használatával [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Tekintse át a következő témakört: [Az Azure arc-adatkezelő létrehozása](create-data-controller.md) az áttekintési információkhoz.

Ha az Azure arc-adatkezelőt a használatával szeretné létrehozni, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] telepítenie kell a alkalmazást.

   [Telepítse a [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

Függetlenül attól, hogy melyik cél platformot választja, a következő környezeti változókat kell megadnia az adatkezelő rendszergazda felhasználójának létrehozása előtt. A hitelesítő adatokat megadhatja más felhasználóknak, akiknek szükségük van rendszergazdai hozzáférésre az adatkezelőhöz.

**AZDATA_USERNAME** – az adatkezelő rendszergazda felhasználójának választott felhasználóneve. Például: `arcadmin`

**AZDATA_PASSWORD** – az adatkezelő rendszergazda felhasználója által választott jelszó. A jelszónak legalább nyolc karakterből kell állnia, és tartalmaznia kell karaktereket a következő négy készletből háromból: nagybetűk, kisbetűk, számok és szimbólumok.

### <a name="linux-or-macos"></a>Linux vagy macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

Csatlakoznia kell a Kubernetes-fürthöz, és hitelesítenie kell magát egy meglévő Kubernetes-környezettel, mielőtt megkezdené az Azure arc-adatkezelő létrehozását. A Kubernetes-fürthöz vagy-szolgáltatáshoz való kapcsolódás módja változó. Tekintse meg a Kubernetes-terjesztés vagy-szolgáltatás dokumentációját, amelyet a Kubernetes API-kiszolgálóhoz való kapcsolódáshoz használ.

Ellenőrizze, hogy van-e aktuális Kubernetes-kapcsolatban, és erősítse meg az aktuális kontextust a következő parancsokkal.

```console
kubectl get namespace
kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>Az Azure arc-adatkezelő létrehozása

> [!NOTE]
> Az alábbi példákban más értéket is használhat a `--namespace` azdata arc DC Create parancs paramétereként, de ügyeljen arra, hogy a névtér nevét használja az `--namespace parameter` összes többi parancsnál.

- [Létrehozás az Azure Kubernetes Service-ben (ak)](#create-on-azure-kubernetes-service-aks)
- [Létrehozás az AK-motoron Azure Stack hub-on](#create-on-aks-engine-on-azure-stack-hub)
- [Létrehozás az AK-on Azure Stack HCI-ben](#create-on-aks-on-azure-stack-hci)
- [Létrehozás az Azure Red Hat OpenShift (ARO)](#create-on-azure-red-hat-openshift-aro)
- [Létrehozás a Red Hat OpenShift Container platformon (OCP)](#create-on-red-hat-openshift-container-platform-ocp)
- [Létrehozás nyílt forráskódú, felsőbb rétegbeli Kubernetes (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)
- [Létrehozás az AWS rugalmas Kubernetes szolgáltatásban (EKS)](#create-on-aws-elastic-kubernetes-service-eks)
- [Létrehozás a Google Cloud Kubernetes Engine Service-ben (GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Létrehozás az Azure Kubernetes Service-ben (ak)

Alapértelmezés szerint az AK-telepítési profil a `managed-premium` Storage osztályt használja. A `managed-premium` tárolási osztály csak akkor működik, ha olyan virtuális gépekkel rendelkezik, amelyek prémium szintű lemezzel rendelkező virtuálisgép-rendszerképekkel lettek telepítve.

Ha `managed-premium` tárolási osztályként fogja használni, akkor a következő parancs futtatásával hozhatja létre az adatkezelőt. Helyettesítse be a parancsban található helyőrzőket az erőforráscsoport nevével, az előfizetés-AZONOSÍTÓval és az Azure-hellyel.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Ha nem biztos abban, hogy milyen tárolási osztályt használ, akkor a tárolási osztályt kell használnia, függetlenül attól, hogy melyik virtuálisgép- `default` típust használja. Csak a leggyorsabb teljesítményt biztosítja.

Ha a `default` tárolási osztályt szeretné használni, futtassa a következő parancsot:

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

A parancs futtatása után folytassa a következővel: a [létrehozási állapot figyelése](#monitoring-the-creation-status).

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Létrehozás az AK-motoron Azure Stack hub-on

Alapértelmezés szerint a központi telepítési profil a `managed-premium` Storage osztályt használja. A `managed-premium` tárolási osztály csak akkor működik, ha olyan virtuálisgép-lemezképekkel telepített munkavégző virtuális gépeket használ, amelyek Azure stack hub Premium lemezzel rendelkeznek.

A következő parancs futtatásával hozhatja létre az adatvezérlőt a felügyelt Premium Storage osztály használatával:

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Ha nem biztos abban, hogy milyen tárolási osztályt használ, akkor a tárolási osztályt kell használnia, függetlenül attól, hogy melyik virtuálisgép- `default` típust használja. Azure Stack központban a prémium szintű lemezeket és a standard lemezeket ugyanaz a tárolási infrastruktúra támogatja. Ezért várhatóan ugyanazt az általános teljesítményt nyújtják, de eltérő IOPS-korlátokkal rendelkeznek.

Ha a `default` tárolási osztályt szeretné használni, akkor futtathatja ezt a parancsot.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

A parancs futtatása után folytassa a következővel: a [létrehozási állapot figyelése](#monitoring-the-creation-status).

### <a name="create-on-aks-on-azure-stack-hci"></a>Létrehozás az AK-on Azure Stack HCI-ben

Alapértelmezés szerint a központi telepítési profil egy nevű tárolási osztályt `default` és a szolgáltatás típusát használja `LoadBalancer` .

A következő parancs futtatásával hozhatja létre az adatvezérlőt a `default` tárolási osztály és a szolgáltatás típusa alapján `LoadBalancer` .

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

A parancs futtatása után folytassa a következővel: a [létrehozási állapot figyelése](#monitoring-the-creation-status).


### <a name="create-on-azure-red-hat-openshift-aro"></a>Létrehozás az Azure Red Hat OpenShift (ARO)

Az Azure Red Hat OpenShift biztonsági környezeti korlátozást igényel.

#### <a name="apply-the-security-context"></a>A biztonsági környezet alkalmazása

Mielőtt létrehozza az adatvezérlőt az Azure Red Hat OpenShift, bizonyos biztonsági környezeti korlátozásokat (SCC) kell alkalmaznia. Az előzetes kiadás esetében ezek a biztonsági korlátozások ellazítására használhatók. A jövőbeli kiadások a frissített SCC-t nyújtják.

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

#### <a name="create-custom-deployment-profile"></a>Egyéni telepítési profil létrehozása

Használja az `azure-arc-azure-openshift` Azure RedHat nyílt eltolású profilt.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>Adatkezelő létrehozása

Az adatkezelő létrehozásához futtassa a következő parancsot:

> [!NOTE]
> Használja ugyanazt a névteret itt és a `oc adm policy add-scc-to-user` fenti parancsokban. Példa: `arc` .

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

A parancs futtatása után folytassa a következővel: a [létrehozási állapot figyelése](#monitoring-the-creation-status).

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Létrehozás a Red Hat OpenShift Container platformon (OCP)

> [!NOTE]
> Ha a Red Hat OpenShift Container platformot használja az Azure-ban, a legújabb elérhető verziót ajánlott használni.

Mielőtt létrehozza az adatvezérlőt a Red Hat OCP, bizonyos biztonsági környezeti korlátozásokat kell alkalmaznia. 

#### <a name="apply-the-security-context-constraint"></a>A biztonsági környezet korlátozásának alkalmazása

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

#### <a name="determine-storage-class"></a>Tárolási osztály meghatározása

A következő parancs futtatásával is meg kell határoznia, hogy melyik tárolási osztályt kell használnia.

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>Egyéni telepítési profil létrehozása

Hozzon létre egy új egyéni telepítési profilt a `azure-arc-openshift` telepítési profil alapján a következő parancs futtatásával. Ez a parancs egy könyvtárat hoz létre az `custom` aktuális munkakönyvtárban és egy egyéni telepítési profilt tartalmazó fájlban a `control.json` könyvtárban.

Használja a `azure-arc-openshift` OpenShift-tároló platform profilját.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>Tárolási osztály beállítása 

Most állítsa be a kívánt tárolási osztályt úgy, hogy lecseréli az `<storageclassname>` alábbi parancsot a használni kívánt tárolási osztály nevére a `kubectl get storageclass` fenti parancs futtatásával.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>Terheléselosztó beállítása (nem kötelező)

Alapértelmezés szerint a `azure-arc-openshift` központi telepítési profil `NodePort` a szolgáltatás típusaként működik. Ha egy terheléselosztó integrált OpenShift-fürtöt használ, a konfigurációt a `LoadBalancer` következő parancs használatával módosíthatja a szolgáltatás típusának használatára:

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="verify-security-policies"></a>Biztonsági házirendek ellenőrzése

A OpenShift használatakor előfordulhat, hogy az alapértelmezett biztonsági házirendekkel szeretné futtatni a OpenShift-ben, vagy a környezetet általában több mint tipikusan szeretné lezárni. A következő parancsok futtatásával letilthatja bizonyos szolgáltatások letiltását, hogy minimálisra csökkentse a telepítéskor szükséges engedélyeket és a futási időt.

Ez a parancs letiltja a hüvelyek metrikáinak gyűjteményeit. Ha letiltja ezt a funkciót, nem fogja tudni megtekinteni a hüvelyek mérőszámait a Grafana-irányítópultokon. Alapértelmezett érték: true (igaz).

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

Ez a parancs letiltja a csomópontokkal kapcsolatos metrikák gyűjteményeit. Ha letiltja ezt a funkciót, a Grafana-irányítópultokon lévő csomópontokra vonatkozó mérőszámok nem jelennek meg. Alapértelmezett érték: true (igaz).

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

Ez a parancs letiltja a memóriaképek hibaelhárítási célból történő elvégzésének lehetőségét.
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

#### <a name="create-data-controller"></a>Adatkezelő létrehozása

Most már készen áll az adatvezérlő létrehozására a következő parancs használatával.

> [!NOTE]
>   Használja ugyanazt a névteret itt és a `oc adm policy add-scc-to-user` fenti parancsokban. Példa: `arc` .

> [!NOTE]
>   A `--path` paraméternek arra a _könyvtárra_ kell mutatnia, amely a fájl control.jsét tartalmazza, nem pedig a fájl control.js.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

A parancs futtatása után folytassa a következővel: a [létrehozási állapot figyelése](#monitoring-the-creation-status).

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Létrehozás nyílt forráskódú, felsőbb rétegbeli Kubernetes (kubeadm)

Alapértelmezés szerint a kubeadm telepítési profil egy nevű tárolási osztályt `local-storage` és egy szolgáltatás típusát használja `NodePort` . Ha ez elfogadható, kihagyhatja az alábbi utasításokat, amelyekkel megadhatja a kívánt tárolási osztályt és szolgáltatástípus-típust, és azonnal futtathatja az `azdata arc dc create` alábbi parancsot.

Ha testre szeretné szabni a telepítési profilt egy adott tárolási osztály és/vagy szolgáltatástípus megadásához, először hozzon létre egy új egyéni telepítési profilt a kubeadm-telepítési profil alapján a következő parancs futtatásával. Ez a parancs egy könyvtárat hoz létre az `custom` aktuális munkakönyvtárban és egy egyéni telepítési profilt `control.json` tartalmazó fájlban a könyvtárban.

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

A rendelkezésre álló tárolási osztályokat a következő parancs futtatásával tekintheti meg.

```console
kubectl get storageclass
```

Most állítsa be a kívánt tárolási osztályt úgy, hogy lecseréli az `<storageclassname>` alábbi parancsot a használni kívánt tárolási osztály nevére a `kubectl get storageclass` fenti parancs futtatásával.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Alapértelmezés szerint a kubeadm telepítési profil `NodePort` a szolgáltatás típusaként használja. Ha egy terheléselosztó integrált Kubernetes-fürtöt használ, a következő parancs használatával módosíthatja a konfigurációt.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

Most már készen áll az adatvezérlő létrehozására a következő parancs használatával.

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

A parancs futtatása után folytassa a következővel: a [létrehozási állapot figyelése](#monitoring-the-creation-status).

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Létrehozás az AWS rugalmas Kubernetes szolgáltatásban (EKS)

Alapértelmezés szerint a EKS tárolási osztálya, `gp2` a szolgáltatás típusa pedig `LoadBalancer` .

A következő parancs futtatásával hozza létre az adatkezelőt a megadott EKS-telepítési profil használatával.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

A parancs futtatása után folytassa a következővel: a [létrehozási állapot figyelése](#monitoring-the-creation-status).

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Létrehozás a Google Cloud Kubernetes Engine Service-ben (GKE)

Alapértelmezés szerint a GKE tárolási osztálya, `standard` a szolgáltatás típusa pedig `LoadBalancer` .

A következő parancs futtatásával hozza létre az adatkezelőt a megadott GKE-telepítési profil használatával.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

A parancs futtatása után folytassa a következővel: a [létrehozási állapot figyelése](#monitoring-the-creation-status).

## <a name="monitoring-the-creation-status"></a>A létrehozási állapot figyelése

A vezérlő létrehozása több percet is igénybe vehet. A következő parancsokkal figyelheti a folyamatot egy másik terminál ablakban:

> [!NOTE]
>  Az alábbi parancsok azt feltételezik, hogy létrehozott egy adatkezelőt és egy Kubernetes-névteret a névvel `arc` . Ha más névtér-vagy adatvezérlő-nevet használt, akkor a nevet lecserélheti `arc` a nevét.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Egy adott Pod létrehozási állapotát is megtekintheti egy, az alábbihoz hasonló parancs futtatásával. Ez különösen hasznos az esetleges problémák elhárításához.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>A létrehozási problémák elhárítása

Ha problémákat tapasztal a létrehozással kapcsolatban, tekintse meg a [hibaelhárítási útmutatót](troubleshoot-guide.md).