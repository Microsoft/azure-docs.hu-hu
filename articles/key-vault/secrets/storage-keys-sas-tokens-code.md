---
title: Közös hozzáférésű jogosultságkód jogkivonatának beolvasása a kódban | Azure Key Vault
description: A felügyelt tárfiók funkció zökkenőmentes integrációt biztosít a Azure Key Vault azure storage-fiók között. Ez a példa a .NET-hez készült Azure SDK-t használja a SAS-jogkivonatok kezeléséhez.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c8a7b357efc2e30d1ca93d0b45fd2563c980045
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752749"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>SAS-definíció létrehozása és közös hozzáférésű jogosultságkód-jogkivonatok beolvasása kódban

A tárfiókot a kulcstartóban tárolt közös hozzáférésű jogosultság jogosultsági (SAS)-jogkivonatokkal kezelheti. További információ: Korlátozott hozzáférés az [Azure Storage-erőforrásokhoz SAS használatával.](../../storage/common/storage-sas-overview.md)

> [!NOTE]
> Javasoljuk, hogy az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../storage/common/storage-auth-aad.md) használatával biztosítsa tárfiókja biztonságát a kiemelkedő biztonság és a könnyű használat érdekében a megosztott kulcsos hitelesítéshez.

Ez a cikk egy SAS-definíciót és SAS-jogkivonatokat lekért .NET-kódmintákat tartalmaz. További részletekért tekintse meg [a ShareLink-mintát,](/samples/azure/azure-sdk-for-net/share-link/) beleértve a létrehozott ügyfelet Key Vault felügyelt tárfiókok esetében. Az SAS-jogkivonatok létrehozásáról és tárolásáról a Tárfiókkulcsok kezelése az Key Vault és az [Azure CLI](overview-storage-keys.md) használatával vagy a Tárfiókkulcsok kezelése a Key Vault és a [Azure PowerShell.](overview-storage-keys-powershell.md)

## <a name="code-samples"></a>Kódminták

A következő példában egy SAS-sablont hozunk létre:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

Ezzel a sablonnal létrehozhatunk egy SAS-definíciót a 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

Az SAS-definíció létrehozása után sas-jogkivonatokat, például titkos adatokat is lekérhet egy `SecretClient` használatával. A titkos nevet a tárfiók nevével, majd egy kötőjellel kell előjelként bejelszódni:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Ha a közös hozzáférésű jogosultság jogosultsági jogkivonata hamarosan lejár, újra lekérheti ugyanazt a titkos kivonatot egy új létrehozásához.

Az Azure Storage-szolgáltatások eléréséhez Key Vault SAS-jogkivonatból lekért [](../../storage/common/storage-account-sas-create-dotnet.md#use-an-account-sas-from-a-client) útmutatóért lásd: Sas-fiók használata az Blob service

> [!NOTE]
> Az alkalmazást fel kell készíteni az SAS frissítéséhez, ha a Storage 403-ast kap, hogy kezelni tudja azt az esetet, amikor egy kulcsot feltörtek, és a szokásos rotációs időszaknál gyorsabban kell elforgatni őket. 

## <a name="next-steps"></a>Következő lépések
- Megtudhatja, hogyan [adhat korlátozott hozzáférést az Azure Storage-erőforrásokhoz SAS használatával.](../../storage/common/storage-sas-overview.md)
- Megtudhatja, hogyan kezelheti a tárfiókkulcsokat [a Key Vault és az Azure CLI](overview-storage-keys.md) vagy a [Azure PowerShell.](overview-storage-keys-powershell.md)
- Lásd: [Felügyelt tárfiók kulcsminái](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)