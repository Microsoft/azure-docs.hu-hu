---
title: Az Azure Blockchain Workbench adatbázis-tűzfalának konfigurálása
description: Ismerje meg, hogyan konfigurálhatja az Azure Blockchain Workbench előzetes verziójú adatbázis-tűzfalát a külső ügyfelek és alkalmazások kapcsolódásának engedélyezéséhez.
ms.date: 09/09/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: dc7a1dc7e4a083ef8f5f7650f29ed1430b32e88b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009449"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Az Azure Blockchain Workbench-adatbázis tűzfalának konfigurálása

Ez a cikk bemutatja, hogyan konfigurálhat tűzfalszabályokat az Azure Portal segítségével. Tűzfalszabályok létrehozásával engedélyezheti külső ügyfeleknek vagy alkalmazásoknak az Azure Blockchain Workbench-adatbázisához való csatlakozást.

## <a name="connect-to-the-blockchain-workbench-database"></a>Csatlakozás a Blockchain Workbench adatbázisához

A következőképpen csatlakozhat ahhoz az adatbázishoz, amelyben szabályt szeretne konfigurálni:

1. Jelentkezzen be a Azure Portalba egy olyan fiókkal, amely rendelkezik **tulajdonosi**     engedélyekkel az Azure Blockchain Workbench erőforrásaihoz.
2. A bal oldali navigációs panelen válassza az **Erőforráscsoportok** lehetőséget.
3. Válassza a Blockchain Workbench-környezete erőforráscsoportjának nevét.
4. Válassza a **Típus** lehetőséget az erőforrások rendezéséhez, majd válassza az **SQL server** lehetőséget.
5. Az alábbi képernyőkép erőforráslistájában két adatbázis látható: *master* és *lsgn-sdk*. A tűzfalszabályt az *lsgn-sdk* adatbázishoz konfiguráljuk.

![Blockchain Workbench-erőforrások listázása](./media/database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Tűzfalszabály létrehozása az adatbázishoz

Tűzfalszabály létrehozásához:

1. Válassza az „lsgn-sdk” adatbázis hivatkozását.
2. Válassza a menüsáv **Kiszolgálói tűzfal beállítása** lehetőségét.

   ![Kiszolgálói tűzfal beállítása](./media/database-firewall/configure-server-firewall.png)

3. A szervezetében érvényes szabály létrehozásához:

   * Adjon meg egy nevet a **SZABÁLY NEVE** mezőben.
   * Adja meg a címtartomány kezdőcímét a **KEZDŐ IP-CÍM** mezőben.
   * Adja meg a címtartomány zárócímét a **ZÁRÓ IP-CÍM** mezőben.

   ![Tűzfalszabály létrehozása](./media/database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Ha csak a számítógépe IP-címét szeretné hozzáadni, válassza az **+ Ügyfél IP-címének hozzáadása** lehetőséget.
        
1. A tűzfal-konfiguráció mentéséhez válassza a **Mentés** lehetőséget.
2. Tesztelje az adatbázishoz konfigurált IP-címtartományt úgy, hogy csatlakozik egy alkalmazásból vagy eszközből. Ehhez használhatja például az SQL Server Management Studiót.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatbázisnézetek az Azure Blockchain Workbenchben](database-views.md)