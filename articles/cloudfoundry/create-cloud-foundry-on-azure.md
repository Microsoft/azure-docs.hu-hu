---
title: Pivotal Cloud Foundry-fürt létrehozása az Azure-ban
description: Megtudhatja, hogyan állíthatja be az Azure-beli Pivotal Cloud Foundry (PCF) fürt kiépítéséhez szükséges paramétereket.
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 3444d6c62b5e36b7e68cfaf6da1ec534e2ea4ec6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551453"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Pivotal Cloud Foundry-fürt létrehozása az Azure-ban

Ez az oktatóanyag gyors lépéseket biztosít a Pivotal Cloud Foundry (PCF) fürt Azure-beli üzembe helyezéséhez szükséges paraméterek létrehozásához és létrehozásához. A Pivotal Cloud Foundry megoldás megkereséséhez végezzen keresést az Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)-en.

![Pivotal Cloud Foundry keresése az Azure-ban](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Nyilvános SSH-kulcs létrehozása

A nyilvános Secure Shell-(SSH-) kulcs többféleképpen is létrehozható Windows, Mac vagy Linux használatával.

```Bash
ssh-keygen -t rsa -b 2048
```

További információ: SSH- [kulcsok használata az Azure-ban Windowson](../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

> [!NOTE]
>
> Egyszerű szolgáltatásnév létrehozásához tulajdonosi fiókra van szükség. Emellett parancsfájlt is írhat az egyszerű szolgáltatás létrehozásának automatizálására. Használhatja például az Azure CLI az [ad SP Create-for-RBAC](/cli/azure/ad/sp).

1. Jelentkezzen be Azure-fiókjába.

    ```azurecli
    az login
    ```

    ![Azure CLI-bejelentkezés](media/deploy/az-login-output.png )
 
    Másolja az "id" értéket előfizetés- **azonosítóként**, és másolja a "tenantId" értéket a későbbi használatra.

2. Állítsa be az alapértelmezett előfizetést ehhez a konfigurációhoz.

    ```azurecli
    az account set -s {id}
    ```

3. Hozzon létre egy Azure Active Directory alkalmazást a PCF. Egyedi alfanumerikus jelszót kell megadnia. Tárolja a jelszót, amelyet később **clientSecret** használni.

    ```azurecli
    az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}
    ```

    Másolja az "appId" értéket a kimenetben, amelyet később a **clientID** használ.

    > [!NOTE]
    >
    > Válassza ki a saját alkalmazás kezdőlapját és azonosítójának URI-JÁT, például: http \: //www \. contoso.com.

4. Hozzon létre egy egyszerű szolgáltatást az új alkalmazás-AZONOSÍTÓval.

    ```azurecli
    az ad sp create --id {appId}
    ```

5. Állítsa be az egyszerű szolgáltatás jogosultságát közreműködői szerepkörre.

    ```azurecli
    az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"
    ```

    Emellett használhatja a

    ```azurecli
    az role assignment create --assignee {service-principal-name} --role "Contributor"
    ```

    ![Egyszerű szolgáltatás szerepkör-hozzárendelése](media/deploy/svc-princ.png )

6. Győződjön meg arról, hogy sikeresen bejelentkezhet a szolgáltatásba az alkalmazás-azonosító, a jelszó és a bérlő azonosítója használatával.

    ```azurecli
    az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}
    ```

7. Hozzon létre egy. JSON fájlt a következő formátumban. Használja a korábban átmásolt **előfizetés-azonosítót**, a **tenantID**, a **clientID** és a **clientSecret** értékeket. Mentse a fájlt.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>A Pivotal hálózati jogkivonat beszerzése

1. Regisztráljon vagy jelentkezzen be a [Pivotal hálózati](https://network.pivotal.io) fiókjába.
2. Válassza ki a profil nevét az oldal jobb felső sarkában. Válassza a **Profil szerkesztése** lehetőséget.
3. Görgessen a lap aljára, és másolja az **örökölt API-jogkivonat** értékét. Ez az érték a **Pivotal hálózati jogkivonat** értéke, amelyet később használ.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>A Cloud Foundry-fürt kiépítése az Azure-ban

Most már rendelkezik a [pivotal Cloud Foundry-fürt Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)-beli üzembe helyezéséhez szükséges összes paraméterrel.
Adja meg a paramétereket, és hozza létre a PCF-fürtöt.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Ellenőrizze a központi telepítést, és jelentkezzen be a Pivotal Ops-kezelőbe

1. A PCF-fürtben egy telepítési állapot látható.

    ![Azure-beli üzembe helyezés állapota](media/deploy/deployment.png )

2. A bal oldali navigációs sávon a PCF Ops-kezelőhöz tartozó hitelesítő adatok beszerzéséhez kattintson a **központi telepítések** hivatkozásra. Válassza ki a **központi telepítés nevét** a következő oldalon.
3. A bal oldali navigációs sávon válassza a **kimenetek** hivatkozást, amely MEGJELENÍTI a PCF Ops-kezelő URL-címét, felhasználónevét és jelszavát. A "OPSMAN-FQDN" érték az URL-cím.
 
    ![Cloud Foundry központi telepítés kimenete](media/deploy/deploy-outputs.png )
 
4. Indítsa el az URL-címet egy böngészőben. A bejelentkezéshez adja meg az előző lépésben megadott hitelesítő adatokat.

    ![Pivotal bejelentkezési oldal](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Ha az Internet Explorer böngésző a "hely nem biztonságos" figyelmeztető üzenet miatt sikertelen, válassza a **További információk** lehetőséget, és lépjen a weblapra. A Firefox esetében válassza az **Advance** lehetőséget, majd a folytatáshoz adja hozzá a minősítést.

5. A PCF Ops Manager megjeleníti az üzembe helyezett Azure-példányokat. Most már üzembe helyezheti és kezelheti az alkalmazásait.
               
    ![Üzembe helyezett Azure-példány a Pivotal](media/deploy/ops-mgr.png )
