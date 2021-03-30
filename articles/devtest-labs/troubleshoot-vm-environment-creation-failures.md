---
title: A virtuális gép és a környezet hibáinak elhárítása Azure DevTest Labs
description: Megtudhatja, hogyan lehet elhárítani a virtuális gépek (VM) és a környezet-létrehozási hibák elhárítását Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b7d3f3ad34d8a5bb48607816623c67121d21d78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476478"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>A virtuális gép (VM) és a környezet-létrehozási hibák elhárítása Azure DevTest Labs
A DevTest Labs figyelmeztetést ad, ha a gép neve érvénytelen, vagy ha a rendszer megsért egy tesztkörnyezet-házirendet. Időnként `X` a laborbeli virtuális gép vagy a környezeti állapot mellett vörös színnel jelenik meg, amely arról tájékoztatja, hogy valamilyen hiba történt.  Ez a cikk néhány trükköt tartalmaz, amelyek segítségével megtalálhatja a mögöttes problémát, és remélhetőleg a későbbiekben elkerülhető a probléma.

## <a name="portal-notifications"></a>Portál értesítései
Ha a Azure Portal használja, a megtekinteni kívánt első hely az **értesítések panel**.  A **harang ikonra** kattintva a fő parancssáv elérhető értesítések paneljén megtudhatja, hogy a tesztkörnyezet virtuális gépe vagy a környezet létrehozása sikeres volt-e.  Ha hiba történt, a létrehozási hibához kapcsolódó hibaüzenet jelenik meg. A részletek gyakran további információkat nyújtanak a probléma megoldásához. A következő példában a virtuális gép létrehozása nem sikerült, mert a magok kifogytak. A részletes üzenetből megtudhatja, hogyan javíthatja ki a problémát, és hogyan kérheti le az alapvető kvóta növelését.

![Értesítés Azure Portal](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>Virtuális gép sérült állapotban
Ha a virtuális gép állapota **sérültként** jelenik meg a laborban, előfordulhat, hogy az alapul szolgáló virtuális gépet törölték a **virtuális gép** lapról, amelyet a felhasználó a **Virtual Machines** lapon tud megnyitni (nem a DevTest Labs oldalról). Törölje a labort a DevTest Labs szolgáltatásban úgy, hogy törli a virtuális gépet a laborból. Ezután hozza létre újra a virtuális gépet a laborban. 

![A virtuális gép sérült állapotban van](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Tevékenységnaplók
Tekintse meg a tevékenységek naplóit, ha a virtuális gép vagy a környezet létrehozásának megkísérlése után egy hibát vizsgál meg. Ez a szakasz bemutatja, hogyan találhatja meg a virtuális gépek és a környezetek naplóit.

## <a name="activity-logs-for-virtual-machines"></a>A virtuális gépek tevékenység-naplófájljai

1. A tesztkörnyezet kezdőlapján válassza ki a virtuális gépet, amelyen el szeretné indítani a **virtuális gép** lapját.
2. A **virtuális gép** lap bal oldali menüjének **figyelés** területén válassza a **műveletnapló** lehetőséget a virtuális géphez társított összes napló megjelenítéséhez.
3. A műveletnapló elemei területen válassza ki a sikertelen műveletet. A sikertelen műveletet általában a rendszer meghívja `Write Virtualmachines` .
4. A jobb oldali ablaktáblán váltson a JSON lapra. A részleteket a napló JSON-nézetében tekintheti meg.

    ![A virtuális gép tevékenységi naplója](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Nézze át a JSON-naplót egészen addig, amíg meg nem találja a `statusMessage` tulajdonságot. Ez biztosítja a fő hibaüzenetet és további részletes információkat, ha vannak ilyenek. A következő JSON-példa a cikk korábbi részében megjelenő, a legfontosabb idézett hibára mutat.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Egy adott környezethez tartozó tevékenység naplója

Az alábbi lépéseket követve tekintheti meg a környezet létrehozásához szükséges tevékenységeket:

1. A tesztkörnyezet kezdőlapján válassza a **konfiguráció és házirendek** elemet a bal oldali menüben.
2. a **konfiguráció és házirendek** lapon válassza a menü **tevékenység naplók** elemét.
3. Keresse meg a hibát a naplóban, és válassza ki a műveletet.
4. A jobb oldali ablaktáblán váltson a JSON lapra, és keresse meg a **statusMessage**.

    ![Környezeti tevékenység naplója](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Resource Manager-sablonok telepítési naplói
Ha a környezet vagy a virtuális gép automatizáláson keresztül lett létrehozva, akkor van egy utolsó hely a hibaüzenetek kereséséhez. Ez a Azure Resource Manager sablon telepítési naplója. Ha egy tesztkörnyezet-erőforrás automatizáláson keresztül jön létre, gyakran egy Azure Resource Manager sablonon keresztül történik. [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)A DevTest Labs-erőforrásokat létrehozó példákat lásd: Azure Resource Manager sablonok.

A labor sablon telepítési naplófájljainak megtekintéséhez kövesse az alábbi lépéseket:

1. Indítsa el azon erőforráscsoport lapját, amelyben a labor létezik.
2. A **Beállítások** területen a bal oldali menüben válassza a **központi telepítések** lehetőséget.
3. Keresse meg a sikertelen állapotú központi telepítéseket, és jelölje ki.
4. A **telepítés** lapon válassza a **művelet részletei** hivatkozásra a sikertelen művelethez.
5. A **művelet részletei** ablakban megjelenő művelet részletei láthatók.

## <a name="next-steps"></a>Következő lépések
Lásd: az összetevők [hibáinak elhárítása](devtest-lab-troubleshoot-artifact-failure.md)
