---
title: Felügyeleti végpont IP-címének felderítése
titleSuffix: Azure SQL Managed Instance
description: Ismerje meg, hogyan kérheti le az Azure SQL felügyelt példányok felügyeleti végpontjának nyilvános IP-címét, és ellenőrizze a beépített tűzfal védelmét
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: a9a2b904bd7526f00a8f8a5d013be0c1e42e38a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91617366"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>A felügyeleti végpont IP-címének meghatározása – az Azure SQL felügyelt példánya 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányának virtuális fürtje olyan felügyeleti végpontot tartalmaz, amelyet az Azure a felügyeleti műveletekhez használ. A felügyeleti végpontot a rendszer egy beépített tűzfallal védi a hálózati szinten, valamint a kölcsönös tanúsítvány-ellenőrzést az alkalmazás szintjén. Meghatározhatja a felügyeleti végpont IP-címét, de ehhez a végponthoz nem férhet hozzá.

A felügyeleti IP-cím meghatározásához hajtson végre [DNS-lekérdezést](/windows-server/administration/windows-commands/nslookup) az SQL felügyelt PÉLDÁNYÁNAK teljes tartománynevén: `mi-name.zone_id.database.windows.net` . Ez egy olyan DNS-bejegyzést ad vissza, amely hasonló `trx.region-a.worker.vnet.database.windows.net` . Ezt követően a ". vnet" nevű DNS-lekérdezést törölheti a teljes tartománynévre. Ekkor a rendszer visszaküldi a felügyeleti IP-címet. 

Ez a PowerShell-kód mindent megtesz, ha \<MI FQDN\> az SQL felügyelt példányának DNS-bejegyzésével helyettesíti a következőt: `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

További információ az SQL felügyelt példányáról és kapcsolatáról: az [Azure SQL felügyelt példányának kapcsolati architektúrája](connectivity-architecture-overview.md).
