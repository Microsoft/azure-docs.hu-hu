---
title: Azure Key Vault-beli titkos kulcsok használata a folyamattevékenységekben
description: Ismerje meg, hogyan hívhat le tárolt hitelesítő adatokat az Azure Key vaultból, és hogyan használhatja őket a adat-előállító folyamatának futtatása közben.
author: ChrisLound
ms.author: chlound
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: d7e1133b8f3880c4c1616ef5ca955ed014348935
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383959"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Azure Key Vault-beli titkos kulcsok használata a folyamattevékenységekben

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A hitelesítő adatokat vagy a titkos értékeket egy Azure Key Vault tárolhatja, és a folyamat végrehajtása során felhasználhatja a tevékenységeket.

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció az adatok gyári felügyelt identitására támaszkodik.  Ismerje meg, hogyan működik az [Data Factory felügyelt identitása](./data-factory-service-identity.md) , és győződjön meg arról, hogy az adatok előállítója társítva van.

## <a name="steps"></a>Lépések

1. Nyissa meg az adatok előállítójának tulajdonságait, és másolja a felügyelt Identity Application ID értéket.

    ![Felügyelt identitás értéke](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Nyissa meg a Key Vault hozzáférési házirendjét, és adja hozzá a felügyelt identitás engedélyeit a titkos kulcsok lekéréséhez és listázásához.

    ![Képernyőfelvétel: a "hozzáférési szabályzatok" oldal megjelenítése a "hozzáférési házirend hozzáadása" művelet kiemelésével.](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Key Vault hozzáférési szabályzatok](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Kattintson a **Hozzáadás**, majd a **Mentés** gombra.

3. Navigáljon a Key Vault titkára, és másolja a titkos azonosítót.

    ![Titkos azonosító](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Jegyezze fel a titkos URI-t, amelyet az adatfeldolgozó-folyamat futtatásakor szeretne kapni.

4. A Data Factory folyamatában adjon hozzá egy új webes tevékenységet, és konfigurálja az alábbiak szerint.  

    |Tulajdonság  |Érték  |
    |---------|---------|
    |Biztonságos kimenet     |Igaz         |
    |URL-cím     |[A titkos URI-érték]? API-Version = 7.0         |
    |Metódus     |GET         |
    |Hitelesítés     |MSI         |
    |Erőforrás        |https://vault.azure.net       |

    ![Webes tevékenység](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Hozzá kell adnia a **? API-Version = 7.0** értéket a titkos URI végéhez.  

    > [!CAUTION]
    > Állítsa a Secure output (biztonságos kimenet) beállítást igaz értékre, hogy megakadályozza a titkos érték egyszerű szövegként való naplózását.  Az értéket használó további tevékenységeknek a biztonságos bemenet beállítását True értékre kell állítani.

5. Ha egy másik tevékenységben szeretné használni az értéket, használja a következő kódú kifejezést **@activity ("Web1"). output. Value**.

    ![Kód kifejezése](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Következő lépések

Az adattárak és a számítási adatok hitelesítő adatainak tárolásához Azure Key Vault használatáról további információt a [hitelesítő adatok tárolása a Azure Key Vault-ben](./store-credentials-in-key-vault.md) című témakörben talál.