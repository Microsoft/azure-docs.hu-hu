---
title: Rugalmasság kialakítása az ügyfelek identitás-és hozzáférés-kezelésében Azure AD B2C használatával | Microsoft Docs
description: Módszerek a rugalmasság kiépítéséhez az ügyfél identitásában és a hozzáférés-kezelésben Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b40918db03c260f899c36d306c892b787cc6371c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724891"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>Hozzon létre rugalmasságot az ügyfelek identitásában és a hozzáférés-kezelésben Azure Active Directory B2C

A [Azure Active Directory (ad) B2C](../../active-directory-b2c/overview.md) egy ügyfél-identitás-és hozzáférés-kezelési (CIAM) platform, amelynek célja, hogy segítsen a kritikus fontosságú ügyfeleket érintő alkalmazások sikeres elindításában. Számos beépített funkció áll rendelkezésre a [rugalmasság](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/) érdekében, amelyek célja, hogy segítse a szolgáltatás méretezését az igényeinek megfelelően, és javítsa a rugalmasságot a lehetséges kimaradási helyzetek szem előtt tartásával. Továbbá a kritikus fontosságú alkalmazások indításakor fontos figyelembe venni az alkalmazás különböző kialakítási és konfigurációs elemeit, valamint azt, hogy az alkalmazás hogyan legyen konfigurálva Azure AD B2Con belül, hogy a kiesési vagy meghibásodási helyzetekben rugalmas viselkedést kapjon. Ebben a cikkben a rugalmasság növelését segítő ajánlott eljárásokat ismertetjük.

A rugalmas szolgáltatás az egyik, amely továbbra is működik a fennakadások ellenére. A szolgáltatás rugalmasságának javítása érdekében a következőket teheti:

- az összes összetevő ismertetése

- egyetlen pont meghibásodásának kiküszöbölése

- hibás összetevők elkülönítése a hatás korlátozására

- redundancia biztosítása gyors feladatátvételi mechanizmusokkal és helyreállítási útvonalakkal

Az alkalmazás fejlesztése során azt javasoljuk, hogy a megoldás identitás-összetevőivel [növelje a hitelesítés és az engedélyezés rugalmasságát az alkalmazásokban](resilience-app-development-overview.md) . Ez a cikk a Azure AD B2C alkalmazásokra vonatkozó rugalmassági fejlesztések megoldását kísérli meg. Javaslataink CIAM függvények szerint vannak csoportosítva.

![](media/resilience-b2c/high-level-components.png)A képen a következő részekben látható CIAM-összetevőkből megismerheti a rugalmasság kialakítását az alábbi területeken:

- [Végfelhasználói élmény](resilient-end-user-experience.md): engedélyezzen egy tartalék tervet a hitelesítési folyamathoz, és enyhítse a Azure ad B2C hitelesítési szolgáltatás megszakadásának lehetséges következményeit.

- [Felületek külső folyamatokkal](resilient-external-processes.md): rugalmasság létrehozása az alkalmazásokban és a felületeken a hibákból való helyreállítással.  

- [Ajánlott eljárások a fejlesztőknek](resilience-b2c-developer-best-practices.md): kerülje a bizonytalanságot a gyakori egyéni házirend-problémák miatt, és javítsa a hibák kezelését olyan területeken, mint például a jogcímek ellenőrzése, harmadik féltől származó alkalmazások és REST API-k.

- [Figyelés és elemzés](resilience-with-monitoring-alerting.md): a szolgáltatás állapotának felmérése a legfontosabb mutatók figyelésével, valamint a hibák észlelésével és a teljesítmény megszakításával a riasztások segítségével.

- [Rugalmasság kialakítása a hitelesítési infrastruktúrában](resilience-in-infrastructure.md)

- [A hitelesítés és az engedélyezés rugalmasságának fokozása az alkalmazásokban](resilience-app-development-overview.md)

Tekintse meg ezt a videót, amelyből megtudhatja, hogyan hozhat létre rugalmas és méretezhető folyamatokat Azure AD B2C használatával.
>[!Video https://www.youtube.com/embed/8f_Ozpw9yTs]
