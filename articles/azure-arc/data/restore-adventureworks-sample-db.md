---
title: A AdventureWorks-mintaadatbázis visszaállítása a felügyelt SQL-példányra
description: A AdventureWorks-mintaadatbázis visszaállítása a felügyelt SQL-példányra
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a6b3577fe820c9dadcb3c8415aa7a951a3283284
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641817"
---
# <a name="restore-the-adventureworks-sample-database-into-sql-managed-instance---azure-arc"></a>A AdventureWorks-mintaadatbázis visszaállítása a felügyelt SQL-példányba – Azure arc

A [AdventureWorks](/sql/samples/adventureworks-install-configure) egy OLTP-adatbázist tartalmazó mintaadatbázis, amelyet gyakran használnak oktatóanyagokban és példákban. A Microsoft a [SQL Server Samples GitHub-tárház](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)részeként biztosít és tart fenn.

Ez a dokumentum egy egyszerű folyamatot ismertet, amely lekéri a AdventureWorks-mintaadatbázis visszaállítását az SQL felügyelt példányára – Azure arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>A AdventureWorks biztonságimásolat-fájl letöltése

Töltse le a AdventureWorks Backup (. bak) fájlt az SQL felügyelt példányának tárolójába. Ebben a példában a parancs használatával `kubectl exec` távolról hajtson végre egy parancsot az SQL felügyelt példányának tárolójában, hogy letöltse a. bak fájlt a tárolóba. Töltse le ezt a fájlt bármely elérhető helyről, `wget` Ha rendelkezik más adatbázis-biztonsági másolati fájlokkal, amelyeket le szeretne húzni az SQL felügyelt példányának tárolóján belül. Ha az SQL felügyelt példány tárolóján belül van, könnyen visszaállítható a standard `RESTORE DATABASE` T-SQL használatával.

A Futtatás előtt futtasson egy olyan parancsot, amely a következőhöz hasonló:. bak fájl, amely a pod név és a névtér nevének értékét cseréli le a futtatása előtt.
> [!NOTE]
>  A tárolónak az 443-es internetkapcsolattal kell rendelkeznie a fájl letöltéséhez a GitHubról.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

Példa

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

## <a name="restore-the-adventureworks-database"></a>A AdventureWorks-adatbázis visszaállítása

Hasonlóképpen futtathat egy `kubectl` exec parancsot is az `sqlcmd` SQL felügyelt példány tárolójában található CLI eszköz használatára, hogy a T-SQL-parancsot végrehajtsa az adatbázis visszaállításához.

Az adatbázis visszaállításához futtasson egy ehhez hasonló parancsot. A Futtatás előtt cserélje le a pod neve, a jelszó és a névtér nevét.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
Példa

```console
kubectl exec sqltest1-0 -n arc -- -c arc-sqlmi /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P MyPassword! -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
