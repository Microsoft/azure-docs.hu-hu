---
title: Virtuális gépek indítása/leállítása v2 (előzetes verzió)
description: Ebből a cikkből megtudhatja, hogyan helyezheti üzembe az Azure-előfizetésében az Azure-beli virtuális gépekhez készült Start/Stop VM v2 (előzetes verzió) szolgáltatást.
services: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 9ca808fffbd26c8837ad9a43447f60e99f89d922
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111980"
---
# <a name="deploy-startstop-vms-v2-preview"></a>Virtuális gépek indítása/leállítása v2 (előzetes verzió)

A jelen témakörben ismertetett lépések végrehajtásával telepítse a virtuális gépek v2 (előzetes verzió) szolgáltatásának indítása/leállítása funkciót. A telepítési folyamat befejezése után konfigurálja az ütemterveket a követelményekhez való testreszabáshoz.

## <a name="deploy-feature"></a>Szolgáltatás üzembe helyezése

A központi telepítés a virtuális gépek v2 GitHub [-szervezetének](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)indítása/leállítása. Habár a szolgáltatás célja, hogy az előfizetésben lévő összes virtuális gépet az adott előfizetésen belüli egyetlen központi telepítésből származó összes erőforráscsoporthoz kezelhesse, a szervezete működési modelljén vagy követelményein alapuló másik példányt is telepíthet. Azt is beállíthatja, hogy a virtuális gépeket központilag kezelhesse több előfizetésen keresztül.

A felügyelet és az Eltávolítás egyszerűsítése érdekében javasoljuk, hogy a virtuális gépek v2 (előzetes verzió) indítási/leállítási szolgáltatását dedikált erőforráscsoporthoz telepítse.

> [!NOTE]
> Ez az előzetes verzió jelenleg nem támogatja meglévő Storage-fiók vagy Application Insights erőforrás megadását.

