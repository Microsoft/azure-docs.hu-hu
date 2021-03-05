---
title: Azure Service Bus – üzenetkezelési egységek automatikus frissítése
description: Ebből a cikkből megtudhatja, hogyan használhatja automatikusan a Service Bus-névtér üzenetkezelési egységeit.
ms.topic: how-to
ms.date: 03/03/2021
ms.openlocfilehash: 7fc3aca82b8f01d70dec4fc2dac7842895417ec9
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177955"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Azure Service Bus névtér üzenetkezelési egységeinek automatikus frissítése 
Az automatikus méretezés lehetővé teszi, hogy az alkalmazás terhelésének kezeléséhez megfelelő mennyiségű erőforrást futtasson. Lehetővé teszi az erőforrások hozzáadását a terhelés növekedésének kezeléséhez, és pénzt takarít meg a tétlenül ülő erőforrások eltávolításával. További információ a Azure Monitor autoskálázási funkciójának [áttekintéséről: Microsoft Azure](../azure-monitor/autoscale/autoscale-overview.md) . 

A Service Bus prémium szintű üzenetkezelés erőforrás-elkülönítést biztosít a CPU és a memória szintjén, így az ügyfél minden számítási feladata elkülönítve fut. Ennek az erőforrás-tárolónak a neve **üzenetkezelési egység**. További információ az üzenetkezelési egységekről: [Service Bus Premium Messaging](service-bus-premium-messaging.md). 

A prémium szintű névterek automatikus méretezés Service Bus funkciójának használatával megadhatja az [üzenetkezelési egységek](service-bus-premium-messaging.md) minimális és maximális számát, valamint a szabályok egy halmaza alapján automatikusan hozzáadhat vagy eltávolíthat üzenetküldési egységeket. 

Az automatikus skálázási funkcióval például a következő skálázási forgatókönyvek alkalmazhatók Service Bus névterekhez. 

- Ha a névtér CPU-használata 75% fölé esik, növelje a Service Bus-névtér üzenetkezelési egységeit. 
- Service Bus névtér üzenetkezelési egységeinek csökkentése, ha a névtér CPU-használata 25% alá csökken. 
- Több üzenetkezelési egység használata munkaidőn belül és kevesebb idő alatt. 

Ebből a cikkből megtudhatja, hogyan méretezheti automatikusan Service Bus névteret (az [üzenetkezelési egységek](service-bus-premium-messaging.md)frissítése) a Azure Portal. 

> [!IMPORTANT]
> Ez a cikk csak a Azure Service Bus **prémium** szintjére vonatkozik. 

## <a name="autoscale-setting-page"></a>Az autoskálázási beállítás lapja
Először az alábbi lépéseket követve navigáljon az Service Bus-névtérhez tartozó **autoskálázási beállítások** lapra.

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com). 
2. A keresősáv mezőbe írja be a **Service Bus** kifejezést, válassza a **Service Bus** elemet a legördülő listából, majd nyomja le az **ENTER** billentyűt. 
1. Válassza ki a **prémium névteret** a névterek listájából. 
1. Váltson a **skála** lapra. 

    :::image type="content" source="./media/automate-update-messaging-units/scale-page.png" alt-text="Service Bus névtér – oldal méretezése":::

## <a name="manual-scale"></a>Manuális méretezés 
Ezzel a beállítással rögzített számú üzenetküldési egység állítható be a névtérhez. 

1. Az automatikus **skálázási beállítás** lapon válassza a **manuális méretezés** lehetőséget, ha még nincs kiválasztva. 
1. Az **üzenetkezelési egységek** beállításnál válassza ki az üzenetkezelési egységek számát a legördülő listából.
1. A beállítás mentéséhez válassza az eszköztár **Mentés** elemét. 

    :::image type="content" source="./media/automate-update-messaging-units/manual-scale.png" alt-text="Üzenetkezelési egységek manuális méretezése":::       


## <a name="custom-autoscale---default-condition"></a>Egyéni autoskálázás – alapértelmezett feltétel
Az üzenetkezelési egységek automatikus skálázását feltételek használatával állíthatja be. Ez a skálázási feltétel akkor kerül végrehajtásra, ha a többi méretezési feltétel egyike sem egyezik. Az alapértelmezett feltételt az alábbi módszerek egyikével állíthatja be:

- Skálázás mérőszám alapján (például CPU vagy memóriahasználat)
- Méretezés adott számú üzenetkezelési egységre

Az alapértelmezett feltételhez nem állítható be az adott napokon vagy dátumtartományon lévő, az autoskálázásra vonatkozó ütemterv. Ezt a skálázási feltételt akkor hajtja végre a rendszer, ha a többi méretezési feltétel nem egyezik az ütemtervekkel. 

