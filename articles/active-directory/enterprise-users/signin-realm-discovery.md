---
title: Felhasználónév-keresés a bejelentkezéskor – Azure Active Directory | Microsoft Docs
description: A bejelentkezéskor a képernyőn megjelenő üzenetkezelés a Felhasználónév-keresést tükrözi Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: overview
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ec804029919f0b9c4be26b044cd7a3eeddf6690
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547831"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>A Azure Active Directory bejelentkezési oldalain a Kezdőlap tartományának felderítése

A Azure Active Directory (Azure AD) bejelentkezési viselkedését módosítjuk, hogy helyet szabadítson fel az új hitelesítési módszerek és a használhatóság javítása érdekében. A bejelentkezés során az Azure AD meghatározza, hogy a felhasználónak hol kell hitelesítenie magát. Az Azure AD intelligens döntéseket tesz a bejelentkezési oldalon megadott Felhasználónév szervezeti és felhasználói beállításainak beolvasásával. Ez egy olyan, a jelszó-mentes jövő felé irányuló lépés, amely lehetővé teszi a további hitelesítő adatokat, például a következőt: 2,0.

## <a name="home-realm-discovery-behavior"></a>A Kezdőlap tartományának felderítési viselkedése

A Kezdőlap tartomány felderítését a rendszer a bejelentkezéskor vagy egy örökölt alkalmazásokhoz tartozó Home Realm-felderítési szabályzatban megadott tartomány szabályozza. A felderítési viselkedésben például egy Azure Active Directory felhasználó írhatja be a felhasználónevét, de továbbra is a szervezete hitelesítőadat-gyűjtési képernyőjén fog megérkezni. Ez akkor fordul elő, ha a felhasználó helyesen adja meg a szervezet "contoso.com" tartománynevét. Ez a viselkedés nem teszi lehetővé a részletességet az egyes felhasználók élményének testreszabásához.

A hitelesítő adatok szélesebb körének és a használhatóság növelésének támogatásához a bejelentkezési folyamat során Azure Active Directory Felhasználónév-keresési viselkedése frissül. Az új viselkedés intelligens döntéseket tesz azáltal, hogy a bejelentkezési oldalon megadott Felhasználónév alapján szervezeti szintű és felhasználói szintű beállításokat olvas be. Ennek lehetővé tételéhez Azure Active Directory ellenőrizze, hogy a bejelentkezési oldalon megadott felhasználónév létezik-e a megadott tartományban, vagy átirányítja a felhasználót a hitelesítő adatok megadásához.

A munka további előnye, hogy javul a hibaüzenetek. Íme néhány példa a továbbfejlesztett hibaüzenetekre, amikor olyan alkalmazásba jelentkezik be, amely csak Azure Active Directory felhasználókat támogat.

- A Felhasználónév típusa helytelen, vagy a Felhasználónév még nem lett szinkronizálva az Azure AD-vel:
  
    ![a Felhasználónév típusa helytelen, vagy nem található](./media/signin-realm-discovery/typo-username.png)
  
- A tartománynév a következő:
  
    ![a tartománynév helytelenül van begépelve, vagy nem található.](./media/signin-realm-discovery/typo-domain.png)
  
- A felhasználó egy ismert fogyasztói tartománnyal próbál bejelentkezni:
  
    ![Bejelentkezés ismert fogyasztói tartománnyal](./media/signin-realm-discovery/consumer-domain.png)
  
- A jelszó hibásan van begépelve, de a Felhasználónév pontos:  
  
    ![a jelszó típusa jó felhasználónévvel van ellátva](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Ez a funkció hatással lehet az összevont tartományokra, amelyek a régi tartományi szintű Kezdőlap-felderítésre támaszkodnak az összevonás kényszerítése érdekében. Az összevont tartományi támogatással kapcsolatos frissítések hozzáadásához tekintse meg a [Kezdőlap tartomány felderítése Microsoft 365 szolgáltatások bejelentkezését](https://azure.microsoft.com/updates/signin-hrd/)ismertető témakört. Addig is előfordulhat, hogy egyes szervezetek az alkalmazottakat olyan felhasználónévvel jelentkeznek be, amely nem szerepel a Azure Active Directoryban, de a megfelelő tartománynevet tartalmazza, mert a tartománynevek jelenleg a szervezet tartományi végpontja felé irányítják a felhasználókat. Az új bejelentkezési viselkedés nem teszi lehetővé ezt. A felhasználó értesítést kap a Felhasználónév kijavítani, és nem jogosult olyan felhasználónévvel való bejelentkezésre, amely nem szerepel a Azure Active Directoryban.
>
> Ha Ön vagy a szervezete olyan gyakorlattal rendelkezik, amelyek a régi viselkedéstől függenek, akkor fontos, hogy a szervezeti rendszergazdák frissíteni tudják az alkalmazottak bejelentkezési és hitelesítési dokumentációját, valamint hogy betanítsák az alkalmazottakat Azure Active Directory felhasználónevét a bejelentkezéshez.
  
Ha az új viselkedéssel kapcsolatos problémái vannak, hagyja meg a megjegyzéseit a jelen cikk **visszajelzések** szakaszában.  

## <a name="next-steps"></a>Következő lépések

[A bejelentkezési arculat testreszabása](../fundamentals/add-custom-domain.md)
