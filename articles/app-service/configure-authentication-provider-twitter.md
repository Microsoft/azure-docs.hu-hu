---
title: Twitter-hitelesítés konfigurálása
description: Megtudhatja, hogyan konfigurálhatja a Twitter-hitelesítést identitás-szolgáltatóként a App Service-alkalmazáshoz.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom: seodec18
ms.openlocfilehash: 794f671b36b5aeb9f19cf5d80e488500cedb1098
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207139"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>A App Service alkalmazás konfigurálása a Twitter-bejelentkezés használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja a Azure App Servicet a Twitter hitelesítési szolgáltatóként való használatához.

A cikkben ismertetett eljárás végrehajtásához olyan Twitter-fiókra van szükség, amely ellenőrzött e-mail-címmel és telefonszámmal rendelkezik. Új Twitter-fiók létrehozásához nyissa meg a [Twitter.com].

## <a name="register"> </a>Alkalmazás regisztrálása a Twitteren

1. Jelentkezzen be a [Azure Portalra] , és lépjen az alkalmazáshoz. Másolja az **URL-címet**. Ezt fogja használni a Twitter-alkalmazás konfigurálásához.
1. Lépjen a [Twitter-fejlesztők] webhelyére, jelentkezzen be a Twitter-fiókja hitelesítő adataival, és válassza az **alkalmazás létrehozása**lehetőséget.
1. Adja meg az alkalmazás **nevét** és **leírását** az új alkalmazáshoz. Illessze be az alkalmazás **URL-címét** a **webhely URL-** címe mezőbe. A **visszahívási URL-címek** szakaszban adja meg app Service alkalmazásának HTTPS URL-címét, és fűzze hozzá az elérési utat `/.auth/login/twitter/callback`. Például: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. A lap alján írja be a legalább 100 karaktert a **mondja el, hogyan használja ezt az alkalmazást**, majd válassza a **Létrehozás**lehetőséget. Az előugró ablakban kattintson az újbóli **Létrehozás** gombra. Megjelenik az alkalmazás részletei.
1. Válassza a **kulcsok és hozzáférési tokenek** fület.

   Jegyezze fel ezeket az értékeket:
   - API-kulcs
   - API titkos kulcsa

   > [!NOTE]
   > Az API titkos kulcsa fontos biztonsági hitelesítő adat. Ezt a titkos kódot Ne ossza meg senkivel, vagy ossza meg az alkalmazásával.

## <a name="secrets"> </a>Twitter-információk hozzáadása az alkalmazáshoz

1. Nyissa meg az alkalmazást a [Azure Portalra].
1. Válassza a **beállítások** > a **hitelesítés/engedélyezés**lehetőséget, és győződjön meg arról, hogy a **app Service hitelesítés** **be van kapcsolva**.
1. Válassza a **Twitter**lehetőséget.
1. Illessze be a korábban beszerzett `API key` és `API secret key` értékeket.
1. Kattintson az **OK** gombra.

   ![Képernyőkép a Mobile App Twitter-beállításairól][1]

   Alapértelmezés szerint a App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. Engedélyezni kell a felhasználókat az alkalmazás kódjában.

1. Választható Ha a webhelyhez való hozzáférést csak a Twitter által hitelesített felhasználókra szeretné korlátozni, állítsa be a **végrehajtandó műveletet, ha a kérés nincs hitelesítve** a **Twitteren**. Ha beállítja ezt a funkciót, az alkalmazásnak minden kérelmet hitelesítenie kell. Emellett az összes nem hitelesített kérelmet is átirányítja a Twitterre a hitelesítéshez.

   > [!CAUTION]
   > A hozzáférés ily módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos olyan alkalmazások esetében, amelyek nyilvánosan elérhető kezdőlaptal rendelkeznek, mint sok egyoldalas alkalmazásban. Ilyen alkalmazások esetén **engedélyezze a névtelen kérelmeket (nincs művelet)** előnyben részesített, hogy az alkalmazás manuálisan megkezdse a hitelesítést. További információ: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow).

1. Kattintson a **Mentés** gombra.

Most már készen áll a Twitter használatára a hitelesítéshez az alkalmazásban.

## <a name="related-content"> </a>További lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter-fejlesztők]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portalra]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
