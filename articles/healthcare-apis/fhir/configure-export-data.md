---
title: Az exportálási beállítások konfigurálása a FHIR készült Azure API-ban
description: Ez a cikk azt ismerteti, hogyan konfigurálható az exportálási beállítások a FHIR készült Azure API-ban
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: bb728b81d4dad5d880c27d1ebe8f85f1508f8231
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018698"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Az exportálási beállítás konfigurálása és a Storage-fiók beállítása

A FHIR készült Azure API támogatja a $export parancsot, amely lehetővé teszi az Azure API-ból FHIR-fiókból származó adatexportálást egy Storage-fiókba.

A FHIR Azure API-ban való exportálásának beállítása három lépésből áll:

1. Felügyelt identitás engedélyezése a FHIR Service-hez készült Azure API-ban
2. Azure Storage-fiók létrehozása (ha még nem történt meg) és engedélyek kiosztása az Azure API-hoz FHIR a Storage-fiókhoz
3. A Storage-fiók kiválasztása az Azure API-ban a FHIR exportálási fiókként

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Felügyelt identitás engedélyezése a FHIR készült Azure API-ban

Az Azure API FHIR-hez való konfigurálásának első lépése az, hogy a rendszer széles körben felügyelt identitást engedélyezzen a szolgáltatásban. Az Azure-ban felügyelt identitásokról [itt](../../active-directory/managed-identities-azure-resources/overview.md)olvashat bővebben.

Ehhez navigáljon az Azure API FHIR szolgáltatáshoz, és válassza az Identity (identitás) panelt. Ha a be állapotra vált, a felügyelt identitást engedélyez az Azure API-ban a FHIR szolgáltatásban.

![Felügyelt identitás engedélyezése](media/export-data/fhir-mi-enabled.png)

Most térjünk át a következő lépésre, és hozzon létre egy Storage-fiókot, és rendeljen hozzá egy engedélyt a szolgáltatáshoz.

## <a name="adding-permission-to-storage-account"></a>Engedély hozzáadása a Storage-fiókhoz

Az Exportálás következő lépése a FHIR szolgáltatáshoz tartozó Azure API-nak a Storage-fiókba való írásához szükséges engedélyek kiosztása.

A Storage-fiók létrehozása után navigáljon a Storage-fiók Access Control (IAM) paneljére, és válassza a szerepkör-hozzárendelések hozzáadása elemet.

![Szerepkör-hozzárendelés exportálása](media/export-data/fhir-export-role-assignment.png)

Itt hozzáadjuk a szerepkör-tároló blob-adatközreműködőjét a szolgáltatás nevéhez.

![Szerepkör hozzáadása](media/export-data/fhir-export-role-add.png)

Most már készen áll a következő lépésre, ahol kiválaszthatja a Storage-fiókot az Azure API-ban a FHIR alapértelmezett Storage-fiókként a $export számára.

## <a name="selecting-the-storage-account-for-export"></a>$export Storage-fiók kiválasztása

Utolsó lépésként hozzá kell rendelnie az Azure Storage-fiókot, amelyet az Azure API FHIR használ az adatexportáláshoz. Ehhez navigáljon az integráció panelre az Azure API-ban a FHIR szolgáltatáshoz Azure Portal és válassza ki a Storage-fiókot.

![FHIR-exportálási tároló](media/export-data/fhir-export-storage.png)

Ezután készen áll az adatexportálásra $export parancs használatával.

>[!div class="nextstepaction"]
>[További beállítások](azure-api-for-fhir-additional-settings.md)
