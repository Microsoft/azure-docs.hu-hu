---
title: Az Azure arc engedélyezése a Kubernetes-on Azure Stack Edge GPU-eszközön | Microsoft Docs
description: Ismerteti, hogyan engedélyezhető az Azure arc egy meglévő Kubernetes-fürtön az Azure Stack Edge GPU-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 5cd163b4c7514507d2a0563f1254c83dd22a3af2
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268193"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-gpu-device"></a>Az Azure arc engedélyezése a Kubernetes-fürtön az Azure Stack Edge GPU-eszközön

Ez a cikk bemutatja, hogyan engedélyezheti az Azure arc szolgáltatást egy meglévő Kubernetes-fürtön az Azure Stack Edge-eszközön. 

Ez az eljárás azok számára készült, akik áttekintették a Kubernetes számítási feladatait [Azure stack Edge-eszközön](azure-stack-edge-gpu-kubernetes-workload-management.md) , és ismeri az [Azure arc-kompatibilis Kubernetes (előzetes verzió)](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)fogalmait?


## <a name="prerequisites"></a>Előfeltételek

Mielőtt engedélyezi az Azure arc használatát a Kubernetes-fürtön, győződjön meg arról, hogy végrehajtotta a következő előfeltételeket az Azure Stack Edge-eszközön és az eszköz eléréséhez használni kívánt ügyfélen:

### <a name="for-device"></a>Az eszköz esetén

