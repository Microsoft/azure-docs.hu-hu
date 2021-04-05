---
title: Egyéni fejlesztésű alkalmazáshoz tartozó API keresése | Azure
description: Az egyes API-k eléréséhez szükséges engedélyek konfigurálása az egyéni fejlesztésű Azure AD-alkalmazásokban
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 28cfb3d8b09c9661d16ac6e7146c50e7043d913a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581958"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Egyéni fejlesztésű alkalmazáshoz szükséges speciális API megkeresése

Az API-khoz való hozzáféréshez hozzáférési hatókörök és szerepkörök konfigurálása szükséges. Ha szeretné elérhetővé tenni az erőforrás-alkalmazás webes API-jait az ügyfélalkalmazások számára, konfigurálja az API hozzáférési hatóköreit és szerepköreit. Ha azt szeretné, hogy egy ügyfélalkalmazás hozzáférjen egy webes API-hoz, konfigurálja az API-k eléréséhez szükséges engedélyeket az alkalmazás regisztrálásához.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Erőforrás-alkalmazás konfigurálása webes API-k közzétételére

Ha a webes API-t teszi elérhetővé, az API a **Select an API (API kiválasztása** ) listában jelenik meg, amikor engedélyeket ad az alkalmazás regisztrálásához. Hozzáférési hatókörök hozzáadásához kövesse az [alkalmazás konfigurálása a webes API-k számára elérhetővé](quickstart-configure-app-expose-web-apis.md)tételéhez című témakör lépéseit.

## <a name="configuring-a-client-application-to-access-web-apis"></a>Ügyfélalkalmazás konfigurálása a webes API-k eléréséhez

Amikor engedélyeket ad az alkalmazás regisztrálásához, **HOZZÁADHAT API-hozzáférést** az elérhető webes API-khoz. A webes API-k eléréséhez kövesse az [ügyfélalkalmazás konfigurálása a webes API-k eléréséhez](quickstart-configure-app-access-web-apis.md)című témakör lépéseit.

## <a name="next-steps"></a>Következő lépések

- [A Azure Active Directory alkalmazás jegyzékfájljának ismertetése](./reference-app-manifest.md)