1. Nyissa meg a böngészőt, és navigáljon a VM v2 [GitHub-szervezet](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)indításához/leállításához.
1. Válassza ki a központi telepítési lehetőséget az Azure-beli virtuális gépek létrehozásához használt Azure-beli felhőalapú környezet alapján. Ekkor megnyílik a Azure Portal egyéni Azure Resource Manager üzembe helyezés lapja.
1. Ha a rendszer kéri, jelentkezzen be a [Azure Portalba](https://portal.azure.com).
1. Írja be a következő értékeket:

    |Name |Érték |
    |-----|------|
    |Region |Válassza ki az új erőforrások közelében lévő régiót.|
    |Erőforráscsoport neve |Adja meg az erőforráscsoport nevét, amely a virtuális gépek indítására és leállítására szolgáló egyedi erőforrásokat fogja tartalmazni. |
    |Erőforráscsoport-régió |Határozza meg az erőforráscsoport régióját. Például: **USA középső régiója**.|
    |Azure függvényalkalmazás neve |Írjon be egy URL-útvonalon érvényes nevet. A rendszer érvényesíti a beírt nevet, hogy a Azure Functions egyedi legyen. |
    |Application Insights neve |Adja meg annak a Application Insights-példánynak a nevét, amely a virtuális gépek indításának és leállításának elemzését fogja tárolni. |
    |Application Insights régió |A Application Insights-példány régiójának megadása.|
    |Storage-fiók neve |Adja meg annak az Azure Storage-fióknak a nevét, amely a virtuális gépek futtatásának indítási/leállítási telemetria tárolja. |
    |E-mail-cím |Egy vagy több e-mail-címet kell megadnia az állapotüzenetek fogadásához, vesszővel (,) elválasztva.|

    :::image type="content" source="media/deploy/deployment-template-details.png" alt-text="Virtuális gépek sablonjának üzembe helyezési konfigurációjának indítása/leállítása":::

1. Kattintson a lap alján található **felülvizsgálat + létrehozás** elemre.
1. A telepítés elindításához válassza a **Létrehozás** lehetőséget.
1. Az üzembe helyezés állapotát úgy nézheti meg, ha a képernyő felső részén kiválasztja a harang (értesítések) ikont. Ekkor **a telepítés folyamatban** van. Várjon, amíg az üzembe helyezés befejeződik.
1. Válassza az **Ugrás az erőforráscsoportra** lehetőséget az értesítési panelen. A következőhöz hasonló képernyő jelenik meg:

    :::image type="content" source="media/deploy/deployment-results-resource-list.png" alt-text="Virtuális gépek sablon üzembe helyezési erőforrásainak indítása/leállítása":::

## <a name="enable-multiple-subscriptions"></a>Több előfizetés engedélyezése

Az üzembe helyezés indítása/leállítása után a következő lépésekkel engedélyezheti a virtuális gépek v2 (előzetes verzió) indítási és leállítási műveleteit a több előfizetésre vonatkozó műveletek végrehajtásához.

1. Másolja a telepítés során megadott Azure függvényalkalmazás név értékét.

1. A portálon navigáljon a másodlagos előfizetéséhez. Válassza ki az előfizetést, majd válassza a **Access Control (iam)** lehetőséget

1. Válassza a **Hozzáadás** , majd a **szerepkör-hozzárendelés hozzáadása** elemet.

1. Válassza ki a **közreműködői** szerepkört a **szerepkör** legördülő listából.

1. A **Select (kiválasztás** ) mezőben adja meg az Azure Function alkalmazás nevét. Válassza ki a függvény nevét az eredmények között.

1. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

## <a name="configure-schedules-overview"></a>Ütemtervek konfigurálása – áttekintés

A virtuális gépek indításának és leállításának szabályozására szolgáló automatizálási módszer kezeléséhez a követelmények alapján egy vagy több befoglalt logikai alkalmazást is be kell állítania.

- Az ütemezett indítási és leállítási műveletek a Azure Resource Manager és a klasszikus virtuális gépeken megadott ütemezés alapján vannak meghatározva.**ststv2_vms_Scheduled_start** és **ststv2_vms_Scheduled_stop** konfigurálja az ütemezett kezdést és leállítást.

- A sorrendbe állított – indítási és leállítási műveletek az előre definiált szekvencia-címkékkel rendelkező virtuális gépekre irányuló ütemezésen alapulnak. Csak két megnevezett címke támogatott – **sequencestart** és **sequencestop**. **ststv2_vms_Sequenced_start** és **ststv2_vms_Sequenced_stop** konfigurálja az előkészített indítást és a leállítást.

    > [!NOTE]
    > Ez a forgatókönyv csak Azure Resource Manager virtuális gépeket támogatja.

- Autostop – ez a funkció csak a CPU-kihasználtsága alapján a Azure Resource Manager és a klasszikus virtuális gépekre vonatkozó leállítási műveletek végrehajtásához használatos. Ez egy ütemezett *művelet* is lehet, amely riasztásokat hoz létre a virtuális gépeken, és a feltétel alapján a riasztás a Leállítás művelet végrehajtásához aktiválódik.**ststv2_vms_AutoStop** konfigurálja az automatikus leállítási funkciót.

Ha további ütemtervekre van szüksége, duplikálhatja a Azure Portal **klónozási** lehetőségével megadott Logic apps egyikét.

:::image type="content" source="media/deploy/logic-apps-clone-option.png" alt-text="Válassza a klónozás lehetőséget egy logikai alkalmazás duplikálása érdekében":::

## <a name="scheduled-start-and-stop-scenario"></a>Ütemezett indítási és leállítási forgatókönyv

A következő lépések végrehajtásával konfigurálhatja a Azure Resource Manager és a klasszikus virtuális gépek ütemezett indítási és leállítási műveleteit. Beállíthatja például, hogy a **ststv2_vms_Scheduled_start** az ütemtervet reggel, amikor az irodában van, és az összes előfizetésben le tudja állítani az összes virtuális gépet, amikor az **ststv2_vms_Scheduled_stop** menetrend alapján elhagyja a munkát az esti munkában.

A logikai alkalmazás csak a virtuális gépek indítására való konfigurálását támogatja.

Az egyes forgatókönyvek esetében a műveletet egy vagy több előfizetésre, egy vagy több erőforráscsoporthoz is megcélozhatja, és egy vagy több virtuális gépet is megadhat egy befoglalási vagy kizárási listán. Nem adhatók meg együtt ugyanabban a logikai alkalmazásban.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , majd navigáljon a **Logic apps** szolgáltatáshoz.

1. A logikai alkalmazások listájáról az ütemezett indítás konfigurálásához válassza a **ststv2_vms_Scheduled_start** lehetőséget. Az ütemezett leállítás konfigurálásához válassza a **ststv2_vms_Scheduled_stop** lehetőséget.

1. Válassza a **Logic app Designer** elemet a bal oldali ablaktáblán.

1. A Logic app Designer megjelenése után a tervező ablaktáblán válassza az **Ismétlődés** lehetőséget a logikai alkalmazás ütemezése konfigurálásához. Az ismétlődési beállításokkal kapcsolatos további információkért lásd: [ismétlődő feladat ütemezése](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="A logikai alkalmazás ismétlődési gyakoriságának konfigurálása":::

1. A tervező ablaktáblán válassza a **függvény – próbálja meg** konfigurálni a célhely beállításait. Ha a kérelem törzsében szeretné kezelni a virtuális gépeket az előfizetés összes erőforráscsoport területén, módosítsa a kérelem törzsét az alábbi példában látható módon.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     }
    }
    ```

    Több előfizetést is megadhat a `subscriptions` tömbben minden egyes értékkel, vesszővel elválasztva, az alábbi példában látható módon.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    Ha meghatározott erőforráscsoportok esetében szeretné kezelni a virtuális gépeket, a kérelem törzsében módosítsa a kérelem törzsét az alábbi példában látható módon. Minden megadott erőforrás-elérési utat vesszővel kell elválasztani. Ha szükséges, megadhat egy erőforráscsoportot.

    Ez a példa egy virtuális gép kizárását is mutatja. A virtuális gépet kizárhatja a virtuális gépek erőforrás-elérési útjának vagy helyettesítő karakterének megadásával.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

    Itt a művelet az összes virtuális gépen elvégezhető, kivéve a virtuális gép nevét az az és a BZ együttesével mindkét előfizetésben.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [“Az*”,“Bz*”],
       "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
    
        ]
      }
    }
    ```

    Ha a kérelem törzsében egy adott virtuális gépet szeretne felügyelni az előfizetésen belül, módosítsa a kérelem törzsét az alábbi példában látható módon. Minden megadott erőforrás-elérési utat vesszővel kell elválasztani. Szükség esetén egy virtuális gépet is megadhat.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg3/providers/Microsoft.Compute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
          
        ]
    }
    ```

## <a name="sequenced-start-and-stop-scenario"></a>Szekvenciális indítási és leállítási forgatókönyv

Egy olyan környezetben, amely két vagy több összetevőt tartalmaz több Azure Resource Manager virtuális gépen egy elosztott alkalmazás-architektúrában, amely támogatja az összetevők indításának és leállításának sorrendjét.

1. A logikai alkalmazások listájáról az előkészített indítás beállításnál válassza a **ststv2_vms_Sequenced_start** lehetőséget. A szekvenciális leállítás konfigurálásához válassza a **ststv2_vms_Sequenced_stop** lehetőséget.

1. Válassza a **Logic app Designer** elemet a bal oldali ablaktáblán.

1. A Logic app Designer megjelenése után a tervező ablaktáblán válassza az **Ismétlődés** lehetőséget a logikai alkalmazás ütemezése konfigurálásához. Az ismétlődési beállításokkal kapcsolatos további információkért lásd: [ismétlődő feladat ütemezése](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="A logikai alkalmazás ismétlődési gyakoriságának konfigurálása":::

1. A tervező ablaktáblán válassza a **függvény – próbálja meg** konfigurálni a célhely beállításait. Ha a kérelem törzsében szeretné kezelni a virtuális gépeket az előfizetés összes erőforráscsoport területén, módosítsa a kérelem törzsét az alábbi példában látható módon.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     },
       "Sequenced": true
    }
    ```

    Több előfizetést is megadhat a `subscriptions` tömbben minden egyes értékkel, vesszővel elválasztva, az alábbi példában látható módon.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    Ha meghatározott erőforráscsoportok esetében szeretné kezelni a virtuális gépeket, a kérelem törzsében módosítsa a kérelem törzsét az alábbi példában látható módon. Minden megadott erőforrás-elérési utat vesszővel kell elválasztani. Szükség esetén egy erőforráscsoportot is megadhat.

    Ez a példa azt is bemutatja, hogy a virtuális gépeket az erőforrás elérési útja alapján kizárják, mint az ütemezett indítási/leállítási és a helyettesítő karaktereket.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      },
       "Sequenced": true
    }
    ```

    Ha egy előfizetésen belül szeretné kezelni a virtuális gépek egy adott készletét, a kérelem törzsében módosítsa a kérelem törzsét az alábbi példában látható módon. Minden megadott erőforrás-elérési utat vesszővel kell elválasztani. Szükség esetén egy virtuális gépet is megadhat.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
        ]
      },
       "Sequenced": true
    }
    ```

