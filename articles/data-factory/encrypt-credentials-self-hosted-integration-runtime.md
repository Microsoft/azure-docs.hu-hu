---
title: Hitelesítő adatok titkosítása Azure Data Factory
description: Megtudhatja, hogyan titkosíthatja és tárolhatja a helyszíni adattárak hitelesítő adatait egy saját üzemeltetésű integrációs modult futtató gépen.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: cd775c5a3bf367600a4537a9409a9bb8f902f588
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82628980"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Hitelesítő adatok titkosítása a helyszíni adattárakhoz Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A saját üzemeltetésű integrációs modult futtató gépen titkosíthatja és tárolhatja a helyszíni adattárak hitelesítő adatait (a társított szolgáltatások bizalmas információkkal). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Egy JSON-definíciós fájlt továbbít a hitelesítő adatokkal a <br/>[**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) parancsmag egy kimeneti JSON-definíciós fájl előállításához a titkosított hitelesítő adatokkal. Ezután használja a frissített JSON-definíciót a társított szolgáltatások létrehozásához.

## <a name="author-sql-server-linked-service"></a>SQL Server társított szolgáltatás szerzője
Hozzon létre egy **SqlServerLinkedService.js** nevű JSON-fájlt bármelyik mappában a következő tartalommal:  

A fájl mentése előtt cserélje le a,, `<servername>` `<databasename>` `<username>` , és `<password>` értékeit a SQL Serverra. És cserélje le `<integration runtime name>` az értékét az Integration Runtime nevére. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Hitelesítő adatok titkosítása
A helyi saját üzemeltetésű integrációs modulban található JSON-adattartalomból származó bizalmas adatok titkosításához futtassa a **New-AzDataFactoryV2LinkedServiceEncryptedCredential**parancsot, és továbbítsa a JSON-adattartalmat. Ez a parancsmag biztosítja, hogy a hitelesítő adatok a DPAPI használatával legyenek titkosítva, és a saját üzemeltetésű Integration Runtime csomóponton helyileg tárolódnak. A hitelesítő adatok titkosított hivatkozását tartalmazó kimeneti adattartalom átirányítható egy másik JSON-fájlba (ebben az esetben a "encryptedLinkedService.json").

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>A JSON használata titkosított hitelesítő adatokkal
Most a **SqlServerLinkedService**beállításához használja az előző parancs kimeneti JSON-fájlját, amely a titkosított hitelesítő adatokat tartalmazza.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>További lépések
További információ az adatáthelyezéssel kapcsolatos biztonsági megfontolásokról: [adatátviteli biztonsági megfontolások](data-movement-security-considerations.md).

