---
title: Key Vault .NET 2. x API kibocsátási megjegyzései | Microsoft Docs
description: Megtudhatja, hogyan frissítheti az Azure Key Vault korábbi verzióira írt alkalmazásokat a C# és a .NET-hez készült Azure Key Vault Library 2,0-es verziójával való együttműködéshez.
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 018570019b306dced76760fefa4441ee7d86ad2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013955"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2,0 – kibocsátási megjegyzések és áttelepítési útmutató
A következő információk segítségével áttelepíthető a C# és a .NET Azure Key Vault könyvtárának 2,0-es verziójára.  A korábbi verziókhoz írt alkalmazásokat frissíteni kell a legújabb verzió támogatásához.  Ezek a módosítások az új és továbbfejlesztett szolgáltatások, például **Key Vault tanúsítványok** teljes körű támogatásához szükségesek.

## <a name="key-vault-certificates"></a>Tanúsítványok Key Vault

Key Vault tanúsítványok kezelik a x509-tanúsítványokat, és a következő viselkedéseket támogatják:  

* Tanúsítványokat hozhat létre Key Vault létrehozási folyamattal vagy létező tanúsítvány importálásával. Ez magában foglalja az önaláírt és a hitelesítésszolgáltató (CA) által generált tanúsítványokat is.
* Biztonságosan tárolhatja és kezelheti a x509 tanúsítványokat a titkos kulcsokkal való interakció nélkül.  
* Határozza meg azokat a házirendeket, amelyek közvetlen Key Vault a tanúsítvány életciklusának kezelésére.  
* Adja meg a kapcsolattartási adatokat az életciklus eseményeihez, például a lejárat figyelmeztetéseit és a megújítási értesítéseket.  
* Tanúsítványok automatikus megújítása a kiválasztott kiállítókkal (Key Vault partner X509-szolgáltatókkal és hitelesítésszolgáltatókkal). * az alternatív (nem partneri) tanúsítvány és a hitelesítésszolgáltatók (nem támogatja az automatikus megújítást).  

## <a name="net-support"></a>.NET-támogatás

* A **.net 4,0** -es verziója nem támogatott az Azure Key Vault .net Library 2,0-es verziójában
* A **.NET-keretrendszer 4.5.2** -es verzióját a Azure Key Vault .net Library 2,0-es verziója támogatja
* A **.NET Standard 1,4** -es verzióját a Azure Key Vault .net Library 2,0 verziója támogatja

## <a name="namespaces"></a>Névterek

* A **modellek** névtere a **Microsoft. Azure.** kulcstartóból a **Microsoft. Azure. kulcstartó. models** típusra módosul.
* A **Microsoft. Azure. kulcstartó. a belső** névtér el lett dobva.
* A következő Azure SDK-függőségi névterek 

    - A **Hyak. Common** mostantól a **Microsoft. Rest**.
    - A **Hyak. Common. Internals** mostantól a **Microsoft. Rest. szerializálás**.

## <a name="type-changes"></a>Típus módosítása

* A *titkos* kód a *SecretBundle* módosult
* A *szótár* *IDictionary* módosult
* *List \<T> , string []* módosította *IList \<T>*
* A *NextList* *NextPageLink* módosult

## <a name="return-types"></a>Visszatérési típusok

* A **Feladatlista** és a **SecretList** mostantól *IPage \<T>* ad vissza a *ListKeysResponseMessage* helyett.
* A generált **BackupKeyAsync** most visszaadja a *BackupKeyResult*, amely tartalmazza az *értéket* (biztonsági másolati blob). Korábban a metódus burkolta, és csak az értéket adta vissza.

## <a name="exceptions"></a>Kivételek

* A *KeyVaultClientException* *KeyVaultErrorException* értékre változik
* A szolgáltatási hiba kivétel miatt módosult *. Hiba* a *kivételnél. Body. error. Message*.
* A következő hibaüzenetből eltávolított további adatokat: **[JsonExtensionData]**.

## <a name="constructors"></a>Konstruktorok

* A *HttpClient* konstruktor argumentumaként való elfogadása helyett a konstruktor csak a *HttpClientHandler* vagy a *DelegatingHandler []* értéket fogadja el.

## <a name="downloaded-packages"></a>Letöltött csomagok

Ha egy ügyfél Key Vault függőséget dolgoz fel, a következő csomagok tölthetők le:

### <a name="previous-package-list"></a>Korábbi csomagok listája

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Aktuális csomagok listája

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Osztályok módosításai

* A **UnixEpoch** osztály el lett távolítva.
* A **Base64UrlConverter** osztály a **Base64UrlJsonConverter** névre lett átnevezve.

## <a name="other-changes"></a>További változások

* A KV-os művelet újrapróbálkozási házirendjének konfigurációjának támogatása átmeneti hibák esetén az API ezen verziójához lett hozzáadva.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft. Azure. Management. kulcstartó NuGet

* A tárolót visszaadó műveletek esetében a visszatérési **típus olyan osztály** *volt, amely egy tár* tulajdonságot foglalt magukban. A visszatérési típus most a *Vault*.
* A *PermissionsToKeys* és a *PermissionsToSecrets* mostantól *engedélyekkel rendelkeznek. kulcsok* és *engedélyek. titkok*
* Bizonyos visszatérési típusok változásai a vezérlő-síkon is érvényesek.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft. Azure. kulcstartó. Extensions NuGet

* A csomag fel van osztva a **Microsoft. Azure. kulcstartó. Extensions** és a **Microsoft. Azure. kulcstartó. titkosítási** műveletekre.

