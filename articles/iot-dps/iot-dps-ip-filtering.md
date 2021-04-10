---
title: Azure IoT DPS IP-kapcsolati szűrők | Microsoft Docs
description: Adott IP-címekről az Azure IoT DPS-példányra irányuló kapcsolatok blokkolása IP-szűrés használatával Blokkolhatja az egyéni IP-címekről vagy IP-címtartományokból érkező kapcsolatokat.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: e1b175a176255da465433b2db45cb3cb67d360d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98934497"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Azure IoT DPS IP-kapcsolati szűrők használata

A biztonság minden IoT-megoldás esetében fontos szempont. A biztonsági konfiguráció részeként olykor explicit módon meg kell adni, hogy mely IP-címekről kapcsolódhatnak az eszközök. Az Azure IoT Hub Device Provisioning Service (DPS) *IP-szűrő* funkciója lehetővé teszi olyan szabályok konfigurálását, amelyek az adott IPv4-címekről érkező forgalom elutasításáról vagy fogadásáról rendelkeznek.

## <a name="when-to-use"></a>A következő esetekben használja

Két konkrét használati eset van, ahol hasznos lehet blokkolni egy DPS-végpont felé irányuló, adott IP-címekről érkező kapcsolatokat:

* A DPS-ének csak az IP-címek egy adott tartományából érkező forgalmat szabad fogadnia, minden mást pedig el kell utasítania. Tegyük fel, hogy a DPS-t az [Azure Express Route-tal](../expressroute/expressroute-faqs.md#supported-services) használja, hogy privát kapcsolatokat létesíthessen a DPS és az eszközei között.

* El kell utasítania a forgalmat az olyan IP-címekről, amelyeket a DPS rendszergazdája gyanúsnak talált.

## <a name="how-filter-rules-are-applied"></a>A szűrési szabályok alkalmazásának módja

Az IP-szűrési szabályokat a rendszer a DPS-példány szintjén alkalmazza. Ezért az IP-szűrési szabályok minden, támogatott protokollt használó eszköz és háttéralkalmazás felől érkező kapcsolatra érvényesek.

A DPS-példány elutasító IP-szabályának megfelelő IP-címekről érkező kapcsolódási kísérletekre a rendszer a 401-es (nem engedélyezett) állapotkódot és egy leírást ad válaszul. A válaszüzenet nem említi az IP-szabályt.

## <a name="default-setting"></a>Alapértelmezett beállítás

Az **IP-szűrő** DPS-re vonatkozó táblázata alapértelmezés szerint üres a portálon. Ez az alapértelmezett beállítás azt jelenti, hogy a DPS bármilyen IP-címről fogad kapcsolatokat. Ez az alapértelmezett beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

![Az IoT DPS alapértelmezett IP-szűrési beállításai](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>IP-szűrési szabály hozzáadása vagy szerkesztése

IP-szűrési szabály hozzáadásához válassza a **+ IP-szűrési szabály hozzáadása** lehetőséget.

![IP-szűrési szabály hozzáadása IoT DPS-hez](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Az **IP-szűrési szabály hozzáadása** lehetőség kiválasztása után töltse ki a mezőket.

![Az IP-szűrési szabály hozzáadása lehetőség kiválasztása után](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Adjon **nevet** az IP-szűrési szabálynak. A névnek egyedi alfanumerikus sztringnek kell lennie, amely nem különbözteti meg a kis- és nagybetűket, és legfeljebb 128 karakterből áll. Csak az ASCII 7 bites alfanumerikus karakterei és a `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` elfogadottak.

* Adjon meg egy egyedi IPv4-címet vagy egy IP-címblokkot CIDR-jelölésrendszer használatával. A CIDR-jelölés szerint például a 192.168.100.0/22 a 192.168.100.0 és a 192.168.103.255 közötti 1024 IPv4-címet jelöli.

* Állítsa be az **Engedélyezés** vagy a **Letiltás** lehetőséget a szűrési szabály **művelet** értékeként.

A mezők kitöltése után kattintson a **Mentés** gombra a szabály mentéséhez. Riasztás fog megjelenni, amely tájékoztatja, hogy a frissítés folyamatban van.

![IP-szűrési szabály mentéséről szóló értesítés](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

A **Hozzáadás** lehetőség le van tiltva, amikor eléri a maximális 100 IP-szűrési szabályt.

Meglévő szabály szerkesztéséhez jelölje ki a módosítani kívánt adatokat, végezze el a módosítást, majd válassza a **Mentés** lehetőséget a módosítás mentéséhez.

> [!NOTE]
> Az IP-címek elutasítása megakadályozhatja, hogy más Azure-szolgáltatások kommunikálni tudjanak a DPS-példánnyal.

## <a name="delete-an-ip-filter-rule"></a>IP-szűrési szabály törlése

IP-szűrési szabály törléséhez válassza az adott sorban található kuka ikont, majd a **Mentés** lehetőséget. Ezzel törli a szabályt, és menti a módosítást.

![IoT DPS IP-szűrési szabály törlése](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>IP-szűrési szabályok frissítése a kódban

A DPS IP-szűrőjét az Azure-beli erőforrás-szolgáltató REST-végpontjának használatával kérheti le és módosíthatja. Lásd: `properties.ipFilterRules` a [createorupdate metódusban](/rest/api/iot-dps/iotdpsresource/createorupdate).

A DPS IP-szűrési szabályok frissítése az Azure CLI-ben és az Azure PowerShellben jelenleg nem támogatott, de Azure Resource Manager-sablonok segítségével elvégezhető. Resource Manager-sablonok használatával kapcsolatos útmutatás: [Azure Resource Manager-sablonok](../azure-resource-manager/templates/overview.md). A következő példasablonok megmutatják, hogyan hozhat létre, szerkeszthet és törölhet DPS IP-szűrési szabályokat.

### <a name="add-an-ip-filter-rule"></a>IP-szűrési szabály hozzáadása

A következő példasablon egy „AllowAll” nevű új IP-szűrési szabályt hoz létre, amely minden forgalmat átenged.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Frissítse a sablon IP-szűrési szabályának attribútumait az igényeinek megfelelően.

| Attribútum                | Leírás |
| ------------------------ | ----------- |
| **FilterName**           | Adjon nevet az IP-szűrési szabálynak. A névnek egyedi alfanumerikus sztringnek kell lennie, amely nem különbözteti meg a kis- és nagybetűket, és legfeljebb 128 karakterből áll. Csak az ASCII 7 bites alfanumerikus karaktereket és a  `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`   rendszer fogadja el. |
| **Művelet**               | Az IP-szűrési szabály elfogadott műveleteinek értékei: **Elfogadás**  vagy  **Elutasítás** . |
| **ipMask**               | Adjon meg egy egyedi IPv4-címet vagy egy IP-címblokkot CIDR-jelölésrendszer használatával. A CIDR-jelölés szerint például a 192.168.100.0/22 a 192.168.100.0 és a 192.168.103.255 közötti 1024 IPv4-címet jelöli. |


### <a name="update-an-ip-filter-rule"></a>IP-szűrési szabály frissítése

A következő példasablon a korábban bemutatott „AllowAll” nevű IP-szűrési szabályt frissíti az összes forgalom elutasítása céljából.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>IP-szűrési szabály törlése

A következő példasablon törli a DPS-példány összes IP-szűrési szabályát.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály kiértékelése

A rendszer az IP-szűrési szabályokat sorrendben alkalmazza, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletét.

Ha például fogadni szeretné a 192.168.100.0/22 tartomány címeit, minden mást viszont elutasítana, a táblázat első szabályának el kell fogadnia a 192.168.100.0/22 címtartományt. A következő szabálynak el kell utasítania minden címet a 0.0.0.0/0 tartomány használatával.

Az IP-szűrési szabályok sorrendjét a sorok elején lévő három, függőlegesen elhelyezett pontra kattintva, húzással módosíthatja.

Az IP-szűrési szabályok új sorrendjének mentéséhez kattintson a **Mentés** gombra.

![DPS IP-szűrési szabályok sorrendjének módosítása](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Következő lépések

A felügyeleti DPS alaposabb megismeréséhez tekintse meg a következőket:

* [Az IoT DPS IP-címek ismertetése](iot-dps-understand-ip-address.md)
* [A DPS beállítása az Azure CLI használatával](quick-setup-auto-provision-cli.md)
* [A DPS-hez való hozzáférés szabályozása](how-to-control-access.md)
