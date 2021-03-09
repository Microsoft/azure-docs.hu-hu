---
title: IP-csoportok a Azure Firewall
description: Az IP-csoportok lehetővé teszik Azure Firewall szabályok IP-címeinek csoportosítását és kezelését.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: victorh
ms.openlocfilehash: 907ed0774db0e07a61a3ad568e6c451ea5d37b44
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501736"
---
# <a name="ip-groups-in-azure-firewall"></a>IP-csoportok a Azure Firewall

Az IP-csoportok lehetővé teszik, hogy az alábbi módokon csoportosítsa és kezelje az Azure Firewall szabályok IP-címeit:

- Forrásként szolgáló DNAT-szabályok
- Forrásként vagy célként megadott címek a hálózati szabályokban
- Az alkalmazási szabályokban szereplő forráscím


Egy IP-csoport rendelkezhet egyetlen IP-címmel, több IP-címmel vagy egy vagy több IP-címtartományból.

Az IP-csoportok újra felhasználhatók Azure Firewall DNAT, hálózati és alkalmazási szabályokban több tűzfalon az Azure-ban. A csoportok nevének egyedinek kell lennie. Az IP-csoportokat a Azure Portalban, az Azure CLI-ben vagy a REST API is konfigurálhatja. Az első lépésekhez egy minta sablont biztosítunk.

> [!NOTE]
> Az IP-csoportok jelenleg nem érhetők el az Azure-beli nemzeti Felhőbeli környezetekben.

## <a name="sample-format"></a>Minták formátuma

Az alábbi IPv4-címformátum példák érvényesek az IP-csoportokban való használatra:

- Egyetlen címe: 10.0.0.0
- CIDR jelölése: 10.1.0.0/32
- Címtartomány: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>IP-csoport létrehozása

Az IP-csoportokat a Azure Portal, az Azure CLI vagy a REST API használatával lehet létrehozni. További információt az [IP-csoport létrehozása](create-ip-group.md)című témakörben talál.

## <a name="browse-ip-groups"></a>IP-csoportok tallózása
1. A Azure Portal keresési sávban írja be az **IP-csoportok** kifejezést, és jelölje ki. Megtekintheti az IP-csoportok listáját, vagy kiválaszthatja a **Hozzáadás** lehetőséget egy új IP-csoport létrehozásához.
2. Válasszon ki egy IP-csoportot az Áttekintés lap megnyitásához. Szerkesztheti, hozzáadhatja vagy törölheti az IP-címeket vagy IP-csoportokat.

   ![IP-csoportok – áttekintés](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>IP-csoport kezelése

Megtekintheti az IP-csoport összes IP-címét és a hozzá társított szabályokat vagy erőforrásokat. Egy IP-csoport törléséhez először el kell távolítania az IP-csoportot az azt használó erőforrásból.

1. Az IP-címek megtekintéséhez vagy szerkesztéséhez válassza az **IP-címek** lehetőséget a bal oldali ablaktábla **Beállítások** területén.
2. Egyetlen vagy több IP-cím hozzáadásához válassza az **IP-címek hozzáadása** lehetőséget. Ekkor megnyílik a feltöltés **húzása vagy tallózása** oldal, vagy manuálisan is megadhatja a címeket.
3.    Válassza ki a jobbra lévő három pontot (**..**.) az IP-címek szerkesztéséhez vagy törléséhez. Több IP-cím szerkesztéséhez vagy törléséhez jelölje be a jelölőnégyzeteket, majd kattintson a felül található **Szerkesztés** vagy **Törlés** lehetőségre.
4. Végül a CSV-fájlformátumban is exportálhatja a fájlt.

> [!NOTE]
> Ha egy IP-csoportban lévő összes IP-címet törli, miközben egy szabályban még használatban van, a rendszer kihagyja a szabályt.


## <a name="use-an-ip-group"></a>IP-csoport használata

Mostantól kiválaszthatja az **IP-csoport** lehetőséget az IP-cím (ek) **forrás** **-vagy DNAT** , amikor Azure Firewall-t, alkalmazást vagy hálózati szabályt hoz létre.

![IP-csoportok a tűzfalban](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az IP-csoportok minden nyilvános Felhőbeli régióban elérhetők.

## <a name="ip-address-limits"></a>IP-címek korlátai

Tűzfalon legfeljebb 100 IP-csoport lehet, maximális 5000 egyedi IP-cím vagy IP-előtag minden IP-csoportonként.

## <a name="related-azure-powershell-cmdlets"></a>Kapcsolódó Azure PowerShell-parancsmagok

Az IP-csoportok létrehozásához és kezeléséhez a következő Azure PowerShell parancsmagokat használhatja:

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup)
- [Remove-AzIPGroup](/powershell/module/az.network/remove-azipgroup)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup)
- [Új – AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
- [Új – AzFirewallApplicationRule](/powershell/module/az.network/new-azfirewallapplicationrule)
- [Új – AzFirewallNatRule](/powershell/module/az.network/new-azfirewallnatrule)

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [helyezhet üzembe és konfigurálhat egy Azure Firewall](tutorial-firewall-deploy-portal.md).