## <a name="auto-stop-scenario"></a>Automatikus leállítás forgatókönyve

A VM v2 indítása/leállítása (előzetes verzió) segítségével kezelheti az előfizetésében a Azure Resource Manager és a klasszikus virtuális gépek futtatásának költségeit a nem csúcsos időszakokban nem használt gépek kiértékelésével, például órák után, és automatikusan leáll, ha a processzor kihasználtsága kisebb, mint a megadott százalék.

A következő metrikai riasztás tulajdonságai a kérelem törzsében a testreszabást támogatják:

- AutoStop_MetricName
- AutoStop_Condition
- AutoStop_Threshold
- AutoStop_Description
- AutoStop_Frequency
- AutoStop_Severity
- AutoStop_Threshold
- AutoStop_TimeAggregationOperator
- AutoStop_TimeWindow

Ha többet szeretne megtudni a Azure Monitor metrikai riasztások működéséről, valamint arról, hogyan konfigurálhatja őket, tekintse meg [a Azure monitor metrikai riasztásokat](../../azure-monitor/alerts/alerts-metric-overview.md).

1. A logikai alkalmazások listájából válassza az automatikus leállítás beállítást **ststv2_vms_AutoStop**.

1. Válassza a **Logic app Designer** elemet a bal oldali ablaktáblán.

