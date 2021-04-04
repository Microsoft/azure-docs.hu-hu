---
title: A TLS-verzió minimálisan felügyelt példányának konfigurálása
description: Megtudhatja, hogyan konfigurálhat minimális TLS-verziót a felügyelt példányhoz
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 17d430946f3cba1aa4680d1eaf8979fa4338bc22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788400"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Minimális TLS-verzió konfigurálása felügyelt Azure SQL-példányon
A minimális [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) verzió beállítása lehetővé teszi, hogy az ügyfelek szabályozzák az Azure SQL felügyelt példányai által használt TLS-verziót.

Jelenleg a TLS 1.0-s, 1.1-es és 1.2-es verziója támogatott. Egy minimális TLS-verzió beállítása biztosítja, hogy a következő, újabb TLS-verziók támogatottak legyenek. Például például a 1,1-nál nagyobb TLS-verzió választása. azt jelenti, hogy a rendszer csak a TLS 1.1-es és 1.2-es verziójú kapcsolatokat fogadja el, a TLS 1.0-s verziójúakat elutasítja. Miután megerősítette, hogy az alkalmazások támogatják, ajánlott az 1.2-es minimális TLS-verziót beállítani, mert ez tartalmazza a korábbi verziókban található biztonsági rések javításait, és ez a felügyelt Azure SQL-példányban támogatott legmagasabb TLS-verzió.

A TLS régebbi verzióit használó ügyfelek esetében javasoljuk, hogy az alkalmazások követelményeinek megfelelően állítsa be a TLS minimális verziószámát. Azon ügyfelek esetében, akik nem titkosított kapcsolaton keresztül kapcsolódnak az alkalmazásokhoz, javasoljuk, hogy ne állítson be minimális TLS-verziót. 

További információ: TLS- [megfontolások SQL Database kapcsolathoz](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

A minimális TLS-verzió beállítása után a bejelentkezés a következő hibával meghiúsul, ha a TLS-t használó ügyfelek nem a minimális TLS-verziót használják:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Minimális TLS-verzió beállítása a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A következő parancsfájlhoz a [Azure PowerShell modul](/powershell/azure/install-az-ps)szükséges.

A következő PowerShell-szkript bemutatja, hogyan `Get` és `Set` a **minimális TLS Version** tulajdonságot a példány szintjén:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Minimális TLS-verzió beállítása az Azure CLI-n keresztül

> [!IMPORTANT]
> Az ebben a szakaszban szereplő összes parancsfájlhoz az [Azure CLI](/cli/azure/install-azure-cli)szükséges.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI bash-rendszerhéjban

A következő CLI-szkript bemutatja, hogyan módosítható a **minimális TLS-verzió** beállítása egy bash-rendszerhéjban:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```