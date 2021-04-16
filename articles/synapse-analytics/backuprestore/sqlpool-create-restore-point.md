---
title: Felhasználó által megadott visszaállítási pont létrehozása dedikált SQL-készlethez
description: Megtudhatja, hogyan használhatja a Azure Portal egy felhasználó által definiált visszaállítási pontot dedikált SQL-készlethez a Azure Synapse Analytics.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b695f6c7aabc21541fcc48efed54bbecd022f65a
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567927"
---
# <a name="user-defined-restore-points"></a>Felhasználó által definiált visszaállítási pontok

Ebből a cikkből megtudhatja, hogyan hozhat létre új, felhasználó által definiált visszaállítási pontot egy dedikált SQL-készlethez a Azure Synapse Analytics a Azure Portal.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Felhasználó által definiált visszaállítási pontok létrehozása a Azure Portal

Felhasználó által definiált visszaállítási pontok is Azure Portal.

1. Jelentkezzen be [Azure Portal](https://portal.azure.com/) fiókjába.

2. Keresse meg azt a dedikált SQL-készletet, amely számára visszaállítási pontot kíván létrehozni.

3. A **bal oldali panelen** válassza az Áttekintés, majd az **+ Új visszaállítási pont lehetőséget.** Ha az Új visszaállítási pont gomb nincs engedélyezve, győződjön meg arról, hogy a dedikált SQL-készlet nincs szüneteltetve.

    ![Új visszaállítási pont](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Adja meg a felhasználó által megadott visszaállítási pont nevét, majd kattintson az **Alkalmaz gombra.** A felhasználó által megadott visszaállítási pontok alapértelmezett megőrzési ideje hét nap.

    ![A visszaállítási pont neve](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Következő lépések

- [Meglévő dedikált SQL-készlet visszaállítása](restore-sql-pool.md)