1. A Logic app Designer megjelenése után a tervező ablaktáblán válassza az **Ismétlődés** lehetőséget a logikai alkalmazás ütemezése konfigurálásához. Az ismétlődési beállításokkal kapcsolatos további információkért lásd: [ismétlődő feladat ütemezése](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="A logikai alkalmazás ismétlődési gyakoriságának konfigurálása":::

1. A tervező ablaktáblán válassza a **függvény – próbálja meg** konfigurálni a célhely beállításait. Ha a kérelem törzsében szeretné kezelni a virtuális gépeket az előfizetés összes erőforráscsoport területén, módosítsa a kérelem törzsét az alábbi példában látható módon.

    ```json
    {
      "Action": "stop",
      "EnableClassic": false,    
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "RequestScopes":{        
        "Subscriptions":[
            "/subscriptions/12345678-1111-2222-3333-1234567891234/",
            "/subscriptions/12345678-2222-4444-5555-1234567891234/"
        ],
        "ExcludedVMLists":[]
      }        
    }
    ```

    Ha meghatározott erőforráscsoportok esetében szeretné kezelni a virtuális gépeket, a kérelem törzsében módosítsa a kérelem törzsét az alábbi példában látható módon. Minden megadott erőforrás-elérési utat vesszővel kell elválasztani. Szükség esetén egy erőforráscsoportot is megadhat.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "ResourceGroups": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroupsvmrg2/",
          "/subscriptions/12345678-2222-4444-5555-1234567891234/resourceGroups/VMHostingRG/"
          ]
      }
    }
    ```

    Ha a kérelem törzsében egy adott virtuális gépet szeretne felügyelni az előfizetésen belül, módosítsa a kérelem törzsét az alábbi példában látható módon. Minden megadott erőforrás-elérési utat vesszővel kell elválasztani. Szükség esetén egy virtuális gépet is megadhat.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/rg3/providers/Microsoft.ClassicCompute/virtualMachines/Clasyvm11",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

## <a name="next-steps"></a>Következő lépések

Ha meg szeretné tudni, hogyan figyelheti az Azure-beli virtuális gépek a Start/Stop VM v2 (előzetes verzió) funkció által kezelt állapotát, és egyéb felügyeleti feladatokat hajthat végre, tekintse meg a [virtuális gépek indítása/leállítása](manage.md) című cikket.