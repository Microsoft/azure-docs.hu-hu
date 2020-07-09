---
title: MFA megkövetelése nem megbízható hálózatokból – Azure Active Directory
description: Megtudhatja, hogyan konfigurálhat feltételes hozzáférési szabályzatot Azure Active Directoryban (Azure AD) a nem megbízható hálózatok hozzáférési kísérleteinek eléréséhez.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7986ca441f7d274670d8fa0238e7dcfa01497b6f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253171"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Útmutató: az MFA megkövetelése a nem megbízható hálózatokból való hozzáféréshez feltételes hozzáféréssel   

Azure Active Directory (Azure AD) lehetővé teszi az egyszeri bejelentkezést az eszközökre, alkalmazásokra és szolgáltatásokra bárhonnan. A felhasználók nem csak a szervezet hálózatáról, hanem bármely nem megbízható internetes helyről is hozzáférhetnek a felhőalapú alkalmazásokhoz. A nem megbízható hálózatokból való hozzáférésre vonatkozó gyakori ajánlott eljárás a többtényezős hitelesítés (MFA) megkövetelése.

Ebből a cikkből megtudhatja, hogyan konfigurálhat olyan feltételes hozzáférési szabályzatot, amely az MFA használatát igényli a nem megbízható hálózatokról való hozzáféréshez. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy ismeri a következőket: 

- Az Azure AD feltételes hozzáférés [alapvető fogalmai](overview.md) 
- A feltételes hozzáférési házirendek konfigurálásának [ajánlott eljárásai](best-practices.md) a Azure Portal

## <a name="scenario-description"></a>Forgatókönyv leírása

A biztonság és a termelékenység egyensúlyának elsajátításához elegendő lehet a szervezet hálózatán lévő bejelentkezések jelszavának megkövetelése. A nem megbízható hálózati helyről való hozzáféréshez azonban nagyobb a kockázata, hogy a bejelentkezéseket nem a legitim felhasználók hajtják végre. A probléma megoldása érdekében letilthatja a nem megbízható hálózatokból való hozzáférést. Azt is megteheti, hogy többtényezős hitelesítést (MFA) is megkövetelheti, hogy további garanciát szerezzen a fiók jogos tulajdonosa által történt kísérletekhez. 

Az Azure AD feltételes hozzáférésével ezt a követelményt egyetlen, hozzáférést biztosító szabályzattal kezelheti: 

- A kiválasztott felhőalapú alkalmazások
- A kiválasztott felhasználók és csoportok esetében  
- Többtényezős hitelesítés megkövetelése 
- Ha a hozzáférés a következőkből származik: 
   - Nem megbízható hely

## <a name="implementation"></a>Megvalósítás

Ennek a forgatókönyvnek a feladata a nem *megbízható hálózati helyről való hozzáférés* feltételes hozzáférési feltételre való lefordítása. A feltételes hozzáférési házirendben beállíthatja a [helyszínek feltételét](location-condition.md) a hálózati telephelyekhez kapcsolódó forgatókönyvek kezeléséhez. A helyszínek feltétele lehetővé teszi az elnevezett helyszínek kiválasztását, amelyek az IP-címtartományok, országok és régiók logikai csoportjai.  

A szervezet általában egy vagy több címtartományt birtokol, például: 199.30.16.0-199.30.16.15.
Megnevezett helyet az alábbiak szerint állíthat be:

- A tartomány meghatározása (199.30.16.0/28) 
- Leíró név, például **vállalati hálózat** kiosztása 

Ahelyett, hogy az összes olyan helyet meg kellene határozni, amely nem megbízható, a következőket teheti:

- Bármely hely belefoglalása 

   ![Feltételes hozzáférés](./media/untrusted-networks/02.png)

- Az összes megbízható helyszín kizárása 

   ![Feltételes hozzáférés](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Szabályzat érvénybe léptetése

A jelen cikkben ismertetett megközelítéssel mostantól konfigurálhat feltételes hozzáférési szabályzatot nem megbízható helyszínekhez. Annak ellenőrzéséhez, hogy a házirend a várt módon működik-e, az ajánlott eljárás az, hogy tesztelje azt az éles üzemben való működés előtt. Ideális esetben egy tesztelési bérlő használatával ellenőrizheti, hogy az új szabályzat a kívánt módon működik-e. További információ: [új szabályzat üzembe helyezése](best-practices.md#how-should-you-deploy-a-new-policy). 

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a feltételes hozzáférésről, tekintse meg a [Mi a feltételes hozzáférés Azure Active Directory?](../active-directory-conditional-access-azure-portal.md) című témakört.
