---
title: Azure multi-Factor Auth-szolgáltatók – Azure Active Directory
description: Mikor érdemes hitelesítési szolgáltatót használni az Azure MFA-val?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c8454d2ca83d4f406149e7eb73feb19ce59554f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96744125"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Mikor kell Azure Multi-Factor Authentication szolgáltatót használni

> [!IMPORTANT]
> Az új hitelesítési szolgáltatók már nem hozhatók létre az 2018. szeptember 1-től érvényesek. A meglévő hitelesítési szolgáltatók továbbra is használhatók és frissíthetők, de a Migrálás már nem lehetséges. A többtényezős hitelesítés továbbra is elérhető lesz prémium szintű Azure AD licencek szolgáltatásként.

A kétlépéses ellenőrzés alapértelmezés szerint a Azure Active Directory-t használó globális rendszergazdák számára érhető el, és Microsoft 365 felhasználókat. De ha ki szeretné használni a [speciális szolgáltatásokat](howto-mfa-mfasettings.md), az Azure Multi-Factor Authentication (MFA) teljes verzióját meg kell vásárolnia.

Az Azure multi-Factor Auth szolgáltató az Azure-Multi-Factor Authentication által nyújtott szolgáltatások kihasználására szolgál azon felhasználók számára, akik **nem rendelkeznek licenccel**.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Az Azure MFA SDK-val kapcsolatos kikötések

Megjegyzés: az SDK elavult, és csak 2018. november 14-én fog működni. Ezután az SDK felé indított hívások meghiúsulnak.

## <a name="what-is-an-mfa-provider"></a>Mi az az MFA-szolgáltató?

Kétféle hitelesítési szolgáltató létezik, és a különbség az Azure-előfizetés díja. A hitelesítésenkénti lehetőség választásakor a rendszer kiszámítja a bérlőn havonta végzett hitelesítések számát. Ezt a lehetőséget akkor érdemes használni, ha néhány felhasználó csak alkalmanként végez hitelesítést. A felhasználónkénti lehetőség választásakor a rendszer kiszámítja a bérlőn egy hónap alatt kétlépéses ellenőrzést végző egyének számát. Ez a lehetőség akkor a legjobb, ha van néhány licenccel rendelkező felhasználója, de ki kell terjesztenie az MFA-t a licenckorlátján túl több felhasználóra.

## <a name="manage-your-mfa-provider"></a>Az MFA-szolgáltató kezelése

Az MFA szolgáltató létrehozását követően már nem módosíthatja a használati modellt (engedélyezett felhasználónként vagy hitelesítésenként).

Ha elegendő licencet vásárolt az MFA-ra engedélyezett összes felhasználó lefedéséhez, akkor az MFA-szolgáltatót teljes egészében törölheti.

Ha az MFA szolgáltató nincs Azure AD-bérlőhöz kapcsolva, vagy új MFA szolgáltatót kapcsol egy másik Azure AD-bérlőhöz, a felhasználói és konfigurációs beállításokat a rendszer nem viszi át. Emellett a meglévő Azure MFA-kiszolgálókat újra kell aktiválni az MFA-szolgáltatón keresztül generált aktiválási hitelesítő adatok használatával.

### <a name="removing-an-authentication-provider"></a>Hitelesítési szolgáltató eltávolítása

> [!CAUTION]
> A hitelesítési szolgáltató törlésekor nincs megerősítés. A **Törlés** lehetőség kiválasztásával állandó folyamat van.

A hitelesítő szolgáltatók a **Azure Portal**  >  **Azure Active Directory**  >  **biztonsági**  >  **MFA**-  >  **szolgáltatók** között találhatók. Kattintson a felsorolt szolgáltatók lehetőségre, hogy megtekintse a szolgáltatóhoz társított részleteket és konfigurációkat.

A hitelesítési szolgáltató eltávolítása előtt jegyezze fel a szolgáltatóban konfigurált testreszabott beállításokat. Döntse el, hogy mely beállításokat kell áttelepíteni a szolgáltató általános MFA-beállításaiba, és el kell végeznie ezeknek a beállításoknak az áttelepítését. 

A szolgáltatóknak kapcsolódó Azure MFA-kiszolgálókat újra kell aktiválni a **Azure Portal**  >  **Azure Active Directory**  >  **biztonsági**  >  **MFA**-  >  **kiszolgáló beállításai** között létrehozott hitelesítő adatokkal. Az újraaktiválás előtt a következő fájlokat törölni kell a `\Program Files\Multi-Factor Authentication Server\Data\` címtárból az Azure MFA-kiszolgálókon a környezetben:

- caCert
- tanúsítvány
- groupCACert
- groupKey
- groupName
- Következő licenckulcs
- pkey

![Hitelesítési szolgáltató törlése a Azure Portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Ha meggyőződött arról, hogy az összes beállítás át lett telepítve, tallózással keresse meg a **Azure Portal**  >  **Azure Active Directory**  >  **biztonsági**  >  **MFA**  >  -**szolgáltatót** , és válassza a három pontot **...** és válassza a **Törlés** lehetőséget.

> [!WARNING]
> A hitelesítésszolgáltató törlése törli a szolgáltatóhoz társított összes jelentési információt. Előfordulhat, hogy a szolgáltató törlése előtt el szeretné menteni a tevékenységek jelentéseit.

> [!NOTE]
> Előfordulhat, hogy a Microsoft Authenticator alkalmazás és az Azure MFA-kiszolgáló régebbi verzióit használó felhasználóknak újra regisztrálniuk kell az alkalmazást.

## <a name="next-steps"></a>Következő lépések

[A Multi-Factor Authentication beállításainak konfigurálása](howto-mfa-mfasettings.md)
