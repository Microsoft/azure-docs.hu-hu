---
title: Transport Layer Security (TLS) konfigurálása ügyfélalkalmazás számára
titleSuffix: Azure Storage
description: Egy ügyfélalkalmazás konfigurálása az Azure Storage szolgáltatással való kommunikációra Transport Layer Security (TLS) minimális verziójával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 37b8c79df5b208feea185292fa09c323b64fa27d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89001807"
---
# <a name="configure-transport-layer-security-tls-for-a-client-application"></a>Transport Layer Security (TLS) konfigurálása ügyfélalkalmazás számára

Biztonsági okokból az Azure Storage-fiókhoz szükség lehet arra, hogy az ügyfelek a Transport Layer Security (TLS) minimális verzióját használják a kérelmek küldéséhez. Az Azure Storage-ba irányuló hívások sikertelenek lesznek, ha az ügyfél olyan TLS-verziót használ, amely alacsonyabb a minimálisan szükséges verziónál. Ha például egy Storage-fiókhoz TLS 1,2 szükséges, akkor a TLS 1,1-t használó ügyfél által eljuttatott kérelem sikertelen lesz.

Ez a cikk azt ismerteti, hogyan konfigurálható egy ügyfélalkalmazás a TLS egy adott verziójának használatára. További információ a TLS minimálisan szükséges verziójának Azure Storage-fiókhoz való konfigurálásáról: [Transport Layer Security (TLS) minimálisan szükséges verziójának konfigurálása egy Storage-fiókhoz](transport-layer-security-configure-minimum-version.md).

## <a name="configure-the-client-tls-version"></a>Az ügyfél TLS-verziójának konfigurálása

Ahhoz, hogy egy ügyfél kérelmet küldjön a TLS egy adott verziójára, az operációs rendszernek támogatnia kell ezt a verziót.

Az alábbi példák bemutatják, hogyan állíthatja be az ügyfél TLS-verzióját a 1,2-re a PowerShell vagy a .NET használatával. Az ügyfél által használt .NET-keretrendszernek támogatnia kell a TLS 1,2-et. További információ: [a TLS 1,2 támogatása](/dotnet/framework/network-programming/tls#support-for-tls-12).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi példa bemutatja, hogyan engedélyezheti a TLS 1,2-et egy PowerShell-ügyfélben:

```powershell
# Set the TLS version used by the PowerShell client to TLS 1.2.
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

# Create a new container.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context
New-AzStorageContainer -Name "sample-container" -Context $ctx
```

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Az alábbi példa bemutatja, hogyan engedélyezheti a TLS 1,2-et egy .NET-ügyfélen az Azure Storage ügyféloldali kódtár 12-es verziójának használatával:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Networking.cs" id="Snippet_ConfigureTls12":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Az alábbi példa bemutatja, hogyan engedélyezheti a TLS 1,2-et egy .NET-ügyfélen az Azure Storage ügyféloldali kódtár 11-es verziójának használatával:

```csharp
static void EnableTls12()
{
    // Enable TLS 1.2 before connecting to Azure Storage
    System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

    // Add your connection string here.
    string connectionString = "";

    // Connect to Azure Storage and create a new container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
}
```

---

## <a name="verify-the-tls-version-used-by-a-client"></a>Az ügyfél által használt TLS-verzió ellenőrzése

Annak ellenőrzéséhez, hogy az ügyfél a megadott TLS-verziót használta-e kérelem küldésére, használhatja a [hegedűst](https://www.telerik.com/fiddler) vagy egy hasonló eszközt. Nyissa meg a hegedűst az ügyfél hálózati forgalmának megkezdéséhez, majd hajtsa végre az egyik példát az előző szakaszban. Tekintse meg a Hegedűs nyomkövetését annak ellenőrzéséhez, hogy a TLS helyes verzióját használta-e a kérelem elküldéséhez, ahogy az az alábbi képen is látható.

:::image type="content" source="media/transport-layer-security-configure-client-version/fiddler-trace-tls-version.png" alt-text="A kérelemben használt TLS-verziót jelző Hegedűs nyomkövetését bemutató képernyőkép":::

## <a name="next-steps"></a>Következő lépések

- [Transport Layer Security (TLS) minimálisan szükséges verziójának konfigurálása a Storage-fiókhoz](transport-layer-security-configure-minimum-version.md)
- [Biztonsági javaslatok a blob Storage-hoz](../blobs/security-recommendations.md)
