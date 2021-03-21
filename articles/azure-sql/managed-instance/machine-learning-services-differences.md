---
title: Machine Learning Services főbb különbségek
description: Ez a cikk az Azure SQL felügyelt példányain és a Machine Learning Services SQL Server Machine Learning Services közötti fő különbségeket ismerteti.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: b5ad439a8e10fa9aa44e477ca35f45d65ae40803
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599544"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>A felügyelt Azure SQL-példányon és az SQL Serveren elérhető Machine Learning Services közötti fő eltérések

Ez a cikk a [felügyelt Azure SQL-példányok és a](machine-learning-services-overview.md) [SQL Server-Machine learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)Machine learning Services közötti főbb különbségeket ismerteti.

## <a name="language-support"></a>Nyelvi támogatás

Machine Learning Services a felügyelt SQL-példányon, és SQL Server támogatja a Python és az R [bővíthetőségi keretrendszert](/sql/machine-learning/concepts/extensibility-framework). Az SQL felügyelt példányain a legfontosabb különbségek a következők:

- Csak a Python és az R nyelv támogatott. Külső nyelvek (például Java) nem adhatók hozzá.

- A Python és az R kezdeti verziói eltérőek:

  | Platform                   | Python futtatókörnyezet verziója           | R Runtime-verziók                   |
  |----------------------------|----------------------------------|--------------------------------------|
  | Felügyelt Azure SQL-példány | 3.7.2                            | 3.5.2                                |
  | SQL Server 2019            | 3.7.1                            | 3.5.2                                |
  | SQL Server 2017            | 3.5.2 és 3.7.2 (CU22 és újabb verziók) | 3.3.3 és 3.5.2 (CU22 és újabb verziók)     |
  | SQL Server 2016            | Nem elérhető                    | 3.2.2 és 3.5.2 (SP2 CU14 és újabb verziók) |

## <a name="python-and-r-packages"></a>Python-és R-csomagok

Nem támogatott az SQL felügyelt példánya olyan csomagok esetében, amelyek külső futtatókörnyezettől (például Java) függenek, vagy a telepítéshez vagy használathoz szükségesek az operációs rendszer API-jai.

A Python és az R csomagok kezelésével kapcsolatos további információkért lásd:

- [Python-csomagadatok lekérése](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [R-csomagadatok lekérése](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Erőforrások szabályozása

A felügyelt SQL-példányok esetében nem lehetséges az R-erőforrások korlátozása [Resource Governoron](/sql/relational-databases/resource-governor/resource-governor?view=azuresqldb-mi-current&preserve-view=true)keresztül, és a külső erőforrások készletei nem támogatottak.

Alapértelmezés szerint az R-erőforrások az elérhető SQL felügyelt példány-erőforrások legfeljebb 20%-ában vannak beállítva, ha a bővíthetőség engedélyezve van. Az alapértelmezett százalék módosításához hozzon létre egy Azure-támogatási jegyet a következő címen: [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) .

A bővíthetőség engedélyezve van a következő SQL-parancsokkal (az SQL felügyelt példánya újraindul, és néhány másodpercig elérhetetlenné válik):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Ha le szeretné tiltani a memória-és CPU-erőforrások kibővítését és 100%-át a SQL Serverre, használja a következő parancsokat:

```sql
sp_configure 'external scripts enabled', 0;
RECONFIGURE WITH OVERRIDE;
```

Az SQL felügyelt példányai számára elérhető összes erőforrás attól függ, hogy melyik szolgáltatási szintet választja ki. További információ: [Azure SQL Database vásárlási modellek](/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Nincs elég memória-hiba

A memóriahasználat az R-szkriptekben használt memória mennyiségétől és a párhuzamosan végrehajtott lekérdezések számától függ. Ha nincs elegendő szabad memória az R-hez, hibaüzenet jelenik meg. Gyakori hibaüzenetek:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Ha ezen hibák valamelyike megjelenik, akkor azt megoldhatja, ha az adatbázist magasabb szolgáltatási rétegre szeretné méretezni.

## <a name="sql-managed-instance-pools"></a>SQL felügyelt példányok készletei

A Machine Learning Services jelenleg nem támogatott az [Azure SQL felügyelt példány-készleteken (előzetes verzió)](instance-pools-overview.md).

## <a name="next-steps"></a>Következő lépések

- Tekintse meg az [Azure SQL felügyelt példányának](machine-learning-services-overview.md)áttekintését Machine learning Services.
- Ha szeretné megtudni, hogyan használható a Python a Machine Learning Servicesban, tekintse meg a [Python-parancsfájlok futtatása](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)című témakört.
- Az R-Machine Learning Services használatának megismeréséhez tekintse meg az [r-parancsfájlok futtatása](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)című témakört.
