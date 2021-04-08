---
title: Konfigurálhatja az Azure Firewall alkalmazásszabályait teljes SQL-tartománynevekkel
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja az SQL FQDN-ket Azure Firewall alkalmazási szabályokban.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/18/2020
ms.author: victorh
ms.openlocfilehash: c65f32cc3ce56ddf3fd235de8c002528e7a3cebd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791442"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Konfigurálhatja az Azure Firewall alkalmazásszabályait teljes SQL-tartománynevekkel

Mostantól az SQL FQDN-sel is konfigurálhatja Azure Firewall alkalmazási szabályokat. Ez lehetővé teszi, hogy a virtuális hálózatokról csak a megadott SQL Server-példányokra korlátozza a hozzáférést.

Az SQL teljes tartományneveivel szűrheti a forgalmat:

- A virtuális hálózatok egy Azure SQL Database vagy az Azure szinapszis Analytics szolgáltatásba. Például: csak a *SQL-server1.database.Windows.net* elérésének engedélyezése.
- A helyszínről az Azure SQL felügyelt példányaira vagy a virtuális hálózatok futó SQL-IaaS.
- Az Azure SQL felügyelt példányain vagy a virtuális hálózatok futó SQL-IaaS.

Az SQL FQDN-szűrés csak [proxy módban](../azure-sql/database/connectivity-architecture.md#connection-policy) támogatott (1433-es port). Ha az SQL-t az alapértelmezett átirányítási módban használja, a [hálózati szabályok](features.md#network-traffic-filtering-rules)részeként szűrheti a hozzáférést az SQL-szolgáltatás címkével.
Ha nem alapértelmezett portokat használ az SQL IaaS-forgalomhoz, ezeket a portokat a tűzfal alkalmazásszabályaiban állíthatja be.

## <a name="configure-using-azure-cli"></a>Konfigurálás az Azure CLI használatával

1. Azure Firewall üzembe helyezése az [Azure CLI használatával](deploy-cli.md).
2. Ha Azure SQL Databasere, Azure szinapszis Analyticsre vagy SQL felügyelt példányra szűri a forgalmat, győződjön meg arról, hogy az SQL-kapcsolati mód **proxyra** van beállítva. Az SQL-kapcsolati mód váltásával kapcsolatos további információkért lásd: [Azure SQL-kapcsolati beállítások](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).

   > [!NOTE]
   > Az SQL *proxy* mód több késést eredményezhet az *átirányításhoz* képest. Ha továbbra is az átirányítási módot szeretné használni, amely az Azure-on keresztül csatlakozó ügyfelek esetében az alapértelmezett, akkor a hozzáférést a tűzfal [hálózati szabályaiban](tutorial-firewall-deploy-portal.md#configure-a-network-rule)található SQL- [szolgáltatás címkével](service-tags.md) szűrheti.

3. Új szabálygyűjtemény létrehozása az SQL FQDN-t használó alkalmazás-szabállyal az SQL Serverhez való hozzáférés engedélyezéséhez:

   ```azurecli
    az extension add -n azure-firewall
    
    az network firewall application-rule create \ 
    -g FWRG \
    --f azfirewall \ 
    --c sqlRuleCollection \
    --priority 1000 \
    --action Allow \
    --name sqlRule \
    --protocols mssql=1433 \
    --source-addresses 10.0.0.0/24 \
    --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-azure-powershell"></a>Konfigurálás Azure PowerShell használatával

1. Azure Firewall üzembe helyezése [Azure PowerShell használatával](deploy-ps.md).
2. Ha Azure SQL Databasere, Azure szinapszis Analyticsre vagy SQL felügyelt példányra szűri a forgalmat, győződjön meg arról, hogy az SQL-kapcsolati mód **proxyra** van beállítva. Az SQL-kapcsolati mód váltásával kapcsolatos további információkért lásd: [Azure SQL-kapcsolati beállítások](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).

   > [!NOTE]
   > Az SQL *proxy* mód több késést eredményezhet az *átirányításhoz* képest. Ha továbbra is az átirányítási módot szeretné használni, amely az Azure-on keresztül csatlakozó ügyfelek esetében az alapértelmezett, akkor a hozzáférést a tűzfal [hálózati szabályaiban](tutorial-firewall-deploy-portal.md#configure-a-network-rule)található SQL- [szolgáltatás címkével](service-tags.md) szűrheti.

3. Új szabálygyűjtemény létrehozása az SQL FQDN-t használó alkalmazás-szabállyal az SQL Serverhez való hozzáférés engedélyezéséhez:

   ```azurepowershell
   $AzFw = Get-AzFirewall -Name "azfirewall" -ResourceGroupName "FWRG"
    
   $sqlRule = @{
      Name          = "sqlRule"
      Protocol      = "mssql:1433" 
      TargetFqdn    = "sql-serv1.database.windows.net"
      SourceAddress = "10.0.0.0/24"
   }
    
   $rule = New-AzFirewallApplicationRule @sqlRule
    
   $sqlRuleCollection = @{
      Name       = "sqlRuleCollection" 
      Priority   = 1000 
      Rule       = $rule
      ActionType = "Allow"
   }
    
   $ruleCollection = New-AzFirewallApplicationRuleCollection @sqlRuleCollection
    
   $Azfw.ApplicationRuleCollections.Add($ruleCollection)    
   Set-AzFirewall -AzureFirewall $AzFw    
   ```

## <a name="configure-using-the-azure-portal"></a>Konfigurálás az Azure Portal használatával
1. Azure Firewall üzembe helyezése az [Azure CLI használatával](deploy-cli.md).
2. Ha Azure SQL Databasere, Azure szinapszis Analyticsre vagy SQL felügyelt példányra szűri a forgalmat, győződjön meg arról, hogy az SQL-kapcsolati mód **proxyra** van beállítva. Az SQL-kapcsolati mód váltásával kapcsolatos további információkért lásd: [Azure SQL-kapcsolati beállítások](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).  

   > [!NOTE]
   > Az SQL *proxy* mód több késést eredményezhet az *átirányításhoz* képest. Ha továbbra is az átirányítási módot szeretné használni, amely az Azure-on keresztül csatlakozó ügyfelek esetében az alapértelmezett, akkor a hozzáférést a tűzfal [hálózati szabályaiban](tutorial-firewall-deploy-portal.md#configure-a-network-rule)található SQL- [szolgáltatás címkével](service-tags.md) szűrheti.
3. Adja hozzá az alkalmazási szabályt a megfelelő protokoll-, port-és SQL FQDN- **fájlhoz**, majd válassza a mentés lehetőséget.
   ![alkalmazási szabály SQL FQDN-sel](media/sql-fqdn-filtering/application-rule-sql.png)
4. SQL-hozzáférés egy virtuális gépről egy olyan VNet, amely a tűzfalon keresztül szűri a forgalmat. 
5. Annak ellenőrzése, hogy a [Azure Firewall naplókban](./firewall-workbook.md) engedélyezett-e a forgalom.

## <a name="next-steps"></a>Következő lépések

Az SQL proxy és az átirányítási módok megismeréséhez tekintse meg [Azure SQL Database kapcsolati architektúrát](../azure-sql/database/connectivity-architecture.md).