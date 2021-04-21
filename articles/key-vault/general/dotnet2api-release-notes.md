---
title: Key Vault .NET 2.x API kibocsátási megjegyzései| Microsoft Docs
description: Ismerje meg, hogyan frissítheti az Azure Key Vault korábbi verzióihoz írt alkalmazásokat, hogy azok a C# és .NET Azure Key Vault kódtár 2.0-s verziójával tudjanak dolgozni.
services: key-vault
author: msmbaldwin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: b923cc289dc0229f18eba144b09ceb34e0edea4e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751885"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 – Kibocsátási megjegyzések és áttelepítési útmutató
Az alábbi információk segítenek a C# és .NET Azure Key Vault kódtárának 2.0-s verziójára való áttelepítésben.  A korábbi verziókhoz írt alkalmazásokat frissíteni kell a legújabb verzió támogatásához.  Ezek a módosítások szükségesek az új és továbbfejlesztett szolgáltatások, például a tanúsítványok teljes **Key Vault támogatásához.**

## <a name="key-vault-certificates"></a>Key Vault tanúsítványok

Key Vault a tanúsítványok x509-tanúsítványokat kezelnek, és a következő viselkedéseket támogatják:  

* Tanúsítványokat hozhat létre egy Key Vault létrehozási folyamattal, vagy meglévő tanúsítványt importálhat. Ez magában foglalja az önaírt és a hitelesítésszolgáltató által létrehozott tanúsítványokat is.
* Biztonságosan tárolhatja és kezelheti az x509-tanúsítványtárolót anélkül, hogy a titkos kulcsanyagokat használva interakcióba esik.  
* A tanúsítvány-életciklus kezeléséhez Key Vault szabályzatokat definiálhat.  
* Adja meg az életciklus-események kapcsolattartási adatait, például a lejárati figyelmeztetéseket és a megújítási értesítéseket.  
* Tanúsítványok automatikus megújítása a kiválasztott kiállítókkal (Key Vault X509-tanúsítványszolgáltatók és hitelesítésszolgáltatók).* A másodlagos (nem partneri) hitelesítésszolgáltatók által nyújtott és a hitelesítésszolgáltatók által nyújtott támogatás (nem támogatja az automatikus megújítást).  

## <a name="net-support"></a>.NET-támogatás

* **A .NET-kódtár** 2.0-s verziója nem támogatja Azure Key Vault .NET 4.0-s verzióját
* .NET-keretrendszer .NET-kódtár 2.0-s verziója támogatja a **4.5.2-es** Azure Key Vault.
* **A .NET Standard 1.4-es** verzióját az Azure Key Vault .NET-kódtár 2.0-s verziója támogatja

## <a name="namespaces"></a>Névterek

* A modellek **névtere** **Microsoft.Azure.KeyVault** névtérről **Microsoft.Azure.KeyVault.Models névtérre módosult.**
* A **Microsoft.Azure.KeyVault.Internal** névtér el van dobva.
* A következő Azure SDK-függőségek névterei 

    - **A Hyak.Common** mostantól **Microsoft.Rest.**
    - **A Hyak.Common.Internals** mostantól **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Típusváltozások

* *Titkos változott* *SecretBundle-re*
* *A szótár az* *IDictionary szóra változott*
* *List \<T> (Lista) , sztring []* *IListre változott \<T>*
* *A NextList* a  *következőre változott: NextPageLink*

## <a name="return-types"></a>Visszatérési típusok

* **A KeyList** és **a SecretList** mostantól *IPage \<T>* értéket ad vissza *a ListKeysResponseMessage helyett*
* A létrehozott **BackupKeyAsync most** a *BackupKeyResult* értéket adja vissza, amely *tartalmazza a Value* (biztonsági mentési blob) értéket. Korábban a metódus burkolt, és csak az értéket adott vissza.

## <a name="exceptions"></a>Kivételek

* *A KeyVaultClientException* a *KeyVaultErrorException kódra módosult*
* A szolgáltatáshiba kivételről *változott. Kivételhiba.* *Body.Error.Message*.
* További információ el lett távolítva **a(z) [JsonExtensionData] hibaüzenetből.**

## <a name="constructors"></a>Konstruktorok

* A *HttpClient* konstruktor-argumentumként való elfogadása helyett a konstruktor csak a *HttpClientHandler* vagy *a DelegatingHandler[] metódust fogadja el.*

## <a name="downloaded-packages"></a>Letöltött csomagok

Amikor egy ügyfél feldolgoz egy Key Vault függőséget, a rendszer a következő csomagokat letölti:

### <a name="previous-package-list"></a>Előző csomaglista

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Aktuális csomaglista

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Osztályváltozások

* **A UnixEpoch** osztály el lett távolítva.
* **A Base64UrlConverter** osztály új neve **Base64UrlJsonConverter.**

## <a name="other-changes"></a>További változások

* A KV-művelet újrapróbálkozási szabályzatának átmeneti hibák esetén való konfigurálási támogatása hozzáadva az API ezen verziójához.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* A tárolót visszaküldő műveletek visszatérési típusa egy olyan osztály volt, amely tartalmazott **egy Vault tulajdonságot.**  A visszatérési típus mostantól *Vault.*
* *A PermissionsToKeys* és *a PermissionsToSecrets* mostantól *Permissions.Keys* és *Permissions.Secrets*
* Bizonyos visszatérési típusok módosításai a vezérlősíkra is érvényesek.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* A csomag a **Microsoft.Azure.KeyVault.Extensions** és **a Microsoft.Azure.KeyVault.Cryptography** szolgáltatásokra van fel bontva a titkosítási műveletekhez.

