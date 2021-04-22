---
title: 'Oktatóanyag: Szabálymotor konfigurálása – Azure Front Door'
description: Ez a cikk bemutatja, hogyan konfigurálhatja a szabálymotort a Azure Portal és a CLI-ban.
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
ms.openlocfilehash: e64f27480126fdf444f8dd72abc2d66c78b30407
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868806"
---
# <a name="tutorial-configure-your-rules-engine"></a>Oktatóanyag: A szabálymotor konfigurálása

Ez az oktatóanyag bemutatja, hogyan hozhat létre szabálymotor-konfigurációt és az első szabályt a Azure Portal és a CLI-ban. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - Konfigurálja a szabálymotort a portálon.
> - Szabálymotor konfigurálása az Azure CLI használatával

## <a name="prerequisites"></a>Előfeltételek

* Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először létre kell hoznia egy Front Doort. További információkért lásd a [Rövid útmutató: Front Door létrehozása](quickstart-create-front-door.md) szakaszt.

## <a name="configure-rules-engine-in-azure-portal"></a>Szabálymotor konfigurálása a Azure Portal
1. A Front Door-erőforráson belül válassza a **Beállítások, majd** a **Szabálymotor konfigurációja lehetőséget.** Kattintson **a Hozzáadás gombra,** adjon nevet a konfigurációnak, és kezdje el létrehozni az első szabálymotor-konfigurációt.

    ![Front Door beállítások menü](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

1. Kattintson **a Szabály hozzáadása elemre** az első szabály létrehozásához. Ezután a **Feltétel** hozzáadása vagy a **Művelet hozzáadása** gombra kattintva definiálhatja a szabályt.
    
    > [!NOTE]
    >- Ha törölni szeretne egy feltételt vagy műveletet a szabályból, használja az adott feltétel vagy művelet jobb oldalán található kuka lomtárát.
    > - Az összes bejövő forgalomra vonatkozó szabály létrehozásához ne adjon meg feltételeket.
    > - Ha leállítja a szabályok kiértékelét az első egyezés feltételének teljesülés után, jelölje be **a Fennmaradó szabály kiértékelének leállítása jelölőnégyzetet.** Ha ez be van jelölve, és egy adott szabály összes egyezésének feltételei teljesülnek, akkor a konfiguráció többi szabálya nem lesz végrehajtva.  

    ![Szabálymotor konfigurációja](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

1. Határozza meg a konfigurációban lévő szabályok prioritását a Felfelé, Lejjebb és Ugrás a felsőre gombokkal. A prioritás növekvő sorrendben van, ami azt jelenti, hogy az elsőként felsorolt szabály a legfontosabb szabály.

1. Miután létrehozott egy vagy több szabályt, nyomja le a **Mentés gombot.** Ez a művelet létrehozza a szabálymotor konfigurációját.

1. Miután létrehozott egy vagy több konfigurációt, társítsa a szabálymotor konfigurációját egy útválasztási szabályhoz. Bár egyetlen konfiguráció több útvonalszabályra is alkalmazható, egy útválasztási szabály csak egy szabálymotor-konfigurációt tartalmazhat. A társításhoz a tervező **útvonalszabályát Front Door meg.**  >   Válassza ki azt az útválasztási szabályt, amelybe a szabálymotor konfigurációját hozzá szeretné adni, majd az Útvonal **részletei** Szabálymotor konfigurációja alatt válassza ki a társítani kívánt  >  konfigurációt.

    ![Konfigurálás útválasztási szabályhoz](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Szabálymotor konfigurálása az Azure CLI-ban

1. Ha még nem, telepítse az [Azure CLI-t.](/cli/azure/install-azure-cli) Adja hozzá a "front-door" bővítményt:- az extension add --name front-door. Ezután jelentkezzen be, és váltson az az account set --subscription <name_or_Id>.

1. Első lépésként hozzon létre egy szabálymotort – ez a példa egy szabályt mutat be egy fejlécalapú művelet és egy egyeztetési feltétel mellett. 

    ```azurecli-interactive
    az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
    ```

1. Sorolja fel az összes szabályt. 

    ```azurecli-interactive
    az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
    ```

1. Adjon hozzá egy továbbítási útvonal felülbírálási műveletét. 

    ```azurecli-interactive
    az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
    ```

1. Listába sorolja egy szabály összes műveletét. 

    ```azurecli-interactive
    az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
    ```

1. Szabálymotor konfigurációjának csatolása útválasztási szabályhoz.  

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
    ```

1. Szabálymotor leválasztása. 

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
    ```

További információért az AFD szabálymotor parancsai teljes listáját itt [talál.](/cli/azure/network/front-door/rules-engine)   

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben konfigurálta és társította a szabálymotor konfigurációját az útválasztási szabályokhoz. Ha már nem szeretné, hogy a szabálymotor konfigurációja a Front Door, az alábbi lépések elvégzésével távolíthatja el a konfigurációt:

1. A szabálymotor neve melletti három pontra kattintva különválaszthatja az útválasztási szabályokat a szabálymotor konfigurációjában.

    :::image type="content" source="./media/front-door-rules-engine/front-door-rule-engine-routing-association.png" alt-text="Útválasztási szabályok társítása":::

1. Törölje az összes olyan útválasztási szabály jelölését, amelyekhez ez a szabálymotor-konfiguráció társítva van, és kattintson a Mentés gombra.

    :::image type="content" source="./media/front-door-rules-engine/front-door-routing-rule-association.png" alt-text="Útválasztási szabályok társítása":::

1. Most már törölheti a szabálymotor konfigurációját a Front Door.

    :::image type="content" source="./media/front-door-rules-engine/front-door-delete-rule-engine-configuration.png" alt-text="Szabálymotor konfigurációjának törlése":::

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Szabálymotor-konfiguráció létrehozása
* Társítsa a konfigurációt a Front Door útválasztási szabályokhoz.

A következő oktatóanyagból megtudhatja, hogyan adhat hozzá biztonsági fejléceket a Szabálymotorral.

> [!div class="nextstepaction"]
> [Biztonsági fejlécek szabálymotorral](front-door-security-headers.md)