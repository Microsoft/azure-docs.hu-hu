---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ba715d510dc296ffa8f9c0ee58841f284416a118
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86027331"
---
## <a name="protect-your-access-keys"></a>A hozzáférési kulcsok védetté való ellátása

A Storage-fiók hozzáférési kulcsa a Storage-fiókhoz tartozó legfelső szintű jelszóhoz hasonló. Mindig ügyeljen arra, hogy megvédje a hozzáférési kulcsait. A kulcsok biztonságos kezelése és elforgatása Azure Key Vault használatával. Kerülje a hozzáférési kulcsok más felhasználók számára történő terjesztését, a merevlemezek kódolását, vagy a mások számára elérhető egyszerű szövegben való mentését. Ha úgy gondolja, hogy a kulcsok biztonsága megsérült, akkor forgassa el a kulcsokat.

> [!NOTE]
> A Microsoft azt javasolja, hogy a Azure Active Directory (Azure AD) használatával engedélyezze a blob-és üzenetsor-adatkéréseket, ha lehetséges, a megosztott kulcs helyett. Az Azure AD kiváló biztonságot és könnyű használatot biztosít a megosztott kulcson keresztül. További információ az adatok Azure AD-vel való hozzáférésének engedélyezéséről: az [Azure-blobok és-várólisták hozzáférésének engedélyezése Azure Active Directory használatával](../articles/storage/common/storage-auth-aad.md).