1. A hitelesítő adatok egy 1 csomópontos Azure Stack peremhálózati eszközhöz vannak bejelentkezett.
    1. Az eszköz aktiválva van. Lásd: [az eszköz aktiválása](azure-stack-edge-gpu-deploy-activate.md).
    1. Az eszközön a Azure Portal-n keresztül konfigurált számítási szerepkör és egy Kubernetes-fürt van konfigurálva. Lásd: [számítás konfigurálása](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Tulajdonosi hozzáféréssel rendelkezik az előfizetéshez. Erre a hozzáférésre szüksége lesz a szolgáltatás szerepkör-hozzárendelési lépése során.
 

### <a name="for-client-accessing-the-device"></a>Az eszközt elérő ügyfél

1. Van egy Windows-ügyfélrendszer, amely az Azure Stack Edge-eszköz elérésére szolgál majd.
  
    - Az ügyfél Windows PowerShell 5,0-es vagy újabb verzióját futtatja. A Windows PowerShell legújabb verziójának letöltéséhez nyissa meg a következőt: [install Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7).
    
    - Bármely más ügyfél [támogatott operációs rendszerrel](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) is rendelkezhet. Ez a cikk a Windows-ügyfelek használatakor követendő eljárást ismerteti. 
    
1. Végrehajtotta a következő témakörben leírt eljárást: [Azure stack Edge-eszközön található Kubernetes-fürt elérése](azure-stack-edge-gpu-create-kubernetes-cluster.md). A következőket teheti:
    
    - `kubectl`Az ügyfélre telepítve  <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - Győződjön meg arról, hogy az `kubectl` ügyfél verziószáma nem több, mint egy olyan verzió, amely az Azure stack Edge-eszközön futó Kubernetes-főverzión fut. 
      - Ezzel a paranccsal `kubectl version` ellenőrizhető az ügyfélen futó kubectl verziója. Jegyezze fel a teljes verziót.
      - Az Azure Stack Edge-eszköz helyi felhasználói felületén lépjen a **szoftverfrissítés** elemre, és jegyezze fel a Kubernetes-kiszolgáló verziószámát. 
    
        ![Kubernetes-kiszolgáló verziószámának ellenőrzése](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - Ellenőrizze, hogy a két verzió kompatibilis-e. 

<!-- az cli version requirements-->

## <a name="register-kubernetes-resource-providers"></a>Kubernetes erőforrás-szolgáltató regisztrálása

Mielőtt a Kubernetes-fürtöt az Azure arc használatával konfigurálja, engedélyeznie kell és regisztrálnia kell az `Microsoft.Kubernetes` `Microsoft.KubernetesConfiguration` előfizetését. 

1. Az erőforrás-szolgáltató engedélyezéséhez a Azure Portal lépjen az üzembe helyezéshez használni kívánt előfizetésre. Nyissa meg az **erőforrás-szolgáltatókat**. 
1. A jobb oldali ablaktáblában keresse meg a hozzáadni kívánt szolgáltatókat. Ebben a példában a `Microsoft.Kubernetes` és a `Microsoft.KubernetesConfiguration` .

    ![Kubernetes erőforrás-szolgáltató regisztrálása](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. Válasszon ki egy erőforrás-szolgáltatót, és a parancssáv felső részén válassza a **regisztráció**lehetőséget. A regisztráció több percet is igénybe vehet. 

    ![Kubernetes erőforrás-szolgáltató regisztrálása](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. Frissítse a felhasználói felületet addig, amíg nem látja, hogy az erőforrás-szolgáltató regisztrálva van. Ismételje meg a folyamatot mindkét erőforrás-szolgáltató esetében.
    
    ![Kubernetes erőforrás-szolgáltató regisztrálása](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

Az erőforrás-szolgáltatókat a használatával is regisztrálhatja `az cli` . További információ: [a két szolgáltató regisztrálása az Azure arc használatára képes Kubernetes](../azure-arc/kubernetes/connect-cluster.md#register-the-two-providers-for-azure-arc-enabled-kubernetes)

## <a name="create-service-principal-assign-role"></a>Egyszerű szolgáltatásnév létrehozása, szerepkör kiosztása

1. Győződjön meg arról, hogy rendelkezik az `Subscription ID` Azure stack Edge szolgáltatás erőforrás-telepítéséhez használt erőforráscsoport nevével. Az előfizetés-azonosító beszerzéséhez nyissa meg az Azure Stack Edge-erőforrást a Azure Portal. Navigáljon az **áttekintés > Essentials**elemre.

    ![Előfizetés-azonosító lekérése](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    Az erőforráscsoport nevének beszerzéséhez lépjen a **Tulajdonságok**elemre.

    ![Erőforráscsoport nevének lekérése](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. Egyszerű szolgáltatásnév létrehozásához használja a következő parancsot a on `az cli` .

    `az as sp create-for-rbac --skip assignment --name "<Informative name for service principal>"`  

    A alkalmazásba való bejelentkezéssel kapcsolatos információkért `az cli` [indítsa el a Cloud Shell a Azure Portal](../cloud-shell/quickstart-powershell.md?view=azure-cli-latest#start-cloud-shell)

    Például: 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. Jegyezze fel a,, `appID` `name` és a `password` `tenantID` következő parancs bemenetként való használatát.

1. Az új egyszerű szolgáltatás létrehozása után rendeljen hozzá egy `Kubernetes Cluster - Azure Arc Onboarding` szerepkört az újonnan létrehozott rendszerbiztonsági tag számára. Ez egy beépített Azure-szerepkör (használja a parancsban található szerepkör-azonosítót) korlátozott engedélyekkel. Használja az alábbi parancsot:

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    Például:
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    Az egyszerű szolgáltatásnév létrehozásával és a szerepkör-hozzárendelés végrehajtásával kapcsolatos további információkért tekintse meg az [Azure arc-kompatibilis](https://docs.microsoft.com/azure/azure-arc/kubernetes/create-onboarding-service-principal)bevezetési szolgáltatás létrehozása című témakör lépéseit.


## <a name="enable-arc-on-kubernetes-cluster"></a>Az Arc engedélyezése a Kubernetes-fürtön

Az alábbi lépéseket követve konfigurálhatja a Kubernetes-fürtöt az Azure arc felügyeletéhez:

1. [Kapcsolódjon az eszköz PowerShell-felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) .

1. Típus:

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`

    Az Azure arc Azure Stack Edge-eszközön való üzembe helyezéséhez győződjön meg arról, hogy az [Azure arc támogatott régióját](../azure-arc/kubernetes/overview.md#supported-regions)használja. Az Azure arc jelenleg előzetes verzióban érhető el. 
    
    Alább bemutatunk egy példát:
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed" -ResourceGroupName "myaserg1" -ResourceName "myasetestresarc" -Location "WestEurope" -TenantId "72f988bf-86f1-41af-91ab-2d7cd011db47" -ClientId "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b" -ClientSecret "<password>"
        [10.128.44.240]: PS>
    ```
    
    A Azure Portal egy erőforrást az előző parancsban megadott névvel kell létrehozni.

    ![Ugrás az Azure arc-erőforrásra](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Annak ellenőrzéséhez, hogy az Azure arc sikeresen engedélyezve van-e, futtassa a következő parancsot a PowerShell felületéről:

    `kubectl get deployments -n azure-arc`

    Ez a parancs megkeresi az `azure-arc` Azure arc-nak megfelelő névtérben üzembe helyezett alkalmazásokat.

    Íme egy minta kimenet, amely a névtérben a Kubernetes-fürtön üzembe helyezett Azure arc-ügynököket mutatja be `azure-arc` . 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    A névtérben a Kubernetes-fürtön futó hüvelyek listáját is lekérheti `azure-arc` . A pod egy alkalmazás-tároló vagy folyamat, amely a Kubernetes-fürtön fut. 

    Használja az alábbi parancsot:
    
    `kubectl get pods -n azure-arc`
    
    Íme egy minta kimenet.
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


Ahogy az előző kimenetben látható, az Azure arc-kompatibilis Kubernetes néhány ügynökből (kezelőből) áll, amelyek a névtérben üzembe helyezett fürtön futnak `azure-arc` .

- `config-agent`: a fürtön alkalmazott forrás-vezérlési konfigurációs erőforrások, valamint a frissítések megfelelőségi állapotának figyeli a csatlakoztatott fürtöt
- `controller-manager`: az operátorok és az Azure arc-összetevők közötti interakciók
- `metrics-agent`: más ív-ügynökök metrikáinak gyűjtésével biztosíthatja, hogy ezek az ügynökök optimális teljesítményt mutassanak
- `cluster-metadata-operator`: a fürt metaadatait – a fürt verzióját, a csomópontok darabszámát és az Azure arc-ügynök verzióját gyűjti.
- `resource-sync-agent`: szinkronizálja a fent említett fürt metaadatait az Azure-ba
- `clusteridentityoperator`: Az Azure arc-kompatibilis Kubernetes jelenleg támogatja a rendszerhez rendelt identitást. a clusteridentityoperator fenntartja a más ügynökök által az Azure-nal folytatott kommunikációhoz használt felügyelt szolgáltatás-identitás (MSI) tanúsítványát.
- `flux-logs-agent`: a a forrás-ellenőrzési konfiguráció részeként üzembe helyezett Flux-kezelők naplóit gyűjti.
- `connect-agent`: az Azure arc erőforrásának megtárgyalása.

### <a name="remove-arc-from-the-kubernetes-cluster"></a>Ív eltávolítása a Kubernetes-fürtből

Az Azure arc-felügyelet eltávolításához kövesse az alábbi lépéseket:

1. 1. [Kapcsolódjon az eszköz PowerShell-felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) .
2. Típus:

    `Remove-HcsKubernetesAzureArcAgent` 


## <a name="next-steps"></a>További lépések

Az Azure arc üzembe helyezésének megismeréséhez lásd: [állapot nélküli php Vendégkönyv alkalmazás üzembe helyezése az Redis-n keresztül az Azure stack Edge-eszközön](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)
