---
title: Felhasználók hozzáadása egy Azure Data Lake Analytics-fiókhoz
description: Megtudhatja, hogyan adhat hozzá felhasználókat a Data Lake Analytics-fiókhoz a felhasználó hozzáadása varázsló és a Azure PowerShell használatával.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: c04b1bbd62e156aeb8d3a0ebb244cfbc753dec52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020823"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Felhasználó hozzáadása az Azure Portalon

## <a name="start-the-add-user-wizard"></a>A felhasználó hozzáadása varázsló elindítása
1. Nyissa meg a Azure Data Lake Analyticst a használatával https://portal.azure.com .
2. Kattintson a **felhasználó hozzáadása varázsló** elemre.
3. A **felhasználó kiválasztása** lépésben keresse meg azt a felhasználót, akit hozzá kíván adni. Kattintson a **Kiválasztás** elemre.
4. a **szerepkör kiválasztása lépésben válassza ki** **Data Lake Analytics fejlesztőt**. Ez a szerepkör az U-SQL-feladatok elküldéséhez/figyeléséhez vagy kezeléséhez szükséges engedélyek minimális készletét határozza meg. Rendeljen hozzá ehhez a szerepkörhöz, ha a csoport nem az Azure-szolgáltatások kezelésére szolgál.
5. A **katalógus engedélyeinek kiválasztása** lépésben válassza ki azokat az adatbázisokat, amelyekre a felhasználónak hozzá kell férnie. A feladatok elküldéséhez a "Master" nevű alapértelmezett statikus adatbázishoz olvasási és írási hozzáférés szükséges. Amikor elkészült, kattintson az **OK** gombra.
6. A **kiválasztott engedélyek hozzárendelésének** utolsó lépésében tekintse át a varázsló által végrehajtott módosításokat. Kattintson az **OK** gombra.


## <a name="configure-acls-for-data-folders"></a>Hozzáférés-vezérlési listák konfigurálása adatmappákhoz
Szükség szerint "R-X" vagy "RWX" megadása a bemeneti adatokat és a kimeneti adatokat tartalmazó mappákon.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Szükség esetén vegye fel a felhasználót a Azure Data Lake Storage Gen1 szerepkör- **olvasó** szerepkörbe.
1.  Keresse meg Azure Data Lake Storage Gen1-fiókját.
2.  Kattintson a **Felhasználók** elemre.
3. Kattintson a **Hozzáadás** parancsra.
4.  Válasszon ki egy Azure-szerepkört a csoport hozzárendeléséhez.
5.  Hozzárendelés az olvasó szerepkörhöz. Ez a szerepkör a ADLSGen1-ben tárolt adatkereséshez és kezeléshez szükséges minimális engedélyekkel rendelkezik. Rendeljen hozzá ehhez a szerepkörhöz, ha a csoport nem az Azure-szolgáltatások kezelésére szolgál.
6.  Írja be a csoport nevét.
7.  Kattintson az **OK** gombra.

## <a name="adding-a-user-using-powershell"></a>Felhasználó hozzáadása a PowerShell használatával

1. Kövesse a jelen útmutató utasításait: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/).
2. Töltse le a [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell-szkriptet.
3. Futtassa a PowerShell-szkriptet. 

Az a minta parancs, amellyel felhasználói hozzáférést biztosíthat a feladatok elküldéséhez, megtekintheti az új feladatok metaadatait, és megtekintheti a régi metaadatokat:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Következő lépések

* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [A Data Lake Analytics használatának első lépései a Azure Portal](data-lake-analytics-get-started-portal.md)
* [Azure Data Lake Analytics kezelése Azure PowerShell használatával](data-lake-analytics-manage-use-powershell.md)
