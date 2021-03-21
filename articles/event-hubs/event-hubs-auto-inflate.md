---
title: Átviteli egységek automatikus méretezése – Azure Event Hubs | Microsoft Docs
description: Engedélyezze az automatikus kiosztást a névtéren az átviteli egységek automatikus skálázásához.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1be564472011622b71b3066495748dfdbe6cc791
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020806"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Az Azure Event Hubs átviteli egységek automatikus méretezése
Az Azure Event Hubs egy rugalmasan méretezhető adatfolyam-platform. Ennek megfelelően a Event Hubs használat a szolgáltatás használatának megkezdése után gyakran növekszik. Az ilyen használathoz az előre meghatározott [átviteli egységek](event-hubs-scalability.md#throughput-units) növelésére van szükség a Event Hubs méretezéséhez és a nagyobb átviteli sebességek kezeléséhez. A Event Hubs **automatikus** feltöltési funkciója automatikusan méretezi az átviteli egységek számának növelésével, hogy megfeleljen a használati igényeknek. Az átviteli egységek növelése megakadályozza a szabályozást, amelyben:

* A bejövő adatforgalom aránya meghaladja a beállított átviteli egységeket.
* A kimenő adatforgalomra vonatkozó kérelmek aránya meghaladja a set átviteli egységeket.

A Event Hubs szolgáltatás növeli az átviteli sebességet, ha a terhelés a minimális küszöbértéknél nagyobb mértékben növekszik, anélkül, hogy a ServerBusy hibákkal kapcsolatos kérések sikertelenek lesznek.

## <a name="how-auto-inflate-works"></a>Az automatikus kiépítési funkció

Event Hubs forgalmat az [átviteli egységek](event-hubs-scalability.md#throughput-units)vezérlik. Egy átviteli egység a bejövő forgalom 1 MB/másodpercét, a kimenő forgalom mennyiségét pedig kétszer is lehetővé teszi. A standard szintű Event hubok 1-20-es átviteli egységekkel konfigurálhatók. Az automatikus kitöltés lehetővé teszi, hogy a minimálisan szükséges átviteli egységeket kis méretűre kezdje. A szolgáltatás ezután automatikusan átméretezi a szükséges átviteli egységek maximális korlátját, a forgalom növekedésének függvényében. Az automatikus kiemelés a következő előnyöket biztosítja:

- Hatékony méretezési mechanizmus a kisebb és vertikális felskálázáshoz a növekedés során.
- Automatikus méretezés a megadott felső korlátra a hibák szabályozása nélkül.
- Nagyobb mértékben szabályozhatja a skálázást, mivel a méretezést is lehetővé teszi.

## <a name="enable-auto-inflate-on-a-namespace"></a>Automatikus kiemelés engedélyezése a névtéren

A következő módszerek egyikével engedélyezheti vagy letilthatja az automatikus kikapcsolást a standard szintű Event Hubs névterekben:

- A [Azure Portal](https://portal.azure.com).
- Egy [Azure Resource Manager sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

> [!NOTE]
> Az alapszintű Event Hubs névterek nem támogatják az automatikus kivonást.

### <a name="enable-auto-inflate-through-the-portal"></a>Automatikus kiengedés engedélyezése a portálon


#### <a name="enable-at-the-time-of-creation"></a>Engedélyezés a létrehozás időpontjában 
**Event Hubs névtér létrehozásakor** engedélyezheti az automatikus kiemelés funkciót:
 
![Automatikus kiemelés engedélyezése az Event hub létrehozásakor](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Ha ez a beállítás engedélyezve van, az átviteli egységeknél kisebbre is kiindulhat, és felskálázást használhat a használati igények növekedésével. Az infláció felső határa nem befolyásolja azonnal a díjszabást, ami az óránként felhasznált átviteli egységek számától függ.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Automatikus kiemelés engedélyezése meglévő Event hub-hoz
Engedélyezheti az automatikus feltöltés funkciót is, és módosíthatja a beállításait a következő utasítások használatával: 
 
1. A **Event Hubs névtér** lapon válassza a Letiltva lehetőséget az **átviteli egységek automatikus** **kikapcsolása** lehetőségnél.  

    ![Válassza ki az átviteli egységek elemet a Event Hubs névtér oldalon](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. A **méretezési beállítások** lapon jelölje be az **Engedélyezés** jelölőnégyzetet (ha nem engedélyezte az autoskálázási funkciót).

    ![Engedélyezés kiválasztása](./media/event-hubs-auto-inflate/scale-settings.png)
3. Adja meg az átviteli egységek **maximális** számát, vagy használja a görgetősávot az érték megadásához. 
4. választható A lap tetején lévő átviteli egységek **minimális** számának frissítése. 


> [!NOTE]
> Ha az automatikus feltöltési konfigurációt alkalmazza az átviteli egységek növelésére, a Event Hubs szolgáltatás diagnosztikai naplókat bocsát ki, amelyekkel az átviteli sebesség megnövekedésének okát és időpontját is megadhatja. Az Event hub diagnosztikai naplózásának engedélyezéséhez kattintson a **diagnosztikai beállítások** elemre a Azure Portal Event hub lapjának bal oldali menüjében. További információ: [diagnosztikai naplók beállítása Azure Event hub-hoz](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Automatikus kiemelés engedélyezése Azure Resource Manager sablon használatával

Engedélyezheti az automatikus kiépítést Azure Resource Manager sablon központi telepítése során. Állítsa be például a `isAutoInflateEnabled` tulajdonságot **igaz** értékre, és állítsa 10 értékre `maximumThroughputUnits` . Például:

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

A teljes sablonhoz lásd: [Event Hubs névtér létrehozása és a kikapcsolt sablon engedélyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) a githubon.


## <a name="next-steps"></a>Következő lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs áttekintése](./event-hubs-about.md)
