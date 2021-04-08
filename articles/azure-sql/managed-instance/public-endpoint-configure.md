---
title: Nyilvános végpont konfigurálása – felügyelt Azure SQL-példány
description: Megtudhatja, hogyan konfigurálhat nyilvános végpontot az Azure SQL felügyelt példányaihoz
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, sstein
ms.date: 02/08/2021
ms.openlocfilehash: 7d5f40be895aea26a234d9ae622aa5bf22528231
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99981442"
---
# <a name="configure-public-endpoint-in-azure-sql-managed-instance"></a>Nyilvános végpont konfigurálása az Azure SQL felügyelt példányában
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A [felügyelt példányok](./sql-managed-instance-paas-overview.md) nyilvános végpontja lehetővé teszi a felügyelt példányhoz való adathozzáférést a [virtuális hálózaton](../../virtual-network/virtual-networks-overview.md)kívülről. Felügyelt példányát a több-bérlős Azure-szolgáltatásokból (például Power BI, Azure App Service vagy egy helyszíni hálózatból) érheti el. A felügyelt példányok nyilvános végpontjának használatával nem szükséges VPN-t használnia, ami segít elkerülni a VPN átviteli sebességét.

Ebből a cikkből megtudhatja, hogyan:

> [!div class="checklist"]
>
> - Nyilvános végpont engedélyezése a felügyelt példányhoz a Azure Portal
> - Nyilvános végpont engedélyezése a felügyelt példány számára a PowerShell használatával
> - Felügyelt példány hálózati biztonsági csoportjának konfigurálása a felügyelt példány nyilvános végpontjának forgalmának engedélyezéséhez
> - Felügyelt példány nyilvános végpontjának összekapcsolási karakterláncának beszerzése

## <a name="permissions"></a>Engedélyek

A felügyelt példányban lévő adatérzékenység miatt a felügyelt példány nyilvános végpontjának engedélyezéséhez kétlépéses folyamat szükséges. Ez a biztonsági mérték a feladatok elkülönítését (SoD) követi:

