---
title: Az Azure hálózati biztonsági csoport (NSG) áthelyezése egy másik Azure-régióba az Azure Portal használatával
description: Azure Resource Manager sablonnal áthelyezheti az Azure hálózati biztonsági csoportot az egyik Azure-régióból a másikba a Azure Portal használatával.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 11b0260eb6c1f5edfb246dcf97a43bdd874bf337
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217277"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Az Azure hálózati biztonsági csoport (NSG) áthelyezése egy másik régióba a Azure Portal használatával

Különböző helyzetekben érdemes áthelyezni a meglévő NSG egyik régióból a másikba. Előfordulhat például, hogy létre szeretne hozni egy NSG ugyanazzal a konfigurációs és biztonsági szabályokkal a teszteléshez. Előfordulhat, hogy a vész-helyreállítási tervezés részeként egy NSG is át szeretne helyezni egy másik régióba.

Az Azure biztonsági csoportok nem helyezhetők át az egyik régióból egy másikba. A NSG meglévő konfigurációs és biztonsági szabályainak exportálásához azonban Azure Resource Manager sablont is használhat.  Ezután egy másik régióban is elvégezheti az erőforrást, ha a NSG sablonba exportálja, módosítja a paramétereket, hogy azok megfeleljenek a célhelynek, majd üzembe helyezi a sablont az új régióban.  A Resource Managerrel és a sablonokkal kapcsolatos további információkért tekintse meg a rövid útmutató [: Azure Resource Manager sablonok létrehozása és telepítése a Azure Portal használatával](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)című témakört.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure-beli hálózati biztonsági csoport abban az Azure-régióban található, amelyről át kívánja helyezni.

- Az Azure-beli hálózati biztonsági csoportok nem helyezhetők át régiók között.  Az új NSG hozzá kell rendelnie a cél régió erőforrásaihoz.

- NSG-konfiguráció exportálásához és sablon üzembe helyezéséhez egy másik régióban lévő NSG létrehozásához szüksége lesz a hálózati közreműködő szerepkörre vagy magasabbra.

- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez az elrendezés tartalmaz, de nem korlátozódik a terheléselosztó, a nyilvános IP-címek és a virtuális hálózatok számára.

- Győződjön meg arról, hogy az Azure-előfizetése lehetővé teszi, hogy NSG hozzon létre a használt célcsoportban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetése elegendő erőforrással rendelkezik a folyamat NSG hozzáadásának támogatásához.  Tekintse meg a következőt: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Előkészítés és áthelyezés
A következő lépések bemutatják, hogyan készítse elő a hálózati biztonsági csoportot a konfigurációs és biztonsági szabály számára egy Resource Manager-sablon használatával, majd helyezze át a NSG konfigurációs és biztonsági szabályait a célként megadott régióba a portál használatával.


### <a name="export-the-template-and-deploy-from-the-portal"></a>A sablon exportálása és üzembe helyezése a portálról

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com)  >  **erőforráscsoporthoz**.
2. Keresse meg a forrás NSG tartalmazó erőforráscsoportot, és kattintson rá.
3. Válassza > **Beállítások**  >  **Exportálás sablon** lehetőséget.
4. A **sablon exportálása** panelen válassza a **telepítés** lehetőséget.
5. Kattintson a **sablon**  >  **szerkesztése paraméterek** lehetőségre, hogy megnyissa a **parameters.js** fájlt az online szerkesztőben.
6. A NSG-név paraméterének szerkesztéséhez módosítsa a **Value** tulajdonságot a **Paraméterek** alatt:

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Módosítsa a forrás NSG értékét a szerkesztőben a célként kiválasztott NSG. Győződjön meg arról, hogy a nevet idézőjelek közé adja.

8.  Kattintson a **Mentés** gombra a szerkesztőben.

9.  Kattintson **a sablon**  >  **szerkesztése** gombra a **template.js** fájl megnyitásához az online szerkesztőben.

10. Ha módosítani szeretné a NSG-konfigurációt és a biztonsági szabályokat áthelyező célhelyet, módosítsa a **Location (hely** ) tulajdonságot az online szerkesztőben található **erőforrások** területen:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. A régióbeli hely kódjának beszerzéséhez tekintse meg az [Azure-helyeket](https://azure.microsoft.com/global-infrastructure/locations/).  A régió kódja a régió neve szóközök nélkül, **Közép-USA**  =  **CentralUS**.

12. A sablon egyéb paramétereit is módosíthatja, és a követelményektől függően választható:

    * **Biztonsági szabályok** – a **securityRules** **template.js** szakasz szabályok hozzáadásával vagy eltávolításával szerkesztheti, hogy mely szabályok legyenek telepítve a cél NSG:

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      A cél NSG lévő szabályok hozzáadásának vagy eltávolításának befejezéséhez az alábbi példa formátuma szerint az egyéni szabályok típusait is szerkesztenie kell a fájl **template.js** végén:

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. Kattintson a **Save (Mentés** ) gombra az online szerkesztőben.

14. Az   >  alapértékek **előfizetése** lehetőségre kattintva válassza ki azt az előfizetést, amelyben a cél NSG telepíteni fogja.

15. Az alapszintű erőforráscsoport **elemre** kattintva  >   válassza ki azt az erőforráscsoportot, amelyben a cél NSG telepíteni fogja.  Az **új létrehozása** lehetőségre kattintva létrehozhat egy új erőforráscsoportot a cél NSG.  Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő NSG forrásoldali erőforráscsoport.

16. Győződjön   >  meg arról, hogy az alapértékek **helye** arra a célhelyre van beállítva, ahol a NSG telepíteni kívánja.

17. Ellenőrizze a **Beállítások** területen, hogy a név megegyezik-e a fenti Parameters Editorban megadott névvel.

18. Jelölje be a jelölőnégyzetet a **feltételek és KIkötések** területen.

19. Kattintson a **vásárlás** gombra a célként megadott hálózati biztonsági csoport telepítéséhez.

## <a name="discard"></a>Elvetés

Ha el szeretné vetni a cél NSG, törölje a cél NSG tartalmazó erőforráscsoportot.  Ehhez válassza ki az erőforráscsoportot az irányítópulton a portálon, és válassza a **Törlés** lehetőséget az Áttekintés oldal tetején.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások végrehajtásához és a NSG áthelyezésének befejezéséhez törölje a forrás NSG vagy az erőforráscsoportot. Ehhez válassza ki a hálózati biztonsági csoportot vagy erőforráscsoportot az irányítópulton a portálon, és válassza a **Törlés** lehetőséget az egyes oldalak tetején.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure-beli hálózati biztonsági csoportot helyezett át az egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](../site-recovery/azure-to-azure-tutorial-migrate.md)