---
title: Az Azure szinapszis kapcsolódási beállításai
description: Egy cikk, amely megtanítja az Azure szinapszis Analytics csatlakozási beállításainak konfigurálását
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 03/15/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ce1a4808833cbd897da17f9ad75af346538d23d1
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103473087"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Az Azure szinapszis Analytics csatlakozási beállításai

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure szinapszis Analytics kapcsolati beállításait, és hogyan konfigurálhatja őket, ha szükséges.


## <a name="connection-policy"></a>Kapcsolatok házirendje
Az Azure szinapszis Analytics szolgáltatáshoz készült szinapszis SQL kapcsolódási házirendje *alapértelmezett* értékre van állítva. Ez az Azure szinapszis Analyticsben nem módosítható. [Itt](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy)tájékozódhat arról, hogy az hogyan érinti az Azure-beli szinapszis SQL-kapcsolatait az Azure szinapszis Analyticsben. 

## <a name="minimal-tls-version"></a>Minimális TLS-verzió
A szinapszis SQL az Azure szinapszis Analyticsben lehetővé teszi a kapcsolatok használatát az összes TLS-verzióval. Az Azure szinapszis Analyticsben nem állítható be a szinapszis SQL minimális TLS-verziója.

## <a name="next-steps"></a>Következő lépések

[Azure szinapszis-munkaterület](./synapse-workspace-ip-firewall.md) létrehozása
