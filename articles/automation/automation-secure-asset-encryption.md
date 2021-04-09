---
title: Biztonságos eszközök titkosítása az Azure Automationben
description: Ez a cikk fogalmakat tartalmaz az Automation-fiók titkosítás használatára való konfigurálásához.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: b46cf3a742158a3347b43a1e9bc6d62d0b2160d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773691"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>Biztonságos eszközök titkosítása az Azure Automationben

Azure Automation a biztonságos adategységek közé tartoznak a hitelesítő adatok, a tanúsítványok, a kapcsolatok és a titkosított változók. Ezeket az eszközöket a Azure Automation több titkosítási szinttel védik. A titkosításhoz használt legfelső szintű kulcs alapján két titkosítási modell létezik:

- Microsoft által felügyelt kulcsok használata
- A felügyelt kulcsok használata

## <a name="microsoft-managed-keys"></a>Microsoft által felügyelt kulcsok

Alapértelmezés szerint a Azure Automation-fiók a Microsoft által felügyelt kulcsokat használja.

Minden biztonságos eszköz titkosítva van, és a Azure Automation tárolja az egyes Automation-fiókokhoz generált egyedi kulcs (adattitkosítási kulcs) használatával. Ezeket a kulcsokat a rendszer titkosítja és tárolja Azure Automation a fiók titkosítási kulcsa (AEK) nevű fiókhoz létrehozott egy másik egyedi kulccsal. Ezek a fiók titkosítási kulcsainak titkosítása és tárolása Azure Automation a Microsoft által felügyelt kulcsok használatával történik. 

## <a name="keys-that-you-manage-with-key-vault-preview"></a>Key Vaultsal felügyelt kulcsok (előzetes verzió)

Az Automation-fiókhoz tartozó biztonságos eszközök titkosítását a saját kulcsaival kezelheti. Amikor az Automation-fiók szintjén megad egy ügyfél által felügyelt kulcsot, a kulcs az Automation-fiókhoz tartozó fiók titkosítási kulcsának megvédésére és hozzáférés-vezérlésére szolgál. Ez a funkció az összes biztonságos eszköz titkosítására és visszafejtésére szolgál. Az ügyfél által felügyelt kulcsok nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. A biztonságos eszközök védelméhez használt titkosítási kulcsokat is naplózhatja.

Az ügyfél által felügyelt kulcsok tárolásához használja a Azure Key Vault. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához.  További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../key-vault/general/overview.md)

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>Az ügyfél által felügyelt kulcsok használata Automation-fiókhoz

Ha az Automation-fiókhoz az ügyfél által felügyelt kulcsokkal végez titkosítást, a Azure Automation a társított kulcstartóban lévő ügyfél által felügyelt kulccsal együtt becsomagolja a fiók titkosítási kulcsát. Az ügyfél által felügyelt kulcsok engedélyezése nem befolyásolja a teljesítményt, és a fiók azonnal, késedelem nélkül titkosítva lesz az új kulccsal.

Az új Automation-fiók mindig a Microsoft által felügyelt kulcsokkal van titkosítva. A fiók létrehozásakor nem lehet engedélyezni az ügyfél által felügyelt kulcsokat. Az ügyfél által felügyelt kulcsok Azure Key Vault tárolódnak, és a kulcstárolót olyan hozzáférési házirendekkel kell kiépíteni, amelyek kulcsfontosságú engedélyeket biztosítanak az Automation-fiókhoz társított felügyelt identitásnak. A felügyelt identitás csak a Storage-fiók létrehozása után érhető el.

Ha módosítja a Azure Automation biztonságos adattitkosításhoz használt kulcsot, az ügyfél által felügyelt kulcsok engedélyezésével vagy letiltásával, a kulcs verziójának frissítésével vagy egy másik kulcs megadásával, a fiók titkosítási kulcsának titkosítása megváltozik, de a Azure Automation fiókjában lévő biztonságos eszközök nem szükségesek újra titkosítva.

> [!NOTE] 
> Az ügyfél által felügyelt kulcsok engedélyezéséhez Azure Automation REST API hívásokat kell tennie az 2020-01-13-es API-verzióval – előzetes verzió

### <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>Az ügyfél által felügyelt kulcsok Azure Automation való használatának előfeltételei

