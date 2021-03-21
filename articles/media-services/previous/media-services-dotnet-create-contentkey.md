---
title: Tartalomkulcsok létrehozása a .NET-tel
description: Ez a cikk bemutatja, hogyan hozhatók létre tartalmi kulcsok a .NET használatával. Ezek a kulcsok biztonságos hozzáférést biztosítanak az eszközökhöz.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 05bf928490e94f43b755e1958213899e9e1e98e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014173"
---
# <a name="create-contentkeys-with-net"></a>Tartalomkulcsok létrehozása a .NET-tel

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

A Media Services lehetővé teszi titkosított eszközök létrehozását és továbbítását. A **ContentKey** biztonságos hozzáférést biztosít az **eszközhöz**. 

Új eszköz létrehozásakor (például a [fájlok feltöltése](media-services-dotnet-upload-files.md)előtt) a következő titkosítási beállításokat adhatja meg: **StorageEncrypted**, **CommonEncryptionProtected** vagy **EnvelopeEncryptionProtected**. 

Amikor eszközöket továbbít az ügyfeleknek, beállíthatja, [hogy az eszközök dinamikusan legyenek titkosítva](media-services-dotnet-configure-asset-delivery-policy.md) a következő két titkosítás egyikével: **DynamicEnvelopeEncryption** vagy **DynamicCommonEncryption**.

A titkosított eszközöket társítani kell a **ContentKey** s-hez. Ez a cikk a tartalmi kulcs létrehozását ismerteti.

> [!NOTE]
> Amikor új **StorageEncrypted** -eszközt hoz létre a Media Services .net SDK-val, a rendszer automatikusan létrehozza és csatolja a **ContentKey** az objektumhoz.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
A tartalmi kulcs létrehozásakor beállított értékek egyike a tartalmi kulcs típusa. Válasszon az alábbi értékek közül. 

```csharp
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }
```

## <a name="create-envelope-type-contentkey"></a><a id="envelope_contentkey"></a>ContentKey-típus létrehozása
A következő kódrészlet létrehozza a boríték titkosítási típusának tartalmi kulcsát. Ezután hozzárendeli a kulcsot a megadott objektumhoz.

```csharp
    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

call

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);
```


## <a name="create-common-type-contentkey"></a><a id="common_contentkey"></a>Common Type ContentKey létrehozása
A következő kódrészlet létrehozza a közös titkosítási típus tartalmi kulcsát. Ezután hozzárendeli a kulcsot a megadott objektumhoz.

```csharp
    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
call

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 
```

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

