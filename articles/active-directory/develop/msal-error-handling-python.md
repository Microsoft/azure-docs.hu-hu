---
title: Hibák és kivételek kezelése a Pythonhoz készült MSAL-ben
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan kezelheti a hibákat és kivételeket, a feltételes hozzáférési jogcímeket, és próbálkozzon újra a Python-alkalmazások MSAL.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 3046787393d38ed60b54236f33acc065db90321d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761088"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>Hibák és kivételek kezelése a Pythonhoz készült MSAL-ben

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>Hibakezelés a Python MSAL

A Pythonhoz készült MSAL a legtöbb hibát az API-hívásból visszaadott értékként továbbítja a rendszer. A hiba a Microsoft Identity platform JSON-válaszát tartalmazó szótárként jelenik meg.

* A sikeres válasz tartalmazza a `"access_token"` kulcsot. A válasz formátumát az OAuth2 protokoll határozza meg. További információ: [5,1 sikeres válasz](https://tools.ietf.org/html/rfc6749#section-5.1)
* A hiba a következőt tartalmazza: `"error"` és általában `"error_description"` . A válasz formátumát az OAuth2 protokoll határozza meg. További információ: 5,2-es [hiba válasza](https://tools.ietf.org/html/rfc6749#section-5.2)

Ha a rendszer hibát ad vissza, a `"error_description"` kulcs egy ember által olvasható üzenetet tartalmaz, amely általában egy Microsoft Identity platform hibakódot tartalmaz. A különböző hibakódokról a [hitelesítési és engedélyezési hibakódok](./reference-aadsts-error-codes.md)című témakörben olvashat bővebben.

A MSAL for Python esetében a kivételek ritkák, mivel a legtöbb hibát egy hibaérték visszaadásával kezeli a rendszer. A `ValueError` kivétel csak akkor fordul elő, ha probléma merül fel a könyvtár használatának megkísérlése során, például ha az API-paraméterek helytelenül vannak formázva.

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Következő lépések

Vegye fontolóra a [MSAL for Python naplózásának](msal-logging-python.md) engedélyezését a problémák diagnosztizálásához és hibakereséséhez.