- A felügyelt példányok nyilvános végpontjának engedélyezését a felügyelt példány rendszergazdájának kell elvégeznie. A felügyelt példány rendszergazdája a felügyelt példány erőforrásának **Áttekintés** lapján található.
- Olyan hálózati biztonsági csoporttal történő adatforgalom engedélyezése, amelyet egy hálózati rendszergazdának kell elvégeznie. További információ: [hálózati biztonsági csoport engedélyei](../../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Felügyelt példányok nyilvános végpontjának engedélyezése a Azure Portalban

1. A Azure Portal elindítása <https://portal.azure.com/.>
1. Nyissa meg az erőforráscsoportot a felügyelt példánnyal, és válassza ki azt az **SQL felügyelt példányt** , amelyen a nyilvános végpontot konfigurálni kívánja.
1. A **biztonsági** beállítások lapon válassza a **virtuális hálózat** lapot.
1. A virtuális hálózat konfigurációja lapon válassza az **Engedélyezés** , majd a **Mentés** ikont a konfiguráció frissítéséhez.

![A képernyőképen az SQL felügyelt példányának virtuális hálózati lapja látható, amelyen engedélyezve van a nyilvános végpont.](./media/public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Felügyelt példány nyilvános végpontjának engedélyezése a PowerShell használatával

### <a name="enable-public-endpoint"></a>Nyilvános végpont engedélyezése

Futtassa a következő PowerShell-parancsokat. Cserélje le az **előfizetés-azonosítót** az előfizetés-azonosítójával. Továbbá cserélje le a **RG nevet** a felügyelt példányhoz tartozó erőforráscsoporthoz, és cserélje le a **mi-Name nevet** a felügyelt példány nevére.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Nyilvános végpont letiltása

Ha a nyilvános végpontot a PowerShell használatával szeretné letiltani, hajtsa végre a következő parancsot (és ne feledkezzen meg a 3342 bejövő port NSG bezárásáról is, ha konfigurálva van):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Nyilvános végponti forgalom engedélyezése a hálózati biztonsági csoportban

1. Ha a felügyelt példány konfigurációs lapja továbbra is nyitva van, navigáljon az **Áttekintés** lapra. Ellenkező esetben lépjen vissza az **SQL felügyelt példányának** erőforrására. Válassza ki a **virtuális hálózat/alhálózat** hivatkozást, amely a virtuális hálózat konfigurációja lapra kerül.

    ![Képernyőfelvétel: a virtuális hálózat konfigurációja oldal, ahol megtalálhatja a virtuális hálózat/alhálózat értékét.](./media/public-endpoint-configure/mi-overview.png)

1. Válassza az **alhálózatok** fület a virtuális hálózat bal oldali konfigurációs paneljén, és jegyezze fel a felügyelt példány **biztonsági csoportját** .

    ![A képernyőképen az alhálózat lap látható, ahol megtekintheti a felügyelt példány biztonsági CSOPORTját.](./media/public-endpoint-configure/mi-vnet-subnet.png)

1. Lépjen vissza a felügyelt példányt tartalmazó erőforráscsoporthoz. Ekkor meg kell jelennie a **hálózati biztonsági csoport** fent említett nevének. Válassza ki a hálózati biztonsági csoport konfigurációs lapjára felvenni kívánt nevet.

1. Válassza a **bejövő biztonsági szabályok** lapot, és **adjon hozzá** egy olyan szabályt, amely magasabb prioritású, mint a **deny_all_inbound** szabály a következő beállításokkal: </br> </br>

    |Beállítás  |Ajánlott érték  |Leírás  |
    |---------|---------|---------|
    |**Forrás**     |Bármely IP-cím vagy szolgáltatás címkéje         |<ul><li>Az Azure-szolgáltatások, például a Power BI esetében válassza az Azure Cloud Service címkét</li> <li>A számítógép vagy az Azure-beli virtuális gép esetében használja a NAT IP-címet</li></ul> |
    |**Forrásporttartományok**     |* |Hagyja ezt a lehetőséget * (bármely), mivel a forrás portjai általában dinamikusan vannak lefoglalva, és mint ilyen, kiszámíthatatlan |
    |**Cél**     |Bármelyik         |A célhely kihagyása a felügyelt példány alhálózatára való adatforgalom engedélyezéséhez |
    |**Célporttartományok**     |3342         |Hatóköri célport a 3342-re, amely a felügyelt példány nyilvános TDS-végpontja |
    |**Protokoll**     |TCP         |Az SQL felügyelt példánya a TDS protokollhoz TCP protokollt használ. |
    |**Művelet**     |Engedélyezés         |Felügyelt példány bejövő forgalmának engedélyezése a nyilvános végponton keresztül |
    |**Prioritás**     |1300         |Győződjön meg arról, hogy ez a szabály magasabb prioritású, mint a **deny_all_inbound** szabály |

    ![A képernyőképen a beérkező biztonsági szabályok a deny_all_inbound szabály fölötti új public_endpoint_inbound szabállyal láthatók.](./media/public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Az 3342-es port a felügyelt példányhoz tartozó nyilvános végponti kapcsolatokhoz használatos, és ezen a ponton nem módosítható.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>A felügyelt példány nyilvános végpontjának összekapcsolási karakterláncának beszerzése

1. Navigáljon a felügyelt példány konfigurálása lapra, amelyen engedélyezve van a nyilvános végpont. Válassza a **kapcsolatok karakterláncok** lapot a **Beállítások** konfiguráció alatt.
1. Vegye figyelembe, hogy a nyilvános végpont állomásneve <mi_name> formátumban jön. **Public**. <dns_zone>. database.Windows.net és a csatlakozáshoz használt port 3342. Íme egy példa a kapcsolati sztringre, amely a SQL Server Management Studio vagy Azure Data Studio kapcsolatokban használható nyilvános végponti portot jelöli: `<mi_name>.public.<dns_zone>.database.windows.net,3342`

    ![A képernyőképen a nyilvános és a privát végpontokhoz tartozó kapcsolatok karakterláncai láthatók.](./media/public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Következő lépések

További információ az [Azure SQL felügyelt példányának biztonságos használatáról nyilvános végponttal](public-endpoint-overview.md).
