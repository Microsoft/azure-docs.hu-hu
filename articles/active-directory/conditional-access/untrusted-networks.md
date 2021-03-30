---
title: MFA megkövetelése nem megbízható hálózatokból – Azure Active Directory
description: Megtudhatja, hogyan konfigurálhat feltételes hozzáférési szabályzatot Azure Active Directoryban (Azure AD) a nem megbízható hálózatok hozzáférési kísérleteinek eléréséhez.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c020a9be7683bf045dbcc747dad3cb45058dd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93077676"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Útmutató: az MFA megkövetelése a nem megbízható hálózatokból való hozzáféréshez feltételes hozzáféréssel   

Azure Active Directory (Azure AD) lehetővé teszi az egyszeri bejelentkezést az eszközökre, alkalmazásokra és szolgáltatásokra bárhonnan. A felhasználók nem csak a szervezet hálózatáról, hanem bármely nem megbízható internetes helyről is hozzáférhetnek a felhőalapú alkalmazásokhoz. A nem megbízható hálózatokból való hozzáférésre vonatkozó gyakori ajánlott eljárás a többtényezős hitelesítés (MFA) megkövetelése.

Ebből a cikkből megtudhatja, hogyan konfigurálhat olyan feltételes hozzáférési szabályzatot, amely az MFA használatát igényli a nem megbízható hálózatokról való hozzáféréshez. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy ismeri a feltételes hozzáférés [alapvető fogalmait](overview.md) . 

## <a name="scenario-description"></a>Forgatókönyv leírása

A biztonság és a termelékenység egyensúlyának elsajátításához elegendő lehet a szervezet hálózatán lévő bejelentkezések jelszavának megkövetelése. A nem megbízható hálózati helyről való hozzáféréshez azonban nagyobb a kockázata, hogy a bejelentkezéseket nem a legitim felhasználók hajtják végre. A probléma megoldása érdekében letilthatja a nem megbízható hálózatokból való hozzáférést. Azt is megteheti, hogy többtényezős hitelesítést (MFA) is megkövetelheti, hogy további garanciát szerezzen a fiók jogos tulajdonosa által történt kísérletekhez. 

Az Azure AD feltételes hozzáférésével ezt a követelményt egyetlen, hozzáférést biztosító szabályzattal kezelheti: 

- A kiválasztott felhőalapú alkalmazások
- A kiválasztott felhasználók és csoportok esetében  
- Többtényezős hitelesítés megkövetelése 
- Ha a hozzáférés a következőkből származik: 
   - Nem megbízható hely

## <a name="implementation"></a>Implementálás

Ennek a forgatókönyvnek a feladata a nem *megbízható hálózati helyről való hozzáférés* feltételes hozzáférési feltételre való lefordítása. A feltételes hozzáférési házirendben beállíthatja a [helyszínek feltételét](location-condition.md) a hálózati telephelyekhez kapcsolódó forgatókönyvek kezeléséhez. A helyszínek feltétele lehetővé teszi az elnevezett helyszínek kiválasztását, amelyek az IP-címtartományok, országok és régiók logikai csoportjai.  

A szervezet általában egy vagy több címtartományt birtokol, például: 199.30.16.0-199.30.16.15.
Megnevezett helyet az alábbiak szerint állíthat be:

- A tartomány meghatározása (199.30.16.0/28) 
- Leíró név, például **vállalati hálózat** kiosztása 

Ahelyett, hogy az összes olyan helyet meg kellene határozni, amely nem megbízható, a következőket teheti:

- Bármely hely belefoglalása 

   :::image type="content" source="./media/untrusted-networks/02.png" alt-text="Képernyőkép az Azure A D-helyek panelről, a beállítás értéke Igen, az include (Belefoglalás) fül látható, a bármely kiválasztott és Kiemelt hely beállítás." border="false":::

- Az összes megbízható helyszín kizárása 

   :::image type="content" source="./media/untrusted-networks/01.png" alt-text="Képernyőkép az Azure A D-helyek panelről, a beállítás értéke Igen, a kizárás lap látható, és a minden megbízható helyek lehetőség ki van választva." border="false":::

## <a name="policy-deployment"></a>Szabályzat érvénybe léptetése

A jelen cikkben ismertetett megközelítéssel mostantól konfigurálhat feltételes hozzáférési szabályzatot nem megbízható helyszínekhez. Annak ellenőrzéséhez, hogy a házirend a várt módon működik-e, az ajánlott eljárás az, hogy tesztelje azt az éles üzemben való működés előtt. Ideális esetben egy tesztelési bérlő használatával ellenőrizheti, hogy az új szabályzat a kívánt módon működik-e.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a feltételes hozzáférésről, tekintse meg a [Mi a feltételes hozzáférés Azure Active Directory?](./overview.md) című témakört.
