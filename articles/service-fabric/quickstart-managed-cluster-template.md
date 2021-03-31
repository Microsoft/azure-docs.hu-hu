---
title: Service Fabric felügyelt fürt (előzetes verzió) üzembe helyezése Azure Resource Manager használatával
description: Megtudhatja, hogyan hozhat létre Service Fabric felügyelt fürtöt egy Azure Resource Manager sablonnal
ms.topic: quickstart
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 079e8e3e39bd306332abb43fa2ae1f8cc04e305b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91410494"
---
# <a name="quickstart-deploy-a-service-fabric-managed-cluster-preview-with-an-azure-resource-manager-template"></a>Gyors útmutató: Service Fabric felügyelt fürt üzembe helyezése (előzetes verzió) Azure Resource Manager sablonnal

Service Fabric felügyelt fürtök az Azure Service Fabric fürterőforrás-modell fejlődése, amely egyszerűsíti az üzembe helyezési és a fürtszolgáltatási élményt. Service Fabric felügyelt fürtök egy teljes körűen beágyazott erőforrás, amely lehetővé teszi egyetlen Service Fabric fürterőforrás üzembe helyezését ahelyett, hogy a Service Fabric-fürtöt alkotó összes mögöttes erőforrást telepíteni kellene. Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy Service Fabric felügyelt fürtöt az Azure-beli teszteléshez Azure Resource Manager sablon (ARM-sablon) használatával.

Az oktatóanyagban üzembe helyezett három csomópontos alapszintű SKU-fürt csak oktatási célokra használható (az éles számítási feladatok helyett). További információ:  [Service Fabric felügyelt fürt SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus)-i.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Samples-Service Fabric fürtözött sablonokból](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Basic-SKU-1-NT)származik.

## <a name="create-a-client-certificate"></a>Ügyféltanúsítvány létrehozása

Service Fabric felügyelt fürtök a hozzáférés-vezérlés kulcsaként használják a ügyféltanúsítványt. Ha már rendelkezik olyan ügyféltanúsítványt, amelyet a fürt hozzáférés-vezérléséhez szeretne használni, akkor kihagyhatja ezt a lépést.

Ha új ügyféltanúsítványt kell létrehoznia, kövesse a [tanúsítvány beállítása és lekérése Azure Key Vaultról](../key-vault/certificates/quick-create-portal.md)című témakör lépéseit.

Jegyezze fel a tanúsítvány ujjlenyomatát, mert a következő lépésben a sablon üzembe helyezéséhez szükség lesz rá.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához.

      [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fservice-fabric-cluster-templates%2Fmaster%2FSF-Managed-Basic-SKU-1-NT%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket

    Ebben a rövid útmutatóban adja meg a saját értékeit a következő sablon paramétereinek:

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget. Adja meg az erőforráscsoport egyedi nevét, például *myResourceGroup*, majd kattintson **az OK gombra**.
    * **Hely**: válasszon egy helyet, például **eastus2**. Service Fabric felügyelt fürtök előzetes verziójának támogatott régiói a következők:,,,, `centraluseuap` `eastus2euap` `eastasia` `northeurope` `westcentralus` és `eastus2` .
    * **Fürt neve**: adjon meg egy egyedi nevet a fürtnek, például *mysfcluster*.
    * **Rendszergazdai Felhasználónév**: adjon meg egy nevet a fürtben található mögöttes virtuális gépeken az RDP-hez használt rendszergazda számára.
    * **Rendszergazdai jelszó**: adja meg az RDP-hez a fürtben található mögöttes virtuális gépeken használandó jelszót.
    * **Ügyféltanúsítvány ujjlenyomata**: adja meg a fürt eléréséhez használni kívánt ügyféltanúsítvány ujjlenyomatát. Ha nem rendelkezik tanúsítvánnyal, kövesse [a tanúsítvány beállítása és lekérése](../key-vault/certificates/quick-create-portal.md) saját aláírású tanúsítvány létrehozásához című témakört.
    * **Csomópont típusa neve**: adjon meg egy egyedi nevet a csomópont-típushoz, például *NT1*.
    * **Elfogadom a fenti feltételeket és kikötéseket**: jelölje be ezt a jelölőnégyzetet az egyeztetéshez. 

3. Válassza a **Beszerzés** lehetőséget.

4. A felügyelt Service Fabric-fürt üzembe helyezése néhány percet vesz igénybe. Várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő lépésekre.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

### <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Miután az üzembe helyezés befejeződött, keresse meg a kimenetben Service Fabric Explorer értéket, és nyissa meg a webböngészőben a címeket a fürt Service Fabric Explorer való megtekintéséhez. Ha a rendszer rákérdez a tanúsítványra, használja azt a tanúsítványt, amelyhez az ügyfél ujjlenyomatát megadták a sablonban.

> [!NOTE]
> A központi telepítés kimenetét az Azure Portalon, az erőforráscsoport telepítések lapon találja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a Service Fabric felügyelt fürthöz tartozó erőforráscsoportot. Az erőforráscsoport törlése a Portalon keresztül:

1. Adja meg az erőforráscsoport nevét a portál felső részén található *keresőmezőbe* . Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy felügyelt Service Fabric-fürtöt telepített. A fürtök méretezésével kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Service Fabric felügyelt fürt felskálázása](tutorial-managed-cluster-scale.md)
