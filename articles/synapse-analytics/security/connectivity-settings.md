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
ms.openlocfilehash: e0d8a8e3320b49b6fbe3e8ab66c0b4569fac9afd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587933"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Az Azure szinapszis Analytics csatlakozási beállításai

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure szinapszis Analytics kapcsolati beállításait, és hogyan konfigurálhatja őket, ha szükséges.


## <a name="connection-policy"></a>Kapcsolatok házirendje
Az Azure szinapszis Analytics szolgáltatáshoz készült szinapszis SQL kapcsolódási házirendje *alapértelmezett* értékre van állítva. Ez az Azure szinapszis Analyticsben nem módosítható. [Itt](../../azure-sql/database/connectivity-architecture.md#connection-policy)tájékozódhat arról, hogy az hogyan érinti az Azure-beli szinapszis SQL-kapcsolatait az Azure szinapszis Analyticsben. 

## <a name="minimal-tls-version"></a>Minimális TLS-verzió
A szinapszis SQL az Azure szinapszis Analyticsben lehetővé teszi a kapcsolatok használatát az összes TLS-verzióval. Az Azure szinapszis Analyticsben nem állítható be a szinapszis SQL minimális TLS-verziója.

## <a name="next-steps"></a>Következő lépések

[Azure szinapszis-munkaterület](./synapse-workspace-ip-firewall.md) létrehozása