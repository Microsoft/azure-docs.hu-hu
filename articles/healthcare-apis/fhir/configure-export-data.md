---
title: Az exportálási beállítások konfigurálása a FHIR készült Azure API-ban
description: Ez a cikk azt ismerteti, hogyan konfigurálható az exportálási beállítások a FHIR készült Azure API-ban
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: matjazl
ms.openlocfilehash: ee110420c697afb6ecad857ba823c61d03c6be6c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046991"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Az exportálási beállítás konfigurálása és a Storage-fiók beállítása

A FHIR készült Azure API támogatja a $export parancsot, amely lehetővé teszi az Azure API-ból FHIR-fiókból származó adatexportálást egy Storage-fiókba.

A FHIR Azure API-ban való exportálásának beállítása három lépésből áll:

1. Felügyelt identitás engedélyezése a FHIR Service-hez készült Azure API-ban.
2. Hozzon létre egy Azure Storage-fiókot (ha még nem tette meg), és rendeljen engedélyt az Azure API-hoz a FHIR való hozzáféréshez.
3. A Storage-fiók kiválasztása az Azure API-ban a FHIR exportálási fiókként.

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Felügyelt identitás engedélyezése a FHIR készült Azure API-ban

Az Azure API FHIR-hez való konfigurálásának első lépése az, hogy a rendszer széles körben felügyelt identitást engedélyezzen a szolgáltatásban. További információ az Azure-beli felügyelt identitásokról: az [Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/overview.md).

Ehhez nyissa meg az Azure API for FHIR szolgáltatást, és válassza az **Identity (identitás**) lehetőséget. Ha a **be** állapotra vált, a felügyelt identitást engedélyez az Azure API-ban a FHIR szolgáltatásban.

![Felügyelt identitás engedélyezése](media/export-data/fhir-mi-enabled.png)

Most átléphet a következő lépésre egy Storage-fiók létrehozásával és a szolgáltatáshoz való hozzárendelésével.

## <a name="adding-permission-to-storage-account"></a>Engedély hozzáadása a Storage-fiókhoz

Az Exportálás következő lépése a FHIR szolgáltatáshoz tartozó Azure API-hoz való írási engedély megadása a Storage-fiókba való íráshoz.

Miután létrehozta a Storage-fiókot, lépjen a **Access Control (iam)** elemre a Storage-fiókban, és válassza a **szerepkör-hozzárendelés hozzáadása** elemet.

![Szerepkör-hozzárendelés exportálása](media/export-data/fhir-export-role-assignment.png)

Itt adja hozzá a szerepkör- **tároló blob-Adatközreműködőjét** a szolgáltatás neveként, majd válassza a **Mentés** lehetőséget.

![Szerepkör hozzáadása](media/export-data/fhir-export-role-add.png)

Most már készen áll arra, hogy kiválassza a Storage-fiókot az Azure API-ban a FHIR alapértelmezett tárolási fiókként $export számára.

## <a name="selecting-the-storage-account-for-export"></a>$export Storage-fiók kiválasztása

Utolsó lépésként hozzá kell rendelni az Azure Storage-fiókot, amelyet az Azure API a FHIR-hoz az adatexportáláshoz fog használni. Ehhez nyissa meg az **integrációt** az Azure API FHIR szolgáltatásban, és válassza ki a Storage-fiókot.

![FHIR-exportálási tároló](media/export-data/fhir-export-storage.png)

Az utolsó lépés elvégzése után már készen áll az adatexportálásra $export parancs használatával.

> [!Note]
> Csak a FHIR-hez készült Azure API-hoz tartozó Storage-fiókok regisztrálhatók a $export műveletek célhelye.

További információ az adatbázis-beállítások konfigurálásáról, a hozzáférés-vezérlésről, a diagnosztikai naplózás engedélyezéséről és az egyéni fejlécek használatával az adatok naplókhoz való hozzáadásáról:

>[!div class="nextstepaction"]
>[További beállítások](azure-api-for-fhir-additional-settings.md)
