---
title: Fiókok és hitelesítő adatok megosztása – Azure Active Directory | Microsoft Docs
description: Leírja, hogy Azure Active Directory lehetővé teszi a szervezetek számára a helyszíni alkalmazások és a fogyasztói Cloud Services fiókjainak biztonságos megosztását.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/03/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb088d56879ebdf5d439c913ac47a701db5c4a60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576247"
---
# <a name="sharing-accounts-with-azure-ad"></a>Fiókok megosztása az Azure AD-vel

## <a name="overview"></a>Áttekintés

Néha a szervezeteknek egyetlen felhasználónevet és jelszót kell használniuk több személy számára, ami általában két esetben fordul elő:

* Ha olyan alkalmazásokhoz fér hozzá, amelyek egyedi bejelentkezést és jelszót igényelnek minden felhasználó számára, akár helyszíni alkalmazások, akár fogyasztói felhőalapú szolgáltatások (például vállalati közösségi média-fiókok).
* Többfelhasználós környezetek létrehozásakor. Lehet, hogy egyetlen, helyi fiókkal rendelkezik, amely emelt szintű jogosultságokkal rendelkezik, és alapszintű telepítési, felügyeleti és helyreállítási tevékenységeket végez. Például a helyi "globális rendszergazda" fiók Microsoft 365 vagy a Salesforce-ben a root fiókhoz.

Ezek a fiókok hagyományosan a hitelesítő adatok (Felhasználónév és jelszó) elosztásával vannak megosztva a megfelelő személyeknek, vagy megosztott helyen tárolják őket, ahol több megbízható ügynök férhet hozzájuk.

A hagyományos megosztási modell több hátránya is van:

* Az új alkalmazásokhoz való hozzáférés engedélyezése megköveteli a hitelesítő adatok terjesztését mindenki számára, akinek hozzáférésre van szüksége.
* Minden megosztott alkalmazáshoz szükség lehet a megosztott hitelesítő adatok egyedi készletére, amely megköveteli, hogy a felhasználók több hitelesítő adatot jegyezzenek be. Ha a felhasználóknak sok hitelesítő adatot kell megemlékezniük, a kockázat növeli a kockázatos eljárásokat. (például a jelszavak leírása).
* Nem állapítható meg, hogy ki férhet hozzá egy alkalmazáshoz.
* Nem állapítható meg, hogy ki *férhet hozzá* egy alkalmazáshoz.
* Ha el szeretné távolítani az alkalmazáshoz való hozzáférést, frissítenie kell a hitelesítő adatokat, és el kell juttatnia azokat mindenki számára, akinek hozzá kell férnie az adott alkalmazáshoz.

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory fiókok megosztása

Az Azure AD egy új megközelítést biztosít a megosztott fiókok használatához, amelyek kiküszöbölik ezeket a hátrányokat.

Az Azure AD-rendszergazda konfigurálja, hogy mely alkalmazások férhetnek hozzá a felhasználóhoz a hozzáférési panelen, és kiválaszthatja az adott alkalmazáshoz legmegfelelőbb egyszeri bejelentkezés típusát. Az egyik ilyen típus, a *jelszó-alapú egyszeri bejelentkezés*, lehetővé teszi, hogy az Azure ad az alkalmazás bejelentkezési folyamata során "közvetítőként" működjön.

A felhasználók a szervezeti fiókjával egyszer jelentkeznek be. Ez a fiók ugyanaz, mint amit rendszeresen használ az asztali vagy e-mail-hozzáféréshez. Csak azokat az alkalmazásokat tudják felderíteni és elérni, amelyekhez hozzá vannak rendelve. A megosztott fiókok esetében az alkalmazások listája tetszőleges számú megosztott hitelesítő adatot tartalmazhat. A végfelhasználónak nem kell megemlékeznie vagy leírnia azokat a különböző fiókokat, amelyeket érdemes lehet használni.

A megosztott fiókok nemcsak növelik a felügyeleti és a használhatóságot, hanem javítják a biztonságot. A hitelesítő adatok használatára vonatkozó engedélyekkel rendelkező felhasználók nem látják a megosztott jelszót, hanem lekérik az engedélyeket, hogy egy előkészített hitelesítési folyamat részeként használják a jelszót. Emellett egyes jelszó-SSO-alkalmazások lehetővé teszi, hogy az Azure AD-vel rendszeres időközönként felváltási (frissítési) jelszavakat használjanak. A rendszer nagyméretű, összetett jelszavakat használ, ami növeli a fiókok biztonságát. A rendszergazda egyszerűen engedélyezheti vagy visszavonhatja a hozzáférést egy alkalmazáshoz, tudja, hogy ki férhet hozzá a fiókhoz, és hogy ki is fér hozzá a múltban.

Az Azure AD támogatja a megosztott fiókokat bármely nagyvállalati mobilitási csomag (EMS) vagy prémium szintű Azure AD licencelési csomag esetében, az összes jelszó-egyszeri bejelentkezési alkalmazás esetében. Az alkalmazás-katalógusban több ezer előre integrált alkalmazás fiókját is megoszthatja, és hozzáadhat saját jelszavas hitelesítést használó alkalmazást [Egyéni SSO](../manage-apps/what-is-single-sign-on.md)-alkalmazásokhoz.

A fiókok megosztását engedélyező Azure AD-funkciók a következők:

* [Jelszó egyszeri bejelentkezés](../manage-apps/sso-options.md#password-based-sso)
* Egyszeri bejelentkezési ügynök jelszava
* [Csoport-hozzárendelés](groups-self-service-management.md)
* Egyéni jelszavas alkalmazások
* [Alkalmazás-használati irányítópult/jelentések](../authentication/howto-sspr-reporting.md)
* Végfelhasználói hozzáférési portálok
* [Alkalmazásproxy](../manage-apps/application-proxy.md)
* [Active Directory piactér](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory)

## <a name="sharing-an-account"></a>Fiók megosztása

Ha az Azure AD-t egy fiók megosztására szeretné használni, a következőket kell tennie:

* Alkalmazás- [Képtár](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) vagy [egyéni alkalmazás](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/) hozzáadása
* Az alkalmazás konfigurálása jelszó-Sign-On (SSO)
* Használjon [csoportos hozzárendelést](groups-saasapps.md) , és válassza a megosztott hitelesítő adatok megadására szolgáló lehetőséget.

A megosztott fiókot Multi-Factor Authentication (MFA) biztonságosabbá teheti (További információ az [alkalmazások az Azure ad-vel való biztonságossá](../authentication/concept-mfa-howitworks.md)tételéről), és delegálhatja azt a képességet, hogy az [Azure ad](groups-self-service-management.md) önkiszolgáló csoport kezelésével Hogyan férhet hozzá az alkalmazáshoz.

## <a name="next-steps"></a>Következő lépések

* [Alkalmazáskezelés az Azure Active Directory használatával](../manage-apps/what-is-application-management.md)
* [Alkalmazások védelme feltételes hozzáféréssel](../../active-directory-b2c/overview.md)
* [Önkiszolgáló csoport kezelése/SSAA](groups-self-service-management.md)