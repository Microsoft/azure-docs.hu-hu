---
title: Ügyfél-adattárolás az ausztrál és az új-zélandi ügyfelek számára – Azure AD
description: Megtudhatja, hol tárolja a Azure Active Directory az ügyfelekkel kapcsolatos adatait az ausztrál és az új-zélandi ügyfelek számára.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/21/2020
ms.custom: it-pro, seodec18, references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d7c37e64e4f1b339ae66fe3d9135b1874476eb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836971"
---
# <a name="customer-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Ügyfél-adattárolás az ausztráliai és új-zélandi ügyfelek számára Azure Active Directory 

A Azure Active Directory (Azure AD) a Microsoft Online szolgáltatásra való feliratkozáskor megadott ország alapján tárolja az ügyféladatokat a földrajzi helyen. A Microsoft Online Services Microsoft 365 és az Azure-t is tartalmazza. 

További információ az Azure AD és más Microsoft-szolgáltatások adatairól: hol találhatók az [adatok?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) című rész a Microsoft adatvédelmi központban.

2020. február 26-án a Microsoft megkezdte az Azure AD vásárlói adatainak tárolását az új bérlők számára az ausztráliai vagy új-zélandi számlázási címen az ausztrál adatközpontokon belül. A Microsoft a 2020-es és a 2021-es március 31. között megjelenő meglévő bérlőket az ausztráliai vagy új-zélandi számlázási címen keresztül telepíti át az ausztrál adatközpontok számára, anélkül, hogy az ügyfél beavatkozására lenne szükség. Az áttelepítési folyamat nem jár leállással az ügyfelek számára, és az áttelepítés során nem befolyásolja a bérlő funkcióit.

Emellett bizonyos Azure AD-funkciók még nem támogatják az ügyféladatok tárolását Ausztráliában. Tekintse meg az [Azure ad-beli adatleképezést](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)az egyes szolgáltatásokkal kapcsolatos információkért. Például Microsoft Azure AD Multi-Factor Authentication tárolja az ügyféladatokat az Egyesült Államokban, és globálisan dolgozza fel azokat. Tekintse [meg az Azure AD multi-Factor Authentication adattárolási és ügyféladatokat](../authentication/concept-mfa-data-residency.md).

> [!NOTE]
> Az Azure AD-vel integrált Microsoft-termékek,-szolgáltatások és harmadik féltől származó alkalmazások hozzáférhetnek az ügyféladatok eléréséhez. Értékelje ki, hogy az egyes termékek, szolgáltatások és alkalmazások hogyan dolgozzák fel az ügyféladatokat az adott termék, szolgáltatás és alkalmazás alapján, és hogy megfelelnek-e a vállalat adattárolási követelményeinek. A Microsoft-szolgáltatások adattárolási helyével kapcsolatban a Microsoft biztonsági és adatkezelési központ [az adatok tárolási helyével](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) foglalkozó szakaszában találhat további információt.