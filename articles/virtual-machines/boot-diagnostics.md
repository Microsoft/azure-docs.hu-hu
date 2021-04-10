---
title: Azure rendszerindítási diagnosztika
description: Az Azure rendszerindítási diagnosztika és a felügyelt rendszerindítási diagnosztika áttekintése
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 030f19d61e19349de7e2d3416a3aee1385fd03c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607522"
---
# <a name="azure-boot-diagnostics"></a>Azure rendszerindítási diagnosztika

A rendszerindítási diagnosztika az Azure Virtual Machines (VM) hibakeresési funkciója, amely lehetővé teszi a virtuális gépek rendszerindítási hibáinak diagnosztizálását. A rendszerindítási diagnosztika lehetővé teszi a felhasználók számára, hogy betartsák a virtuális gép állapotát, mivel a soros napló információi és képernyőképek összegyűjtésével indulnak.

## <a name="boot-diagnostics-storage-account"></a>Rendszerindítási diagnosztika Storage-fiókja
Amikor Azure Portalban hoz létre virtuális gépet, a rendszerindítási diagnosztika alapértelmezés szerint engedélyezve van. Az ajánlott rendszerindítási diagnosztika a felügyelt Storage-fiók használata, mivel az Azure-beli virtuális gép létrehozásához szükséges idő jelentős teljesítménybeli javítást eredményez. Ennek az az oka, hogy egy Azure által felügyelt Storage-fiókot kell használni, és el kell távolítani a rendszerindítási diagnosztikai adatait tároló új felhasználói Storage-fiók létrehozásához szükséges időt.

> [!IMPORTANT]
> A rendszerindítási diagnosztika adatblobok (amelyek a naplókból és a pillanatkép-lemezképből állnak) egy felügyelt Storage-fiókban tárolódnak. Az ügyfeleknek csak a Blobok által használt GiBs kell fizetniük, nem pedig a lemez kiosztott méretétől. A rendszer a felügyelt Storage-fiók számlázására szolgáló pillanatkép-mérőszámokat használja. Mivel a felügyelt fiókok szabványos LRS vagy standard szintű ZRS jönnek létre, a rendszer havi 0,05/GB-onként díjat számít fel a diagnosztikai adatblobok méretére. A díjszabással kapcsolatos további információkért lásd: [felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/). Az ügyfelek ezt a díjat a virtuális gép erőforrás-URI-ja alapján fogják látni. 

Egy alternatív rendszerindítási diagnosztikai megoldás egy felhasználó által felügyelt Storage-fiók használata. Egy felhasználó létrehozhat egy új Storage-fiókot, vagy használhat egy meglévőt is.
> [!NOTE]
> A rendszerindítási diagnosztika szolgáltatáshoz társított, felhasználó által felügyelt Storage-fiókok esetében a Storage-fióknak és a társított virtuális gépnek ugyanabban az előfizetésben kell lennie. 



## <a name="boot-diagnostics-view"></a>Rendszerindítási diagnosztika nézet
A virtuális gép paneljén a rendszerindítási diagnosztika lehetőség a Azure Portal *támogatási és hibaelhárítási* szakaszában található. A rendszerindítási diagnosztika lehetőségre kattintva megjelenik a képernyőkép és a soros napló adatai. A soros napló kernel-üzenetkezelést tartalmaz, és a képernyőkép a virtuális gépek aktuális állapotának pillanatképe. Attól függően, hogy a virtuális gép Windows vagy Linux rendszert futtat-e, meghatározza, hogy a várt képernyőkép hogyan fog kinézni. A Windowsban a felhasználók asztali hátteret és Linux rendszerű felhasználókat látnak, és a felhasználók egy bejelentkezési kérést fognak látni.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="A Linux rendszerindítási diagnosztika képernyőképe":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="A Windows rendszerindítási diagnosztika képernyőképe":::

## <a name="enable-managed-boot-diagnostics"></a>Felügyelt rendszerindítási diagnosztika engedélyezése 
A felügyelt rendszerindítási diagnosztika a Azure Portal, a CLI és az ARM sablonok használatával engedélyezhető. A PowerShell használatával történő engedélyezés még nem támogatott. 

### <a name="enable-managed-boot-diagnostics-using-the-azure-portal"></a>Felügyelt rendszerindítási diagnosztika engedélyezése a Azure Portal használatával
Amikor virtuális gépet hoz létre a Azure Portalban, az alapértelmezett beállítás az, hogy a rendszerindítási diagnosztika engedélyezve legyen a felügyelt Storage-fiók használatával. Ennek megtekintéséhez navigáljon a *kezelés* lapra a virtuális gép létrehozása során. 

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-enable-portal.png" alt-text="Képernyőfelvétel a felügyelt rendszerindítási diagnosztika engedélyezéséről a virtuális gépek létrehozása során.":::

### <a name="enable-managed-boot-diagnostics-using-cli"></a>Felügyelt rendszerindítási diagnosztika engedélyezése a parancssori felület használatával
A felügyelt Storage-fiókkal rendelkező rendszerindítási diagnosztika az Azure CLI 2.12.0 és újabb verzióiban támogatott. Ha nem adja meg a Storage-fiók nevét vagy URI azonosítóját, a rendszer felügyelt fiókot fog használni. További információkat és kódokat a [rendszerindítási diagnosztika parancssori felületének dokumentációjában](/cli/azure/vm/boot-diagnostics)talál.

### <a name="enable-managed-boot-diagnostics-using-azure-resource-manager-arm-templates"></a>Felügyelt rendszerindítási diagnosztika engedélyezése Azure Resource Manager (ARM) sablonok használatával
Minden, a 2020-06-01-es API-verzió támogatja a felügyelt rendszerindítási diagnosztikát. További információ: [rendszerindítási diagnosztikai példány nézet](/rest/api/compute/virtualmachines/createorupdate#bootdiagnostics).

```ARM Template
            "name": "[parameters('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2020-06-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[parameters('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[parameters('virtualMachineComputerName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "linuxConfiguration": {
                        "disablePasswordAuthentication": true
                    }
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true
                    }
                }
            }
        }
    ],

```

## <a name="limitations"></a>Korlátozások
- A rendszerindítási diagnosztika csak Azure Resource Manager virtuális gépek esetében érhető el.
- A felügyelt rendszerindítási diagnosztika nem támogatja a nem felügyelt operációsrendszer-lemezeket használó virtuális gépeket.
- A rendszerindítási diagnosztika nem támogatja a Premium Storage-fiókokat, ha prémium szintű Storage-fiókot használ a rendszerindítási diagnosztikai felhasználóknak a `StorageAccountTypeNotSupported` virtuális gép indításakor hibaüzenet jelenik meg. 
- A felügyelt Storage-fiókok a Resource Manager API "2020-06-01" és újabb verzióiban támogatottak.
- Az Azure soros konzol jelenleg nem kompatibilis a rendszerindítási diagnosztika felügyelt tárolási fiókjával. További információ az [Azure soros konzolról](/troubleshoot/azure/virtual-machines/serial-console-overview).
- A portál csak az Egypéldányos virtuális gépekhez tartozó felügyelt Storage-fiókkal támogatja a rendszerindítási diagnosztika használatát.

## <a name="next-steps"></a>Következő lépések

További információ az [Azure soros konzolról](/troubleshoot/azure/virtual-machines/serial-console-overview) , valamint a [virtuális gépek Azure-ban való hibakeresésének](/troubleshoot/azure/virtual-machines/boot-diagnostics)rendszerindítási diagnosztika használatával történő használatáról.