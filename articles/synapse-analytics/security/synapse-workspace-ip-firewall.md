---
title: IP-tűzfalszabályok konfigurálása
description: Egy cikk, amely megtanítja az IP-tűzfalszabályok konfigurálását az Azure szinapszis Analyticsben
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 787e600df4185346b250d9a96bdb89927e134075
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472024"
---
# <a name="azure-synapse-analytics-ip-firewall-rules"></a>Az Azure szinapszis Analytics IP-tűzfalszabályok

Ez a cikk ismerteti az IP-tűzfalszabályok szabályait, és megtanítja, hogyan konfigurálhatja őket az Azure szinapszis Analyticsben.

## <a name="ip-firewall-rules"></a>IP-tűzfalszabályok

Az IP-tűzfalszabályok engedélyezik vagy tiltják a Synapse-munkaterülethez való hozzáférést az egyes kérések kiindulási IP-címe alapján. Konfigurálhat IP-tűzfalszabályokat a munkaterületéhez. A munkaterület szintjén konfigurált IP-tűzfalszabályok a munkaterület összes nyilvános végpontján érvényesek (dedikált SQL-készletek, kiszolgáló nélküli SQL-készlet és fejlesztés).

## <a name="create-and-manage-ip-firewall-rules"></a>IP-tűzfalszabályok létrehozása és kezelése

Az IP-tűzfalszabályok kétféleképpen vehetők fel egy szinapszis-munkaterületre. Ha IP-tűzfalat szeretne hozzáadni a munkaterülethez, válassza a **Biztonság és hálózatkezelés** lehetőséget, és jelölje be az **összes IP-cím kapcsolatának engedélyezése** a munkaterület létrehozása során jelölőnégyzetet.

![Képernyőfelvétel: a biztonság és hálózatkezelés gomb kiemelése.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Azure Portal a szinapszis-munkaterület IP-konfigurációját.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

A munkaterület létrehozása után IP-tűzfalszabályok is hozzáadhatók egy szinapszis-munkaterülethez. Válasszon **tűzfalat** a Azure Portal **Biztonság** területen. Új IP-tűzfalszabály hozzáadásához adja meg a nevet, a kezdő IP-címet és a záró IP-címet. Ha végzett, kattintson a **Mentés** gombra.

![Az Azure szinapszis munkaterület IP-konfigurációja Azure Portalban.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connect-to-synapse-from-your-own-network"></a>Csatlakozás a Synapse-hez a saját hálózatáról

A szinapszis-munkaterülethez a szinapszis Studio használatával csatlakozhat. SQL Server Management Studio (SSMS) használatával is csatlakozhat az SQL-erőforrásokhoz (dedikált SQL-készletek és kiszolgáló nélküli SQL-készlet) a munkaterületen.

Győződjön meg arról, hogy a hálózat és a helyi számítógép tűzfala lehetővé teszi a kimenő kommunikációt a 80, 443 és 1443 TCP-portokon a szinapszis studióhoz.

Emellett engedélyeznie kell a kimenő kommunikációt a 53-es UDP-porton a szinapszis studióhoz. Ha a SSMS és a Power BI eszközzel szeretne csatlakozni, engedélyeznie kell a kimenő kommunikációt az 1433-as TCP-porton.


## <a name="next-steps"></a>Következő lépések

[Azure szinapszis-munkaterület](../quickstart-create-workspace.md) létrehozása

Azure szinapszis-munkaterület létrehozása [felügyelt munkaterülettel Virtual Network](./synapse-workspace-managed-vnet.md)