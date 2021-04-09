---
title: Azure Firewall házirendben szereplő IP-csoportok
description: Az IP-csoportok lehetővé teszik az IP-címek csoportosítását és kezelését Azure Firewall házirend-szabályokhoz.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 54faa0bc7d414eb15a866b8e1e6d09e15b22b071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500852"
---
# <a name="ip-groups-in-azure-firewall-policy"></a>Azure Firewall házirendben szereplő IP-csoportok

Az IP-csoportok lehetővé teszik, hogy az alábbi módokon csoportosítsa és kezelje Azure Firewall házirend IP-címeit:

- Forrás típusa a DNAT-szabályokban
- Forrásként vagy célként megadott típusként a hálózati szabályokban
- Forrás típusa az alkalmazás szabályaiban


Egy IP-csoport rendelkezhet egyetlen IP-címmel, több IP-címmel vagy egy vagy több IP-címtartományból.

Az IP-csoportok újra felhasználhatók Azure Firewall DNAT, hálózati és alkalmazási szabályokban több tűzfalon az Azure-ban. A csoportok nevének egyedinek kell lennie. Az IP-csoportokat a Azure Portalban, az Azure CLI-ben vagy a REST API is konfigurálhatja. Az első lépésekhez egy minta sablont biztosítunk.

## <a name="sample-format"></a>Minták formátuma

Az alábbi IPv4-címformátum példák érvényesek az IP-csoportokban való használatra:

- Egyetlen címe: 10.0.0.0
- CIDR jelölése: 10.1.0.0/32
- Címtartomány: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>IP-csoport létrehozása

Az IP-csoportokat a Azure Portal, az Azure CLI vagy a REST API használatával lehet létrehozni. További információt az [IP-csoport létrehozása](../firewall/create-ip-group.md)című témakörben talál.

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

Mostantól kiválaszthatja az **IP-csoport** lehetőséget az IP-cím (ek) **forrás** **-vagy DNAT** , amikor létrehoz egy szabályzatot az alkalmazással vagy a hálózati szabályokkal.

:::image type="content" source="media/ip-groups/firewall-ip-group.png" alt-text="IP-csoportok a tűzfalban":::

## <a name="ip-address-limits"></a>IP-címek korlátai

Tűzfalon legfeljebb 100 IP-csoport lehet, maximális 5000 egyedi IP-cím vagy IP-előtag minden IP-csoportonként.

## <a name="related-azure-powershell-cmdlets"></a>Kapcsolódó Azure PowerShell-parancsmagok

Az IP-csoportok létrehozásához és kezeléséhez a következő Azure PowerShell parancsmagokat használhatja:

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup)
- [Remove-AzIPGroup](/powershell/module/az.network/remove-azipgroup)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup)
- [Új – AzFirewallPolicyNetworkRule](/powershell/module/az.network/new-azfirewallpolicynetworkrule)
- [Új – AzFirewallPolicyApplicationRule](/powershell/module/az.network/new-azfirewallpolicyapplicationrule)
- [Új – AzFirewallPolicyNatRule](/powershell/module/az.network/new-azfirewallpolicynatrule)

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: virtuális WAN biztonságossá tétele a Azure Firewall Managerrel](secure-cloud-network.md)