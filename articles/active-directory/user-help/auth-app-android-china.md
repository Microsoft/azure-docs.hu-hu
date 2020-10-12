---
title: Az Android-eszközök rendelkezésre állásának és korlátozásának Microsoft Authenticator Kínában | Microsoft Docs
description: Ismerje meg, hogyan kérheti le a Microsoft Authenticator alkalmazás elérhetőségét Kínában
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/20/2020
ms.author: curtand
ms.openlocfilehash: 795c68fc063d98bdee6ccf59dba6ee718dc92d03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84323025"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>Microsoft Authenticator Androidhoz a nyilvános felhőben Kínában

Az Androidhoz készült Microsoft Authenticator alkalmazás letölthető Kínában. A Google Play Áruház Kínában nem érhető el, ezért az alkalmazást le kell tölteni a többi kínai alkalmazás-Piactérről. Az Androidhoz készült Microsoft Authenticator alkalmazás jelenleg a következő kínai áruházakban érhető el:

- [Baidu](https://shouji.baidu.com/software/26638379.html)
- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Samsung Galaxy áruház](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

Az alkalmazás legújabb buildje a Google Play Áruházban van, de a lehető leggyorsabban frissítjük az alkalmazást minden más alkalmazás-áruházban. Mivel az alkalmazás-áruházakban nincs telepítve egyéni Android-alkalmazáscsomag (APK), az alkalmazás zökkenőmentesen frissíthető az alábbi helyekről:

- A tároló, amelyről letöltött
- A Google Play Áruház, ha a felhasználó keresztezi a régiókat

## <a name="limitations"></a>Korlátozások

Az Androidhoz készült Microsoft Authenticator alkalmazás a Google Firebase felhőalapú üzenetkezelési rendszerét és a Google Play szolgáltatásokat használja a leküldéses értesítések fogadásához. Mivel egyik szolgáltatás sem érhető el Kínában, az alkalmazás funkcióinak bizonyos korlátai vannak:

- A hitelesítő alkalmazás többtényezős hitelesítési (MFA) metódusként való regisztrálása leküldéses értesítések használatával nem működik.

- [A telefonos bejelentkezés](../authentication/howto-authentication-sms-signin.md) nem állítható be. A felhasználónak a leküldéses értesítések használatával MFA-módszerként kell beállítania az alkalmazást, amely jelenleg nem működik.

Ha a felhasználó korábban már felügyelte a telefonos bejelentkezést vagy a többtényezős hitelesítést az alkalmazás használatával, manuálisan is megkeresheti az értesítési kérelmeket az alkalmazásban, és felhasználhatja az identitás-ellenőrzéshez.

## <a name="multi-factor-authentication-workaround"></a>A multi-Factor Authentication megkerülő megoldása

A többtényezős hitelesítéshez használt leküldéses értesítések használata helyett a felhasználók úgy [állíthatják be a hitelesítő](multi-factor-authentication-setup-auth-app.md#set-up-the-microsoft-authenticator-app-to-use-verification-codes) alkalmazásaikat, hogy hitelesítő kódokat kapjanak az eszközön, amelyet az MFA számára használhatnak az identitásuk ellenőrzéséhez. Ezek az ellenőrző kódok 30 másodpercig érvényesek, a rendszergazdáknak pedig lehetővé kell tenniük a bérlőnek, hogy az időalapú One-Time Password (TOTP) ellenőrző kódokat használják az ellenőrzés elvégzéséhez.

## <a name="availability"></a>Rendelkezésre állás

Microsoft Authenticator funkció | Rendelkezésre állás Kínában
------------------------------- | ---------------------
MFA-regisztráció leküldéses értesítések használatával | Nem
Előzetesen létező MFA-fiók, amely leküldéses értesítésekkel ellenőrzi az identitást | Nem
Előzetesen létező MFA-fiók az értesítések manuális vizsgálatával | Igen
MFA-regisztráció/hitelesítés csak TOTP/ellenőrző kódok használatával | Igen
Telefonos bejelentkezési regisztráció | Nem
Meglévő telefonos bejelentkezés leküldéses értesítések használatával | Nem
Meglévő telefonos bejelentkezési ellenőrzés a hitelesítési kérelmek kézi ellenőrzésének elvégzésével | Igen

## <a name="next-steps"></a>Következő lépések

- [Töltse le és telepítse a Microsoft Authenticator alkalmazást](user-help-auth-app-download-install.md)
