---
title: Védett webes API – áttekintés
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre védett webes API-t (áttekintés).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 434c255f0b9634e0d17bad1627ddd92af9498bfe
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582434"
---
# <a name="scenario-protected-web-api"></a>Forgatókönyv: védett webes API

Ebből a forgatókönyvből megtudhatja, hogyan teheti elérhetővé a webes API-t. Azt is megtudhatja, hogyan védhető a webes API, hogy csak a hitelesített felhasználók férhessenek hozzá.

A webes API használatához engedélyezze a hitelesített felhasználók számára a munkahelyi és iskolai fiókokat, vagy engedélyezze a személyes Microsoft-fiókokat.

## <a name="specifics"></a>Sajátosságai

Itt találja a webes API-k elleni védelemhez szükséges információkat:

- Az alkalmazás regisztrációjának legalább egy *hatókört* vagy egy *alkalmazás-szerepkört* ki kell mutatnia.
  - A hatóköröket a felhasználók nevében meghívó webes API-k teszik közzé.
  - Az alkalmazási szerepköröket Daemon-alkalmazások által hívott webes API-k teszik közzé (amelyek a webes API-t a saját nevükön meghívja).
- Ha új webes API-alkalmazás regisztrációját hozza létre, válassza ki a webes API által elfogadott [hozzáférési jogkivonat verzióját](reference-app-manifest.md#accesstokenacceptedversion-attribute) a következőre: `2` . Az örökölt webes API-k esetében az elfogadott jogkivonat verziója lehet `null` , ez az érték azonban csak a szervezetek számára korlátozza a bejelentkezési célközönséget, és a személyes Microsoft-fiókokat (MSA) nem támogatja a rendszer.
- A webes API-kód konfigurációjának ellenőriznie kell a webes API meghívásakor használt jogkivonatot.
- A vezérlő műveleteiben szereplő kódnak ellenőriznie kell a tokenben lévő szerepköröket vagy hatóköröket.

## <a name="recommended-reading"></a>Ajánlott olvasás

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Következő lépések

Lépjen tovább a forgatókönyv következő cikkére, amely az [alkalmazás regisztrálását](scenario-protected-web-api-app-registration.md)ismerteti.
