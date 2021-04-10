---
title: Hitelesítő adatok tárolása az Azure Key Vaultban
description: Megtudhatja, hogyan tárolhatja az Azure Key vaultban használt adattárak hitelesítő adatait, amelyek Azure Data Factory automatikusan lekérhető futásidőben.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: 00c28a9ede01a74f4ae64109d277276050047461
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653764"
---
# <a name="store-credential-in-azure-key-vault"></a>Hitelesítő adatok tárolása Azure Key Vaultban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az adattárakhoz és a számítási feladatokhoz tartozó hitelesítő adatokat egy [Azure Key Vault](../key-vault/general/overview.md)tárolhatja. Azure Data Factory beolvassa a hitelesítő adatokat, amikor egy adattárat/számítást használó tevékenységet hajt végre.

Jelenleg az egyéni tevékenység kivételével az összes tevékenységtípus támogatja ezt a funkciót. Az összekötő konfigurálásához a részletekért olvassa el az [egyes összekötők témakör](copy-activity-overview.md#supported-data-stores-and-formats) "társított szolgáltatás tulajdonságai" szakaszát.

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció az adatok gyári felügyelt identitására támaszkodik. Ismerje meg, hogyan működik a [felügyelt identitás a refactoryban](data-factory-service-identity.md) , és győződjön meg arról, hogy az adatok előállítója társítva van.

## <a name="steps"></a>Lépések

A Azure Key Vaultban tárolt hitelesítő adatokra való hivatkozáshoz a következőket kell tennie:

1. Az **adatok gyári felügyelt identitásának beolvasása** a gyári környezettel együtt generált "felügyelt identitási objektum azonosító" értékének másolásával. Ha az ADF authoring felhasználói felületét használja, akkor a felügyelt identitás objektum azonosítója megjelenik a Azure Key Vault társított szolgáltatás létrehozása ablakban; a Azure Portalból is lekérheti, ha [beolvassa az adatok gyári felügyelt identitását](data-factory-service-identity.md#retrieve-managed-identity).
2. **Adja meg a felügyelt identitás hozzáférését a Azure Key Vaulthoz.** A Key vaultban – > hozzáférési szabályzatok – > hozzáférési házirend hozzáadása lehetőségre, keresse meg ezt a felügyelt identitást a **Get** engedély megadásához a titkos engedélyek legördülő menüben. Lehetővé teszi a kijelölt gyár számára a titkos kulcs elérését a Key vaultban.
3. **Hozzon létre egy társított szolgáltatást, amely a Azure Key Vaultre mutat.** Tekintse meg [Azure Key Vault társított szolgáltatást](#azure-key-vault-linked-service).
4. **Hozzon létre egy adattárhoz társított szolgáltatást, amely a Key vaultban tárolt megfelelő titkos kulcsra hivatkozik.** Tekintse meg a [Key vaultban tárolt hivatkozási titkot](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Azure Key Vaulthoz társított szolgáltatás

Azure Key Vault társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **AzureKeyVault**. | Yes |
| baseUrl | A Azure Key Vault URL-cím megadása. | Yes |

**Szerzői felhasználói felület használata:**

Válassza a **kapcsolatok**  ->  **társított szolgáltatások**  ->  **új** lehetőséget. Az új társított szolgáltatásban keresse meg és válassza ki a "Azure Key Vault" elemet:

![Keresés Azure Key Vault](media/store-credentials-in-key-vault/search-akv.png)

Válassza ki a kiépített Azure Key Vault, ahol a hitelesítő adatait tárolja. A **kapcsolódási teszttel** ellenőrizheti, hogy a AKV-kapcsolatok érvényesek-e. 

![Az Azure Key Vault konfigurálása](media/store-credentials-in-key-vault/configure-akv.png)

**JSON-példa:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>Hivatkozás a Key Vaultban tárolt titkos kulcsra

A következő tulajdonságok támogatottak, ha olyan mezőt konfigurál a társított szolgáltatásban, amely a Key Vault-titokra hivatkozik:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A mező Type (típus) tulajdonságát a következőre kell beállítani: **AzureKeyVaultSecret**. | Yes |
| secretName | A titok neve Azure Key Vaultban. | Yes |
| Titkoskulcsverziója | A titok verziója Azure Key Vaultban.<br/>Ha nincs megadva, mindig a titkos kulcs legújabb verzióját használja.<br/>Ha meg van adva, a rendszer az adott verzióra ragaszkodik.| No |
| áruház | Egy Azure Key Vault társított szolgáltatásra hivatkozik, amelyet a hitelesítő adatok tárolására használ. | Yes |

**Szerzői felhasználói felület használata:**

Válassza a titkos mezők **Azure Key Vault** lehetőséget, miközben létrehozza a kapcsolódást az adattárhoz vagy a számítási feladatokhoz. Válassza ki a kiépített Azure Key Vault társított szolgáltatást, és adja meg a **titkos nevet**. Igény szerint megadhat egy titkos verziót is. 

>[!TIP]
>A társított szolgáltatás kapcsolati karakterláncát (például SQL Server, blob Storage stb.) használó összekötők esetében választhat, hogy csak a titkos mezőt kívánja-e tárolni (például jelszó a AKV), vagy a teljes kapcsolati karakterláncot a AKV tárolja. Mindkét lehetőséget megtalálhatja a felhasználói felületen is.

![Azure Key Vault titok konfigurálása](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON-példa: (lásd a "jelszó" szakaszt)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
