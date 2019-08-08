---
title: Kubernetes-fürt létrehozása az Azure Kubernetes Service-szel (AKS) és a Terraformmal
description: Oktatóanyag, amely bemutatja, hogyan hozhat létre egy Kubernetes-fürtöt az Azure Kubernetes Service-szel és a Terraformmal
services: terraform
ms.service: azure
keywords: terraform, devops, virtuális gép, azure, kubernetes
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/04/2018
ms.openlocfilehash: 257a2d78a54e292faecda836811f0a58fabd584d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854506"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-and-terraform"></a>Kubernetes-fürt létrehozása az Azure Kubernetes Service és a Terraform segítségével
Az [Azure Kubernetes Service (AKS)](/azure/aks/) felügyeli az üzemeltetett Kubernetes környezetet, lehetővé téve a tárolóalapú alkalmazások gyors és egyszerű üzembe helyezését és felügyeletét tárolóvezénylési szakértelem nélkül is. Ezenkívül a folyamatban lévő műveletek és karbantartás terhét is megszünteti az erőforrások igény szerinti kiépítésével, frissítésével és méretezésével anélkül, hogy offline állapotba kellene helyezni az alkalmazásait.

Ebben az oktatóanyagban megtudhatja, hogyan végezheti el az alábbi feladatokat a [Kubernetes-fürt](https://www.redhat.com/en/topics/containers/what-is-kubernetes) [Terraformmal](https://terraform.io) és AKS-sel történő létrehozása során:

> [!div class="checklist"]
> * Kubernetes-fürt meghatározása HCL (HashiCorp Language) használatával
> * Kubernetes-fürt létrehozása Terraformmal és AKS-sel
> * Kubernetes-fürt rendelkezésre állásának tesztelése a kubectl eszközzel

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.

- **Terraform konfigurálása**: Kövesse a cikk utasításait, [Terraform és konfigurálja az Azure-hoz való hozzáférést](/azure/virtual-machines/linux/terraform-install-configure)

- **Azure egyszerű szolgáltatás**: Kövesse a cikk **egyszerű szolgáltatásnév létrehozása** című szakaszának utasításait, és [hozzon létre egy Azure-SZOLGÁLTATÁSNEVET az Azure CLI-vel](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Jegyezze fel az appId, a displayName, a password és a tenant értékét.

## <a name="create-the-directory-structure"></a>A könyvtárstruktúra létrehozása
Az első lépés a könyvtár létrehozása, amely a feladathoz tartozó Terraform konfigurációs fájlokat tárolja.

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. Nyissa meg az [Azure Cloud Shellt](/azure/cloud-shell/overview). Ha még nem választott ki környezetet, válassza a **Bash** környezetet.

    ![Cloud Shell-parancssor](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Lépjen be a `clouddrive` könyvtárba.

    ```bash
    cd clouddrive
    ```

1. Hozzon létre egy `terraform-aks-k8s` nevű könyvtárat.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Lépjen be az új könyvtárba:

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>Az Azure-szolgáltató deklarálása
Hozza létre az Azure-szolgáltatót deklaráló Terraform konfigurációs fájlt.

1. Hozzon létre egy `main.tf` nevű fájlt a Cloud Shellben.

    ```bash
    vi main.tf
    ```

1. Az I billentyű lenyomásával lépjen beszúrási módba.

1. Másolja az alábbi kódot a szerkesztőbe:

    ```JSON
    provider "azurerm" {
        version = "~>1.5"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. A beszúrás módból az **Esc** billentyűvel léphet ki.

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

## <a name="define-a-kubernetes-cluster"></a>Kubernetes-fürt meghatározása
Hozza létre a Kubernetes-fürt erőforrásait deklaráló Terraform konfigurációs fájlt.

1. Hozzon létre egy `k8s.tf` nevű fájlt a Cloud Shellben.

    ```bash
    vi k8s.tf
    ```

1. Az I billentyű lenyomásával lépjen beszúrási módba.

1. Másolja az alábbi kódot a szerkesztőbe:

    ```JSON
    resource "azurerm_resource_group" "k8s" {
        name     = "${var.resource_group_name}"
        location = "${var.location}"
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = "${var.log_analytics_workspace_location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        sku                 = "${var.log_analytics_workspace_sku}"
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = "${azurerm_log_analytics_workspace.test.location}"
        resource_group_name   = "${azurerm_resource_group.k8s.name}"
        workspace_resource_id = "${azurerm_log_analytics_workspace.test.id}"
        workspace_name        = "${azurerm_log_analytics_workspace.test.name}"

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = "${var.cluster_name}"
        location            = "${azurerm_resource_group.k8s.location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        dns_prefix          = "${var.dns_prefix}"

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = "${file("${var.ssh_public_key}")}"
            }
        }

        agent_pool_profile {
            name            = "agentpool"
            count           = "${var.agent_count}"
            vm_size         = "Standard_DS1_v2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = "${var.client_id}"
            client_secret = "${var.client_secret}"
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
            }
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    Az előző kód megadja a fürt nevét, helyét és az erőforráscsoport nevét. Emellett a dns_prefix érték is be lesz állítva. (Ez a fürt eléréséhez használt teljes tartománynév egy része.)

    A **linux_profile** rekord lehetővé teszi, hogy konfigurálja azokat a beállításokat, amelyek engedélyezik az SSH-bejelentkezést a munkavégző csomópontokra.

    Az AKS-sel csak a munkavégző csomópontokért kell fizetnie. Az **agent_pool_profile** rekord ezen munkavégző csomópontok részleteit konfigurálja. Az **agent_pool_profile record** tartalmazza a létrehozandó munkavégző csomópontok számát és a munkavégző csomópontok típusát. Ha a jövőben a fürt vertikális felskálázásra vagy leskálázásra lesz szükség, módosítsa a rekord **count** (darabszám) értékét.

1. A beszúrás módból az **Esc** billentyűvel léphet ki.

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

## <a name="declare-the-variables"></a>Változók deklarálása

1. Hozzon létre egy `variables.tf` nevű fájlt a Cloud Shellben.

    ```bash
    vi variables.tf
    ```

1. Az I billentyű lenyomásával lépjen beszúrási módba.

1. Másolja az alábbi kódot a szerkesztőbe:

    ```JSON
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. A beszúrás módból az **Esc** billentyűvel léphet ki.

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

## <a name="create-a-terraform-output-file"></a>Terraform kimeneti fájl létrehozása
A [Terraform-kimenetek](https://www.terraform.io/docs/configuration/outputs.html) lehetővé teszik, hogy megadja azokat az értékeket, amelyek ki lesznek emelve a felhasználó számára a Terraform-tervek alkalmazásakor, és lekérdezhetők a `terraform output` paranccsal. Ebben a szakaszban létrehozza a kimeneti fájlt, amellyel hozzáférhet a fürthöz a [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) használatával.

1. Hozzon létre egy `output.tf` nevű fájlt a Cloud Shellben.

    ```bash
    vi output.tf
    ```

1. Az I billentyű lenyomásával lépjen beszúrási módba.

1. Másolja az alábbi kódot a szerkesztőbe:

    ```JSON
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. A beszúrás módból az **Esc** billentyűvel léphet ki.

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Az Azure Storage beállítása Terraform-állapot tárolásához
A Terraform helyileg követi nyomon az állapotot a `terraform.tfstate` fájlon keresztül. Ez a minta jól működik egy egyszemélyes környezetben. A gyakorlatban is használható többszemélyes környezetben az [Azure Storage tárfiókot](/azure/storage/) használó kiszolgálón kell nyomon követni az állapotot. Ebben a szakaszban a Storage-fiók szükséges információit (a fióknevet és fiókkulcsot) kéri le, és létrehoz egy Storage-tárolót amelyben a Terraform állapotinformációit tárolja a rendszer.

1. Az Azure Portalon a bal oldali menüben válassza a **Minden szolgáltatás** elemet.

1. Válassza a **Tárfiókok** lehetőséget.

1. A **Tárfiókok** lapon válassza ki annak a tárfióknak nevét, amelyben a Terraform fogja tárolni az állapotot. Használhatja például azt a tárfiókot is, amely a Cloud Shell első megnyitásakor jött létre.  A Cloud Shell által létrehozott tárfiók neve általában `cs` értékkel kezdődik, amelyet számok és betűk véletlenszerű sorozata követ. **Jegyezze meg a kiválasztott tárfiók nevét, mert később szükség lesz rá.**

1. A tárfiók lapon válassza a **Hozzáférési kulcsok** lehetőséget.

    ![Tárfiók menüje](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Jegyezze fel a **key1** **key** értékét. (A kulcs jobb oldalán található ikonra kattintva a vágólapra másolhatja az értéket.)

    ![Tárfiók hozzáférési kulcsa](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. A Cloud Shellben hozzon létre egy tárolót az Azure Storage-tárfiókban (cserélje le a &lt;YourAzureStorageAccountName> és a &lt;YourAzureStorageAccountAccessKey> helyőrzőket az Azure Storage-tárfiók megfelelő értékeire).

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Kubernetes-fürt létrehozása
Ez a szakasz ismerteti, hogyan használható a `terraform init` parancs az előző szakaszokban létrehozott konfigurációs fájlokat meghatározó erőforrások létrehozásához.

1. A Cloud Shellben inicializálja a Terraformot (cserélje le a &lt;YourAzureStorageAccountName> és a &lt;YourAzureStorageAccountAccessKey> helyőrzőket az Azure Storage-tárfiók megfelelő értékeire).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    A `terraform init` parancs megjeleníti a háttérrendszer és a szolgáltató beépülő modul sikeres inicializálását:

    ![A „terraform init” eredményeit mutató példa](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Exportálja az egyszerű szolgáltatásnév hitelesítő adatait. Cserélje le a &lt;your-client-id> és a &lt;your-client-secret> helyőrzőket a szolgáltatásnévvel társított **alkalmazásazonosítóval**, illetve **jelszóval**.

    ```bash
    export TF_VAR_client_id=<your-client-id>
    export TF_VAR_client_secret=<your-client-secret>
    ```

1. Futtassa a `terraform plan` parancsot az infrastruktúra elemeit meghatározó Terraform-terv létrehozásához. 

    ```bash
    terraform plan -out out.plan
    ```

    A `terraform plan` parancs megjeleníti azokat az erőforrásokat, amelyek a `terraform apply` parancs futtatásakor jönnek létre:

    ![A „terraform plan” eredményeit mutató példa](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Futtassa a `terraform apply` parancsot a Kubernetes-fürtöt létrehozó terv alkalmazásához. A Kubernetes-fürt létrehozásának folyamata néhány percet igénybe vehet, ez a Cloud Shell-munkamenet időtúllépését eredményezi. Ha a Cloud Shell munkamenet időtúllépés miatt meghaladta az időkorlátot, kövesse a "helyreállítás Cloud Shell időtúllépésből" című szakasz lépéseit az oktatóanyag befejezéséhez.

    ```bash
    terraform apply out.plan
    ```

    A `terraform apply` parancs megjeleníti a konfigurációs fájlokban meghatározott erőforrások létrehozásának eredményét:

    ![A „terraform apply” eredményeit mutató példa](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. A Azure Portal a bal oldali menüben a **minden szolgáltatás** elemre kattintva megtekintheti az új Kubernetes-fürthöz létrehozott erőforrásokat.

    ![Cloud Shell-parancssor](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Helyreállítás a Cloud Shell időtúllépéséből
Ha a Cloud Shell-munkamenet túllépi az időkorlátot, az alábbi lépéseket hajthatja végre a helyreállításhoz:

1. Indítson egy Cloud Shell-munkamenetet.

1. Lépjen a Terraform konfigurációs fájljait tartalmazó könyvtárra.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Futtassa a következő parancsot:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>A Kubernetes-fürt tesztelése
A Kubernetes-eszközök használhatók az újonnan létrehozott fürt teszteléséhez.

1. Kérje le a Kubernetes-konfigurációt a Terraform állapotából, és tárolja el egy fájlban, amelyet a kubectl olvashat.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Állítson be egy környezeti változót, így a kubectl a helyes konfigurációt veszi fel.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Ellenőrizze a fürt állapotát.

    ```bash
    kubectl get nodes
    ```

    Megjelenik a munkavégző csomópontok állapota, és minden csomópont **Ready** (Kész) állapotú, az alábbi képen láthatóhoz hasonlóan:

    ![A kubectl eszközzel ellenőrizheti a Kubernetes-fürt állapotát](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Állapot és naplók monitorozása
Az AKS-fürt létrejöttekor a monitorozás is engedélyezve lett, hogy rögzítse a fürtcsomópontok és a podok állapotmetrikáit. Ezek az állapotmetrikák elérhetők az Azure Portalon. A tároló állapotának figyelésével kapcsolatos további információkért lásd: az [Azure Kubernetes szolgáltatás állapotának figyelése](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerte, hogyan használható a Terraform és az AKS egy Kubernetes-fürt létrehozásához. Íme néhány további segédlet, amelyek segítségével többet tudhat meg a Terraform az Azure-on történő használatáról: 

 [Terraform Hub a Microsoft.com webhelyen](https://docs.microsoft.com/azure/terraform/)  
 [Terraform: Azure szolgáltatói dokumentáció](https://aka.ms/terraform)  
 [Terraform: Azure-szolgáltatói forrás](https://aka.ms/tfgit)  
 [Terraform: Azure-modulok](https://aka.ms/tfmodules)