### <a name="scale-based-on-a-metric"></a>Skálázás mérőszám alapján
Az alábbi eljárás bemutatja, hogyan adhat hozzá olyan feltételt, amely automatikusan növeli az üzenetkezelési egységeket (vertikális felskálázás), ha a CPU-használat meghaladja a 75%-ot, és csökkenti az üzenetkezelési egységeket (skálázás), ha a CPU-használat kevesebb, mint 25%. A növekmények 1 – 2, 2 és 4 – 8, valamint 8 és 16 közötti értékre vannak elvégezve. Hasonlóképpen, a csökkentések 16 – 8, 8 – 4, 4 – 2, 2 és 1 között történik. 

1. Az automatikus **skálázási beállítás** lapon válassza az **Egyéni automatikus méretezés** lehetőséget az **erőforrás-méretezési lehetőség kiválasztásával** . 
1. Az oldal **alapértelmezett** szakaszában adja meg az alapértelmezett feltétel **nevét** . A szöveg szerkesztéséhez válassza a **ceruza** ikont. 
1. Skálázási **mód** **mérőszáma alapján** válassza a skála lehetőséget. 
1. Válassza **a + szabály hozzáadása** lehetőséget. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-add-rule-link.png" alt-text="Alapértelmezett – metrika alapján méretezhető":::    
1. A **skálázási szabály** lapon kövesse az alábbi lépéseket:
    1. Válasszon mérőszámot a **metrika neve** legördülő listából. Ebben a példában ez a **CPU**. 
    1. Válasszon ki egy operátort és egy küszöbértéket. Ebben a példában a metrikus küszöbértéknél **nagyobbak** és **75** a **skálázási művelet elindításához**. 
    1. Válasszon **műveletet** a **művelet** szakaszban. Ebben a példában a **növelés** értékre van állítva. 
    1. Ezután válassza a **Hozzáadás** lehetőséget.
    
        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-75.png" alt-text="Alapértelmezett – vertikális felskálázás, ha a CPU-használat meghaladja a 75%-ot":::       

        > [!NOTE]
        > Az autoscale funkció növeli a névtér üzenetkezelési egységeit, ha a teljes CPU-használat 75% fölé esik ebben a példában. A növekmények 1 – 2, 2 és 4 – 8, valamint 8 és 16 közötti értékre vannak elvégezve. 
1. Válassza a **+ szabály hozzáadása** lehetőséget, majd kövesse az alábbi lépéseket a **skálázási szabály** lapon:
    1. Válasszon mérőszámot a **metrika neve** legördülő listából. Ebben a példában ez a **CPU**. 
    1. Válasszon ki egy operátort és egy küszöbértéket. Ebben a példában a metrikus küszöbértéknél **kevesebb, mint** **25** a **skálázási művelet elindításához**. 
    1. Válasszon **műveletet** a **művelet** szakaszban. Ebben a példában a **csökkenés** értékre van állítva. 
    1. Ezután válassza a **Hozzáadás** lehetőséget. 

        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-25.png" alt-text="Alapértelmezett – méretezés abban az esetben, ha a CPU-használat kevesebb, mint 25%":::       

        > [!NOTE]
        > Az autoscale funkció csökkenti a névtér üzenetkezelési egységeit, ha a teljes CPU-használat a jelen példában 25% alá esik. A csökkentések 16 – 8, 8 – 4, 4 – 2 és 2 és 1 közötti értékre állnak. 
1. Az üzenetkezelési egységek **minimális** és **maximális** és **alapértelmezett** számának beállítása.

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-based.png" alt-text="Alapértelmezett szabály metrika alapján":::
1. Az autoskálázási beállítás mentéséhez válassza az eszköztár **Mentés** elemét. 
        
### <a name="scale-to-specific-number-of-messaging-units"></a>Méretezés adott számú üzenetkezelési egységre
A következő lépésekkel konfigurálhatja a szabályt úgy, hogy az adott számú üzenetkezelési egység használatára méretezi a névteret. Az alapértelmezett feltétel akkor is érvényes, ha a többi méretezési feltétel egyike sem egyezik. 

1. Az automatikus **skálázási beállítás** lapon válassza az **Egyéni automatikus méretezés** lehetőséget az **erőforrás-méretezési lehetőség kiválasztásával** . 
1. Az oldal **alapértelmezett** szakaszában adja meg az alapértelmezett feltétel **nevét** . 
1. Skálázási **mód** esetén válassza a **Méretezés adott üzenetkezelési egységhez** lehetőséget. 
1. Az **üzenetkezelési egységek** esetében válassza ki az alapértelmezett üzenetküldési egységek számát. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-messaging-units.png" alt-text="Alapértelmezett – adott üzenetkezelési egységek méretezése":::       

## <a name="custom-autoscale---additional-conditions"></a>Egyéni autoskálázás – további feltételek
Az előző szakaszban megtudhatja, hogyan adhat hozzá alapértelmezett feltételt az autoskálázási beállításhoz. Ebből a szakaszból megtudhatja, hogyan adhat hozzá több feltételt az autoskálázási beállításhoz. Ezeknél a további nem alapértelmezett feltételeknél a hét adott napjain vagy dátumtartományon alapuló ütemtervet is beállíthat. 

