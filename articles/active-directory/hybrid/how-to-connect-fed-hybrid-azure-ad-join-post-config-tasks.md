---
title: 'Azure AD Connect: hibrid Azure AD JOIN post konfigurációs feladatok | Microsoft Docs'
description: Ez a dokumentum részletesen ismerteti a hibrid Azure AD-csatlakozás befejezéséhez szükséges konfigurációs feladatokat.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da5cefbacbd3851d2609a687c1948d9bcba5ffae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88612469"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Hibrid Azure AD-csatlakozás konfigurálása utáni feladatok

Miután futtatta a Azure AD Connectt a hibrid Azure AD-csatlakozáshoz való konfigurálásához, néhány további lépést is végre kell hajtania a beállítás véglegesítéséhez.  Csak azokat a lépéseket hajtsa végre, amelyek alkalmazhatók az eszközökre.

## <a name="1-configure-controlled-rollout-optional"></a>1. szabályozott bevezetés konfigurálása (nem kötelező)
A Windows 10 és a Windows Server 2016 rendszerű összes tartományhoz csatlakoztatott eszköz automatikusan regisztrálja az Azure AD-t az összes konfigurációs lépés befejezése után. Ha az automatikus regisztráció helyett inkább az ellenőrzött bevezetést részesíti előnyben, a csoportházirend segítségével szelektív módon engedélyezheti vagy letilthatja az automatikus bevezetést.  Ezt a csoportházirendet a további konfigurációs lépések megkezdése előtt be kell állítani:
* Hozzon létre egy csoportházirend-objektumot a Active Directory.
* Nevezze el (a hibrid Azure AD-csatlakozás).
* Szerkesztés és ugrás: Számítógép konfigurációja > házirendek > Felügyeleti sablonok > Windows-összetevők > eszköz regisztrálása.

>[!NOTE]
>A 2012R2 a **Számítógép konfigurációja > házirendek > Felügyeleti sablonok > Windows-összetevők > Workplace JOIN > automatikusan munkahelyi csatlakozás ügyfélszámítógépeken**

* A beállítás engedélyezése: tartományhoz csatlakoztatott számítógépek regisztrálása eszközként.
* Alkalmazza, és kattintson az OK gombra.
* Csatolja a GPO-t az Ön által választott helyhez (szervezeti egység, biztonsági csoport vagy a tartományhoz az összes eszköz esetében).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. a hálózat konfigurálása az eszköz regisztrációs végpontokkal
Győződjön meg arról, hogy az alábbi URL-címek elérhetők a szervezeti hálózaton belüli számítógépekről az Azure AD-ba való regisztráláshoz:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com` 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. WPAD implementálása Windows 10-es eszközökhöz
Ha a szervezet kimenő proxyn keresztül éri el az internetet, implementálja a webproxy automatikus felderítését (WPAD) a Windows 10-es számítógépek Azure AD-ba való regisztrálásának engedélyezéséhez.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. konfigurálja az SCP-t bármely olyan erdőben, amelyet nem Azure AD Connect konfiguráltak 

A szolgáltatáskapcsolódási pont (SCP) tartalmazza az Azure AD-bérlői adatait, amelyeket az eszközök az automatikus regisztrációhoz fognak használni.  Futtassa a Azure AD Connectból letöltött PowerShell-parancsfájlt ConfigureSCP.ps1.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. konfigurálja a Azure AD Connect által nem konfigurált összevonási szolgáltatásokat

Ha a szervezete összevonási szolgáltatást használ az Azure AD-be való bejelentkezéshez, az Azure AD függő entitás megbízhatóságának jogcím-szabályainak engedélyeznie kell az eszköz hitelesítését. Ha AD FS-vel való összevonást használ, lépjen a [AD FS súgóba](https://aka.ms/aadrptclaimrules) a jogcím-szabályok létrehozásához. Ha nem a Microsofttól származó összevonási megoldást használ, útmutatásért forduljon a szolgáltatóhoz.  

>[!NOTE]
>Ha a Windows leállási szintű eszközei vannak, a szolgáltatásnak támogatnia kell a AuthenticationMethod és a wiaormultiauthn jogcímek kiállítását az Azure AD-megbízhatóságra irányuló kérések fogadásakor. AD FS egy olyan kiállítási átalakítási szabályt kell hozzáadnia, amely áthalad a hitelesítési módszeren.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. az Azure AD zökkenőmentes egyszeri bejelentkezésének engedélyezése a Windows rendszerű eszközökön

Ha a szervezete jelszó-kivonatoló szinkronizálást vagy átmenő hitelesítést használ az Azure AD-be való bejelentkezéshez, engedélyezze az Azure AD zökkenőmentes SSO-t a bejelentkezési módszerrel a Windows Down-szintű eszközök hitelesítéséhez:  https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso . 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Állítsa be az Azure AD-szabályzatot a Windows rendszerű eszközökön.

A Windows Down szintű eszközök regisztrálásához meg kell győződnie arról, hogy az Azure AD-házirend lehetővé teszi a felhasználóknak az eszközök regisztrálását. 

* Jelentkezzen be a fiókjába a Azure Portal.
* Ugrás: Azure Active Directory > eszközök > eszközbeállítások
* A "felhasználók regisztrálhatják eszközeiket az Azure AD-ben" értékre.
* Kattintson a Save (Mentés) gombra.

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Azure AD-végpont hozzáadása a Windows régebbi eszközökhöz

Adja hozzá az Azure AD-alapú hitelesítési végpontot a Windows Down-szintű eszközök helyi intranetes zónájához, hogy elkerülje a tanúsítvány kérését az eszközök hitelesítése során: `https://device.login.microsoftonline.com` 

Ha [zökkenőmentes SSO](how-to-connect-sso.md)-t használ, a zónában engedélyezze az "állapotjelző sáv frissítéseinek engedélyezése" lehetőséget is, és adja hozzá a következő végpontot: `https://autologon.microsoftazuread-sso.com` 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Telepítse a Microsoft Workplace Joint a Windows rendszerű eszközökön.

Ez a telepítő egy ütemezett feladatot hoz létre a felhasználó környezetében futó eszköz rendszeren. A feladat akkor aktiválódik, amikor a felhasználó bejelentkezik a Windowsba. A feladat csendesen csatlakoztatja az eszközt az Azure AD-vel a felhasználói hitelesítő adatokkal az integrált Windows-hitelesítés használatával végzett hitelesítés után. A letöltőközpontban a következő címen érhető el: https://www.microsoft.com/download/details.aspx?id=53554 . 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. konfigurálja a csoportházirendet az eszközök regisztrálásának engedélyezéséhez

További információ a hibrid Azure AD JOIN egyéni eszközökhöz való engedélyezéséről: [hibrid Azure ad-csatlakozás vezérelt](../devices/hybrid-azuread-join-control.md)ellenőrzése.

## <a name="next-steps"></a>Következő lépések
[Az eszköz visszaírási konfigurálása](how-to-connect-device-writeback.md)
