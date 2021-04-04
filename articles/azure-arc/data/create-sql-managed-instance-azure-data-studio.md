---
title: Felügyelt Azure SQL-példány létrehozása Azure Data Studio használatával
description: Felügyelt Azure SQL-példány létrehozása Azure Data Studio használatával
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 651811384a8e831c41c48ca8c4849e5ee3852054
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280471"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>SQL felügyelt példány létrehozása – Azure arc Azure Data Studio használatával

Ez a dokumentum végigvezeti az Azure SQL felügyelt példányának telepítésének lépésein – Azure arc Azure Data Studio használatával

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>Jelentkezzen be az Azure arc-adatkezelőbe

Példány létrehozása előtt jelentkezzen be az Azure arc-adatkezelőbe, ha még nincs bejelentkezve.

```console
azdata login
```

Ekkor a rendszer kérni fogja az adatvezérlőt létrehozó névtér, a Felhasználónév és a jelszó megadását a vezérlőbe való bejelentkezéshez.  

> Ha ellenőriznie kell a névteret, a futtatásával ```kubectl get pods -A``` lekérheti a fürtben található összes névtér listáját.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Azure SQL felügyelt példány létrehozása az Azure arc-ban

- Azure Data Studio elindítása
- A kapcsolatok lapon kattintson a bal felső sarokban található három pontra, és válassza az "új központi telepítés" lehetőséget.
- A központi telepítési lehetőségek közül válassza az **Azure SQL felügyelt példány – Azure arc** lehetőséget. 
  > [!NOTE]
  > Előfordulhat, hogy a rendszer arra kéri, hogy telepítse az [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] itt, ha jelenleg nincs telepítve.
- Fogadja el az adatvédelmi és a licencfeltételeket, majd kattintson a lenti **kijelölés** elemre.



- Az Azure SQL felügyelt példányának üzembe helyezése – Azure arc panelen adja meg a következő adatokat:
  - Adja meg a SQL Server példány nevét
  - Adja meg és erősítse meg a SQL Server példány jelszavát.
  - Válassza ki az adattárolási osztályt a megfelelő értékként
  - A naplókhoz megfelelő tárolási osztály kiválasztása

- Kattintson a **telepítés** gombra

- Ennek el kell indítania az Azure SQL felügyelt példányának létrehozását – az Azure-ívet az adatkezelőn.

- Néhány percen belül sikeresen befejeződött a létrehozás.

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Kapcsolódás az Azure SQL felügyelt példányához – Azure arc Azure Data Studio

- Jelentkezzen be az Azure arc-adatkezelőbe az adatkezelő névterének, felhasználónevének és jelszavának megadásával: 
```console
azdata login
```

- Az összes felügyelt Azure SQL-példány megtekintése az alábbi parancsok használatával:

```console
azdata arc sql mi list
```

A kimenetnek a következőképpen kell kinéznie: másolja a ServerEndpoint (beleértve a portszámot is) innen.

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- Azure Data Studio a **kapcsolatok** lapon kattintson az **új kapcsolatra** a **kiszolgálók** nézetben.
- A **kapcsolatok** panelen illessze be a ServerEndpoint a kiszolgáló szövegmezőbe.
- Adja meg az **SQL-bejelentkezést** hitelesítési típusként
- Adja meg az *sa* nevet felhasználónévként
- Adja meg a fiók jelszavát `sa`
- Szükség esetén megadhatja az adott adatbázis nevét, amelyhez csatlakozni szeretne
- Ha szükséges, válassza ki/adja hozzá az új kiszolgálócsoport lehetőséget
- Válassza a **Kapcsolódás** lehetőséget az Azure SQL felügyelt példányhoz való kapcsolódáshoz – Azure arc




## <a name="next-steps"></a>Következő lépések

Most próbálkozzon az [SQL-példány figyelésével](monitor-grafana-kibana.md)