### <a name="scale-based-on-a-metric"></a>Skálázás mérőszám alapján
1. Az automatikus **skálázási beállítás** lapon válassza az **Egyéni automatikus méretezés** lehetőséget az **erőforrás-méretezési lehetőség kiválasztásával** . 
1. Válassza az **alapértelmezett** blokkban a **méretezési feltétel hozzáadása** lehetőséget. 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Egyéni – méretezési feltétel hivatkozásának hozzáadása":::    
1. Adja meg a feltétel **nevét** . 
1. Ellenőrizze, hogy ki van-e jelölve a **skála mérőszámon alapuló** beállítás. 
1. Válassza a **+ szabály hozzáadása** lehetőséget egy szabály hozzáadásához az üzenetkezelési egységek növeléséhez, ha a teljes CPU-használat 75% fölé esik. Kövesse az [alapértelmezett feltétel](#custom-autoscale---default-condition) szakasz lépéseit. 
5. Az üzenetkezelési egységek **minimális** és **maximális** és **alapértelmezett** számának beállítása.
6. Egyéni feltételhez is beállíthat **ütemtervet** (az alapértelmezett feltételnél nem). Megadhatja a feltétel kezdési és befejezési dátumát (vagy) egy hét bizonyos napjai (hétfő, kedd stb.) közül. 
    1. Ha a **kezdő/befejező dátumok megadása** lehetőséget választja, válassza ki az **időzónát**, a **kezdés dátumát és idejét** , valamint a **befejezési dátumot és időt** (ahogy az az alábbi képen is látható) a feltétel érvénybe léptetéséhez. 

       :::image type="content" source="./media/automate-update-messaging-units/custom-min-max-default.png" alt-text="Az üzenetkezelési egységek számának minimális, maximális és alapértelmezett értékei":::
    1. Ha a **megadott napok ismétlése** lehetőséget választja, válassza ki a hét napjait, az időzónát, a kezdési időt és a befejezési időpontot, amikor a feltételt alkalmazni szeretné. 

        :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days.png" alt-text="Adott napok ismétlése":::
  
### <a name="scale-to-specific-number-of-messaging-units"></a>Méretezés adott számú üzenetkezelési egységre
1. Az automatikus **skálázási beállítás** lapon válassza az **Egyéni automatikus méretezés** lehetőséget az **erőforrás-méretezési lehetőség kiválasztásával** . 
1. Válassza az **alapértelmezett** blokkban a **méretezési feltétel hozzáadása** lehetőséget. 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Egyéni – méretezési feltétel hivatkozásának hozzáadása":::    
1. Adja meg a feltétel **nevét** . 
2. **Skálázási mód** esetén válassza a **Méretezés adott üzenetkezelési egységhez** lehetőséget. 
1. Válassza ki az **üzenetkezelési egységek** számát a legördülő listából. 
6. Az **ütemtervhez** adja meg a feltétel kezdési és befejezési dátumát (vagy) a hét és az idő megadott napjai (hétfő, kedd stb.) közül. 
    1. Ha a **kezdő/befejező dátumok megadása** lehetőséget választja, válassza ki az **időzónát**, a **kezdő dátum és idő** , valamint a **befejezési dátum és idő** beállítást a feltétel érvénybe léptetéséhez. 
    
    :::image type="content" source="./media/automate-update-messaging-units/scale-specific-messaging-units-start-end-dates.png" alt-text="méretezés adott üzenetkezelési egységekre – kezdési és befejezési dátumok":::        
    1. Ha a **megadott napok ismétlése** lehetőséget választja, válassza ki a hét napjait, az időzónát, a kezdési időt és a befejezési időpontot, amikor a feltételt alkalmazni szeretné.
    
    :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days-2.png" alt-text="méretezés adott üzenetkezelési egységekre – adott napok ismétlése":::

    
    Ha többet szeretne megtudni az autoskálázási beállítások működéséről, különösen arról, hogy miként lehet profilt vagy feltételt kiértékelni, és több szabályt is kiértékel, tekintse meg az az [autoskálázási beállítások ismertetése](../azure-monitor/autoscale/autoscale-understanding-settings.md)          

    > [!NOTE]
    > - Az automatikus skálázással kapcsolatos döntések elvégzéséhez szükséges mérőszámok 5-10 percen belül lehetnek. A tüskés munkaterhelések kezelésekor azt javasoljuk, hogy rövidebb időtartamokkal rendelkezzen a méretezéshez (> 10 perc) való méretezéshez és a hosszabb időtartamokhoz, hogy elegendő üzenetküldési egység legyen a tüskés munkaterhelések feldolgozásához. 
    > 
    > - Ha nem rendelkezik a kapacitás hiánya miatti hibákkal (nem érhető el üzenetküldési egység), egy támogatási jegyet is felvehet velünk.  


## <a name="next-steps"></a>Következő lépések
Az üzenetkezelési egységekről a prémium szintű [üzenetkezelés](service-bus-premium-messaging.md) című témakörben olvashat bővebben.

