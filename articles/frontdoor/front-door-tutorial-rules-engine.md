---
title: 'Oktatóanyag: szabályok konfigurálása motor – Azure bejárati ajtó'
description: Ez a cikk bemutatja, hogyan konfigurálhatja a szabályok motorját a Azure Portal és a parancssori felületen.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4f81d8a75cab38bccbfe0ca0f6405baafeb83459
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068183"
---
# <a name="tutorial-configure-your-rules-engine"></a>Oktatóanyag: a szabályok motorjának konfigurálása

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre a szabályok motorjának konfigurációját és az első szabályt a Azure Portal és a parancssori felületen egyaránt. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - Konfigurálja a szabályok motort a portál használatával.
> - Szabályok motor konfigurálása az Azure CLI-vel

## <a name="prerequisites"></a>Előfeltételek

* Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először létre kell hoznia egy Front Doort. További információkért lásd a [Rövid útmutató: Front Door létrehozása](quickstart-create-front-door.md) szakaszt.

## <a name="configure-rules-engine-in-azure-portal"></a>Szabályok konfigurálása motor a Azure Portal
1. A bejárati ajtó erőforrásában lépjen a **Beállítások** elemre, és válassza a **szabály motor konfigurálása** lehetőséget. Kattintson a **Hozzáadás** gombra, adja meg a konfiguráció nevét, és kezdje el létrehozni az első szabályok motor konfigurációját.

    ![A bejárati ajtó beállításai menü](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

1. Az első szabály létrehozásához kattintson a **szabály hozzáadása** elemre. Ezután a **feltétel hozzáadása** vagy a **művelet hozzáadása** lehetőségre kattintva megadhatja a szabályt.
    
    > [!NOTE]
    >- Ha törölni szeretne egy feltételt vagy műveletet a szabályból, használja a szemetet az adott feltétel vagy művelet jobb oldalán.
    > - Egy olyan szabály létrehozásához, amely az összes bejövő forgalomra vonatkozik, ne határozzon meg feltételeket.
    > - Ha meg szeretné szüntetni a szabályok kiértékelését az első egyeztetési feltétel teljesülése után, akkor a **hátralévő szabály kiértékelésének leállítása** gombra kell bejelentkeznie. Ha be van jelölve, és egy adott szabály összes egyeztetési feltétele teljesül, a konfigurációban maradó többi szabály nem lesz végrehajtva.  

    ![Szabályok motorjának konfigurációja](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

1. Határozza meg a konfiguráción belüli szabályok prioritását a feljebb, a lejjebb, és a felülre lépés gombokkal. A prioritás növekvő sorrendben van megadva, ami azt jelenti, hogy az elsőként felsorolt szabály a legfontosabb szabály.

1. Miután létrehozott egy vagy több szabályt, nyomja meg a **Mentés** gombot. Ez a művelet létrehozza a szabályok motorjának konfigurációját.

1. Miután létrehozott egy vagy több konfigurációt, társítsa a szabályok motorjának konfigurációját egy útválasztási szabállyal. Habár egy konfiguráció számos útválasztási szabályra alkalmazható, az útválasztási szabályok csak egy szabály-végrehajtó konfigurációt tartalmazhatnak. A társítás létrehozásához nyissa meg a bevezető **ajtó tervezői**  >  **útvonalának szabályait**. Válassza ki azt az útválasztási szabályt, amelyhez hozzá szeretné adni a szabályok motorjának konfigurációját, lépjen a **Route details**  >  **Rules Engine Configuration** elemre, és válassza ki a hozzárendelni kívánt konfigurációt.

    ![Konfigurálás útválasztási szabályhoz](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Szabályok konfigurálása motor az Azure CLI-ben

1. Ha még nem tette meg, telepítse az [Azure CLI](/cli/azure/install-azure-cli)-t. Adja hozzá az "előtérben" kiterjesztést: az Extension Add--Name bejárati ajtót. Ezután jelentkezzen be, és váltson az előfizetésre az az Account set--előfizetés <name_or_Id>.

1. Első lépésként hozzon létre egy szabályt motort – ez a példa egy, a fejléc-alapú művelettel és egy egyeztetési feltétellel rendelkező szabályt mutat be. 

    ```azurecli-interactive
    az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
    ```

1. Sorolja fel az összes szabályt. 

    ```azurecli-interactive
    az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
    ```

1. Adja meg a továbbítási útvonal felülbírálási műveletét. 

    ```azurecli-interactive
    az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
    ```

1. Egy szabály összes műveletének listázása. 

    ```azurecli-interactive
    az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
    ```

1. Szabály-végrehajtói konfiguráció csatolása útválasztási szabályhoz.  

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
    ```

1. Szabályok leválasztása motor. 

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
    ```

További információkért tekintse meg az AFD Rules Engine parancsainak teljes listáját [itt](/cli/azure/ext/front-door/network/front-door/rules-engine).   

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben konfigurálta és társította a szabályok motorjának beállításait az útválasztási szabályokhoz. Ha már nem szeretné, hogy a szabályok Engine-konfiguráció a bejárati ajtóhoz legyen társítva, a következő lépések végrehajtásával távolíthatja el a konfigurációt:

1. Ha a szabály motorjának neve melletti három pontra kattint, szüntesse meg a szabály-végrehajtó konfigurációjának útválasztási szabályait.

    :::image type="content" source="./media/front-door-rules-engine/front-door-rule-engine-routing-association.png" alt-text="Útválasztási szabályok hozzárendelése":::

1. Törölje az összes útválasztási szabályt, amelyhez ez a szabályrendszer-konfiguráció hozzá van rendelve, majd kattintson a Mentés gombra.

    :::image type="content" source="./media/front-door-rules-engine/front-door-routing-rule-association.png" alt-text="Útválasztási szabály társítása":::

1. Most törölheti a szabály motorjának konfigurációját a bejárati ajtóról.

    :::image type="content" source="./media/front-door-rules-engine/front-door-delete-rule-engine-configuration.png" alt-text="Szabály-végrehajtó konfigurációjának törlése":::

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Szabály-végrehajtó konfiguráció létrehozása
* A konfigurációt a bejárati ajtó útválasztási szabályaihoz kell rendelni.

Ha meg szeretné tudni, hogyan adhat hozzá biztonsági fejléceket a szabály-végrehajtóval, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Biztonsági fejlécek szabályok motorral](front-door-security-headers.md)