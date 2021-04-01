---
title: Gyakori hitelesítési hibák elhárítása, Azure Marketplace
description: Általános hitelesítési hibákkal kapcsolatos segítséget nyújt a Cloud Partner Portal API-k Azure Marketplace-en való használatakor.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 01af5357e4ae2f4dfb317a0931a8d0bc2b2d54e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89487317"
---
# <a name="troubleshooting-common-authentication-errors"></a>Gyakori hitelesítési hibák elhárítása

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a-vel, és továbbra is működnek a partner Centerben. Az áttérés kis változásokat mutat be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működőképes legyen a partneri központba való áttérés után. A CPP API-kat csak olyan meglévő termékekhez szabad használni, amelyek már integrálva lettek a partneri központba való áttérés előtt. az új termékeknek a partner Center beküldési API-kat kell használniuk.

Ez a cikk a Cloud Partner Portal API-k használatakor a gyakori hitelesítési hibákkal kapcsolatos segítséget nyújt.

## <a name="unauthorized-error"></a>Jogosulatlan hiba

Ha folyamatosan hibákat kap `401 unauthorized` , ellenőrizze, hogy rendelkezik-e érvényes hozzáférési jogkivonattal.  Ha még nem tette meg, hozzon létre egy alapszintű Azure Active Directory (Azure AD) alkalmazást és egy egyszerű szolgáltatásnevet a következő témakörben ismertetett módon: a [portál használatával hozzon létre egy Azure Active Directory alkalmazást és egy egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz](../active-directory/develop/howto-create-service-principal-portal.md). Ezután használja az alkalmazást vagy egy egyszerű HTTP POST-kérelmet a hozzáférés ellenőrzéséhez.  A hozzáférési jogkivonat beszerzéséhez meg kell adni a bérlő AZONOSÍTÓját, az alkalmazás AZONOSÍTÓját, az objektumazonosító és a titkos kulcsot.

## <a name="forbidden-error"></a>Tiltott hibaüzenet

Ha `403 forbidden` hibaüzenetet kap, győződjön meg arról, hogy a megfelelő egyszerű szolgáltatásnév hozzá lett adva a közzétevő fiókjához a Cloud Partner Portal. Az [Előfeltételek](./cloud-partner-portal-api-prerequisites.md) lapon szereplő lépések végrehajtásával adja hozzá a szolgáltatásnevet a portálhoz.

Ha a megfelelő szolgáltatásnevet adta hozzá, akkor ellenőrizze az összes többi információt. Ügyeljen arra, hogy a portálon megjelenő objektumazonosító kiemelten legyen. A Azure Active Directory-alkalmazás regisztrációja lapon két objektumazonosító szerepel, és a helyi objektumazonosítót kell használnia. Az alkalmazás **Alkalmazásregisztrációk** lapjára kattintva megtalálhatja a megfelelő értéket, és az alkalmazás nevére kattinthat a **helyi címtárban található felügyelt alkalmazás** területen. Ekkor megjelenik az alkalmazás helyi tulajdonságai, ahol az alábbi ábrán látható módon megtalálhatja a megfelelő objektumazonosítót a **Tulajdonságok** lapon. Továbbá győződjön meg arról, hogy a megfelelő közzétevő AZONOSÍTÓját használja az egyszerű szolgáltatás hozzáadásakor, és az API-hívást.