Az ügyfél által felügyelt kulcsok Automation-fiókhoz való engedélyezése előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

 - Az ügyfél-összefoglalt kulcsot egy Azure Key Vault tárolja. 
 - Engedélyezze mind a helyreállítható **törlést** , sem a kulcstárolóban lévő tulajdonságok **eltávolítását** . Ezek a funkciók a kulcsok helyreállításának engedélyezéséhez szükségesek a véletlen törlés esetén.
 - Azure Automation titkosításhoz csak RSA-kulcsok támogatottak. A kulcsokkal kapcsolatos további információkért tekintse meg [a Azure Key Vault kulcsok, titkos kódok és tanúsítványok](../key-vault/general/about-keys-secrets-certificates.md)című témakört.
- Az Automation-fiók és a kulcstartó különböző előfizetésekben lehet, de ugyanabban a Azure Active Directory bérlőben kell lennie.

### <a name="assignment-of-an-identity-to-the-automation-account"></a>Identitás hozzárendelése az Automation-fiókhoz

Ha az ügyfél által felügyelt kulcsokat Automation-fiókkal szeretné használni, az Automation-fióknak hitelesítenie kell az ügyfél által felügyelt kulcsokat tároló kulcstartón. A Azure Automation rendszerhez rendelt felügyelt identitásokat használ a fiók hitelesítéséhez Azure Key Vault használatával. A felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md)

A következő REST API hívásával konfigurálja a rendszerhez rendelt felügyelt identitást az Automation-fiókhoz:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Kérés törzse:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

Az Automation-fiók rendszerhez rendelt identitását a következőhöz hasonló válasz adja vissza:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configuration-of-the-key-vault-access-policy"></a>A Key Vault hozzáférési szabályzat konfigurálása

Ha egy felügyelt identitás hozzá van rendelve az Automation-fiókhoz, az ügyfél által felügyelt kulcsokat tároló kulcstartó elérését konfigurálja. Azure Automation az ügyfél által felügyelt kulcsokra vonatkozó **Get**, **Recover**, **wrapKey** és **UnwrapKey** szükséges.

Az ilyen hozzáférési szabályzatok a következő REST API hívásával állíthatók be:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Kérés törzse:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> A **tenantId** és a **objectId** mezőket az **Identity. tenantId** és **Identity. principalId** értékekkel kell megadni az Automation-fiók felügyelt identitásának válasza alapján.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Az Automation-fiók konfigurációjának módosítása az ügyfél által felügyelt kulcs használatára

Végezetül átválthatja az Automation-fiókját a Microsoft által felügyelt kulcsokból az ügyfél által felügyelt kulcsokra az alábbi REST API hívás használatával:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Kérés törzse:

```json
{
    "identity": {
    "type": "SystemAssigned"
    },
    "properties": {
        "encryption": {
            "keySource": "Microsoft.Keyvault",
            "keyvaultProperties": {
                "keyName": "sample-vault-key",
                "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
                "keyVersion": "7c73556c521340209371eaf623cc099d"
            }
        }
    }
}
```

Mintaválasz

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="rotation-of-a-customer-managed-key"></a>Ügyfél által felügyelt kulcs elforgatása

A megfelelőségi szabályzatok alapján Azure Key Vault elforgathatja az ügyfél által felügyelt kulcsot. A kulcs elforgatásakor frissítenie kell az Automation-fiókot az új kulcs URI-azonosítójának használatára.

A kulcs elforgatása nem aktiválja a biztonságos eszközök újratitkosítását az Automation-fiókban. Nincs szükség további műveletre.

## <a name="revocation-of-access-to-a-customer-managed-key"></a>Ügyfél által felügyelt kulcshoz való hozzáférés visszavonása

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a PowerShellt vagy az Azure CLI-t. További információ: [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) vagy [Azure Key Vault parancssori](/cli/azure/keyvault)felület. A hozzáférés visszavonása hatékonyan blokkolja az Automation-fiókban található összes biztonságos objektum elérését, mivel a titkosítási kulcs nem érhető el Azure Automation.

## <a name="next-steps"></a>Következő lépések

- A Azure Key Vault megismeréséhez tekintse meg a [Mi az Azure Key Vault?](../key-vault/general/overview.md)című témakört.
- A tanúsítványok kezeléséhez lásd: [tanúsítványok kezelése Azure Automationban](shared-resources/certificates.md).
- A hitelesítő adatok kezeléséhez lásd: [a hitelesítő adatok kezelése Azure Automationban](shared-resources/credentials.md).
- Az Automation-változók használatához [Azure Automation változók kezelése](shared-resources/variables.md).
- A kapcsolatok kezelésével kapcsolatos segítségért lásd: [kapcsolatok kezelése Azure Automationban](automation-connections.md).
