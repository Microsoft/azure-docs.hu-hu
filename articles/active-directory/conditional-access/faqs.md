---
title: Az Azure Active Directory feltételes hozzáférés – gyakori kérdések |} A Microsoft Docs
description: Feltételes hozzáférés – gyakori kérdések válaszok az Azure Active Directoryban.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad0494868c494b488a238a81e504c58552813907
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508976"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Az Azure Active Directory feltételes hozzáférés – gyakori kérdések

## <a name="which-applications-work-with-conditional-access-policies"></a>Mely alkalmazások a feltételes hozzáférési szabályzatainak használatát?

A feltételes hozzáférési szabályzatokkal használható alkalmazások kapcsolatos információkért lásd: [alkalmazások és az Azure Active Directory feltételes hozzáférési szabályok használó böngészőket](technical-reference.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Feltételes hozzáférési házirendek érvényben vannak a B2B-együttműködés és a vendég felhasználók számára?

Szabályzatok-vállalatközi (B2B) együttműködési felhasználókat a rendszer érvényesíti. Azonban bizonyos esetekben a felhasználó nem tud a házirend követelményeinek kielégítéséhez. A vendégfelhasználó szervezeti például előfordulhat, hogy támogatja a multi-factor authentication szolgáltatás. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>SharePoint Online-szabályzat is vonatkozik a onedrive vállalati verzióba?

Igen. SharePoint Online-szabályzat is vonatkozik, a onedrive vállalati verzióba.

## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Miért nem egy szabályzat beállítása az ügyfélalkalmazások, például a Word vagy az Outlookba?

Feltételes hozzáférési szabályzat beállítása egy szolgáltatáshoz való hozzáférésre vonatkozó követelmények. Erre a szolgáltatásra hitelesítés esetén érvényes. A szabályzat nem közvetlenül az ügyfélalkalmazás van beállítva. Ehelyett azt használja, amikor az ügyfél meghívja a szolgáltatást. Például egy házirendet, állítsa be a SharePoint SharePoint hívása ügyfelére érvényes. Egy szabályzat beállítása az Exchange Outlook vonatkozik.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Feltételes hozzáférési szabályzat vonatkozik szolgáltatásfiókok?

Feltételes hozzáférési szabályzatok érvényesek az összes felhasználói fiók. Ez magában foglalja a szolgáltatási fiókként használt felhasználói fiókok. Gyakran előfordul a felügyelet nélküli futtató szolgáltatásfiók nem felel meg a feltételes hozzáférési szabályzat. A multi-factor authentication szolgáltatás például szükség lehet. Szolgáltatásfiókok is ki kell zárni egy házirendet feltételes hozzáférési szabályzat beállítások használatával. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Graph API-k érhetők el a feltételes hozzáférési házirendek konfigurálásához?

Jelenleg nincs. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Mi az az alapértelmezett kizárási házirend nem támogatott eszközplatformok?

Feltételes hozzáférési szabályzatok jelenleg az iOS és Android-eszközök felhasználói külön-külön tartatni. Alkalmazások eszköz platformokon, alapértelmezés szerint nem érinti a feltételes hozzáférési házirend az iOS és Android-eszközökön. Bérlői rendszergazda választhat, felülbírálhatja a globális szabályzatot a felhasználók által nem támogatott platformokon való hozzáférés letiltása.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Hogyan működik a Microsoft Teams feltételes hozzáférési szabályzatokat?

Microsoft Teams erősen támaszkodik a Exchange online-hoz és a SharePoint online-hoz a core irodai alkalmazási helyzetekben, mint értekezletek, a naptárak és a fájlmegosztást. Ezek a felhőalapú alkalmazások számára beállított feltételes hozzáférési házirendek közvetlenül a Microsoft Teams vonatkozik a Microsoft Teams, amikor egy felhasználó jelentkezik.

Microsoft Teams is külön-külön is támogatott az Azure Active Directory feltételes hozzáférési házirendek egy felhőalkalmazás. Felhőalapú alkalmazás esetében beállított feltételes hozzáférési házirendek Microsoft Teams vonatkozik, amikor egy felhasználó bejelentkezik. Azonban az Exchange online-hoz és a SharePoint online-hoz hasonló más alkalmazásokat a megfelelő házirendek nélkül felhasználók előfordulhat, hogy is közvetlenül elérni ezeket az erőforrásokat.

Microsoft Teams asztali ügyfelek Windows és Mac támogatja a modern hitelesítést. Bejelentkezés a az Azure Active Directory Authentication Library (ADAL) az ügyfélalkalmazások számára a Microsoft Office-alapú platformon biztosítható a modern hitelesítést.

## <a name="next-steps"></a>További lépések

- Feltételes hozzáférési házirendjei a környezet konfigurálásához tekintse meg a [gyakorlati tanácsok az Azure Active Directory feltételes hozzáférés](best-practices.md). 
