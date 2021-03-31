---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "76118111"
---
## <a name="about-the-user-delegation-sas"></a>Tudnivalók a felhasználói delegálási SAS-ról

Egy tárolóhoz vagy blobhoz való hozzáféréshez szükséges SAS-tokent az Azure AD hitelesítő adataival vagy egy fiók kulcsával lehet védeni. Az Azure AD-beli hitelesítő adatokkal védett SAS-t egy felhasználói delegálási SAS-nek nevezzük, mert az SAS aláírásához használt OAuth 2,0 tokent a felhasználó nevében kell kérelmezni.

A Microsoft azt javasolja, hogy az Azure AD hitelesítő adatait akkor használja, ha a fiók kulcsa helyett biztonsági szempontból ajánlott, ami könnyebben sérülhet. Ha az alkalmazás kialakításához közös hozzáférésű aláírásokra van szükség, az Azure AD hitelesítő adataival hozzon létre egy felhasználói delegálási SAS-t a kiváló biztonság érdekében. A felhasználói delegálási SAS-vel kapcsolatos további információkért lásd: [felhasználói delegálási sas létrehozása](/rest/api/storageservices/create-user-delegation-sas).

> [!CAUTION]
> Minden olyan ügyfél, amely érvényes SAS-val rendelkezik, a Storage-fiókban lévő, az adott SAS által engedélyezett módon férhet hozzá az adataihoz. Fontos, hogy az SAS-t rosszindulatú vagy nem rendeltetésszerű használatból védjék. Saját belátása szerint oszthatja meg a SAS-t, és megtervezheti a feltört SAS visszavonását.

A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../articles/storage/common/storage-sas-overview.md).
