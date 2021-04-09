---
title: SCIM-végpont létrehozása a felhasználók Azure Active Directory való kiépítéséhez
description: Ismerje meg, hogyan fejleszthet SCIM-végpontokat, hogyan integrálhatja a SCIM API-t az Azure AD-vel, és hogyan hozhatja létre automatikusan a felhasználókat és csoportokat a Felhőbeli alkalmazásokba Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 4e933000c8e700d8bfd193b542e3855b2fca26f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689334"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Oktatóanyag: minta SCIM-végpont fejlesztése

Senki nem szeretne teljesen új végpontot létrehozni, ezért létrehoztunk egy [hivatkozási kódot](https://aka.ms/scimreferencecode) , amellyel megkezdheti a [rendszert a tartományok közötti Identitáskezelés (scim)](https://aka.ms/scimoverview)számára. A SCIM-végpontot legfeljebb öt perc alatt elérheti kód nélkül.

Ez az oktatóanyag leírja, hogyan helyezheti üzembe a SCIM hivatkozási kódját az Azure-ban, és hogyan tesztelheti a Poster használatával vagy a Azure Active Directory (Azure AD) SCIM-ügyféllel való integrálásával. Ez az oktatóanyag olyan fejlesztők számára készült, akik a SCIM vagy a SCIM-végpont teszteléséhez szeretnének kezdeni.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A SCIM-végpont üzembe helyezése az Azure-ban.
> * Tesztelje a SCIM-végpontot.

## <a name="deploy-your-scim-endpoint-in-azure"></a>A SCIM-végpont üzembe helyezése az Azure-ban

A következő lépésekkel telepítheti a SCIM-végpontot egy szolgáltatásba a [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) és a [Azure app Service](../../app-service/index.yml)használatával. A SCIM-hivatkozási kód helyileg futtatható, helyszíni kiszolgáló által üzemeltetett, vagy egy másik külső szolgáltatásban is üzembe helyezhető.

1. Nyissa meg a [hivatkozási kódot](https://github.com/AzureAD/SCIMReferenceCode) a githubon, és válassza a **klónozás vagy a letöltés** lehetőséget.

1. Válassza a **Megnyitás az asztalon** lehetőséget, vagy másolja a hivatkozást, nyissa meg a Visual studiót, és válassza a **klónozás vagy a kód kijelentkezése** lehetőséget a másolt hivatkozás megadásához, és készítsen helyi másolatot.

1. A Visual Studióban ellenőrizze, hogy be van-e jelentkezni az üzemeltetési erőforrásaihoz hozzáféréssel rendelkező fiókba.

1. A Megoldáskezelő nyissa meg a *Microsoft. scim. SLN* fájlt, és kattintson a jobb gombbal a *Microsoft. scim. WebHostSample* fájlra. Válassza a **Közzététel** lehetőséget.

    ![Képernyőkép, amely megjeleníti a minta fájlt.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > A megoldás helyi futtatásához kattintson duplán a projektre, és válassza a **IIS Express** lehetőséget a projekt elindításához a helyi gazdagép URL-címét tartalmazó weblapként.

1. Válassza a **profil létrehozása** lehetőséget, és győződjön meg arról, hogy a **app Service** és az **új létrehozása** lehetőség ki van választva.

    ![A közzétételi ablakot megjelenítő képernyőkép.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Lépjen át a párbeszédpanel beállításai között, és nevezze át az alkalmazást egy tetszőleges névre. Ezt a nevet az alkalmazás és a SCIM végpont URL-címe is használja.

    ![Képernyőkép, amely egy új App Service létrehozását mutatja be.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Válassza ki a használni kívánt erőforráscsoportot, és válassza a **Közzététel** lehetőséget.

    ![Az új App Service közzétételét bemutató képernyőkép.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-4.png)

1. Nyissa meg az alkalmazást **Azure app Service**  >  **konfigurációban** , és válassza az **új Alkalmazásbeállítás** lehetőséget a *Token__TokenIssuer* beállítás értékkel való hozzáadásához `https://sts.windows.net/<tenant_id>/` . Cserélje le `<tenant_id>` az-t az Azure ad-bérlői azonosítóra. Ha az SCIM-végpontot [Poster](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)használatával szeretné tesztelni, adjon hozzá egy *ASPNETCORE_ENVIRONMENT* beállítást az értékkel `Development` .

   ![Képernyőkép, amely az Alkalmazásbeállítások ablakot jeleníti meg.](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Ha a [Azure Portal](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client)vállalati alkalmazással teszteli a végpontot, két lehetőség közül választhat. Megtarthatja a környezetet a-ben, és megadhatja a `Development` tesztelési tokent a `/scim/token` végponttól, vagy megváltoztathatja a környezetet, `Production` és a jogkivonat mezőt üresen hagyhatja.

Ennyi az egész! A SCIM-végpont már közzé van téve, és a Azure App Service URL-cím segítségével tesztelheti az SCIM-végpontot.

## <a name="test-your-scim-endpoint"></a>SCIM-végpont tesztelése

Az SCIM-végpontra irányuló kérések hitelesítésre van szükség. A SCIM standard több lehetőséget kínál a hitelesítéshez és az engedélyezéshez, beleértve a cookie-kat, az alapszintű hitelesítést, a TLS-ügyfél hitelesítését vagy az [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2)-ben felsorolt módszerek bármelyikét.

Ügyeljen arra, hogy elkerüljék a nem biztonságos metódusokat, például a felhasználónevet és a jelszót a biztonságosabb módszer (például a OAuth) mellett. Az Azure AD támogatja a hosszú élettartamú tulajdonosi jogkivonatokat (a katalógusok és a nem katalógusos alkalmazások esetében) és a OAuth engedélyezési támogatását (a Gallery-alkalmazásokhoz).

> [!NOTE]
> A tárházban megadott engedélyezési módszerek kizárólag tesztelésre szolgálnak. Az Azure AD-vel való integráció során áttekintheti az engedélyezési útmutatót. Lásd: [scim-végpont létesítésének tervezése](use-scim-to-provision-users-and-groups.md).

A fejlesztési környezet lehetővé teszi, hogy az éles környezetben nem biztonságos szolgáltatások, például a hivatkozási kód a biztonsági jogkivonat érvényesítésének viselkedését szabályozza. A jogkivonat-ellenőrzési kód önaláírt biztonsági jogkivonatot használ, és az aláíró kulcsot a konfigurációs fájlban tárolja a rendszer. Tekintse meg a **token: IssuerSigningKey** paramétert a fájl *appsettings.Development.jsjában* .

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Ha **Get** -kérést küld a `/scim/token` végpontnak, a rendszer egy jogkivonatot állít ki a konfigurált kulccsal. Ezt a tokent tulajdonosi jogkivonatként használhatja a későbbi engedélyezéshez.

Az alapértelmezett jogkivonat-érvényesítési kód Azure AD-jogkivonat használatára van konfigurálva, és a kiállító bérlőt a fájlappsettings.js **jogkivonat: TokenIssuer** paraméterével kell  konfigurálni.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>A Poster használata a végpontok teszteléséhez

Az SCIM-végpont üzembe helyezése után ellenőrizheti, hogy az megfelel-e a SCIM RFC-nek. Ez a példa olyan teszteket biztosít a Poster-ban, amelyek ellenőrzik a szifilisz (létrehozás, olvasás, frissítés és törlés) műveleteit a felhasználók és csoportok számára, a szűrést, a csoporttagság frissítését és a felhasználók letiltását.

A végpontok a `{host}/scim/` címtárban találhatók, és szabványos http-kérésekkel is használhatók a velük való kommunikációhoz. Az útvonal módosításához `/scim/` lásd: *ControllerConstant. cs* a **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**-  >  **vezérlőkben**.

> [!NOTE]
> A helyi tesztekhez csak HTTP-végpontokat használhat. Az Azure AD kiépítési szolgáltatásához a végpontnak támogatnia kell a HTTPS-t.

1. Töltse le a [Poster](https://www.getpostman.com/downloads/) -t, és indítsa el az alkalmazást.
1. Másolja és illessze be ezt a hivatkozást a Poster-ba a tesztelési gyűjtemény importálásához: `https://aka.ms/ProvisioningPostman` .

    ![Képernyőkép, amely a tesztelési gyűjtemény importálását mutatja be a Poster-ben.](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Hozzon létre egy tesztkörnyezetben, amely a következő változókkal rendelkezik:

   |Környezet|Változó|Érték|
   |-|-|-|
   |A projekt helyi futtatása IIS Express használatával|||
   ||**Kiszolgáló**|`localhost`|
   ||**Port**|`:44359`*(ne feledkezzen meg a **`:`** )*|
   ||**API**|`scim`|
   |A projekt helyi futtatása a vércse használatával|||
   ||**Kiszolgáló**|`localhost`|
   ||**Port**|`:5001`*(ne feledkezzen meg a **`:`** )*|
   ||**API**|`scim`|
   |A végpont üzemeltetése az Azure-ban|||
   ||**Kiszolgáló**|*(adja meg a SCIM URL-címét)*|
   ||**Port**|*(hagyja üresen)*|
   ||**API**|`scim`|

1. A Poster gyűjtemény **kulcsának beolvasása** parancsával küldjön le egy **Get** -kérést a jogkivonat-végpontnak, és kérje le a **jogkivonat** -változóban tárolni kívánt biztonsági jogkivonatot a további kérésekhez.

   ![A Poster Get Key mappáját bemutató képernyőkép.](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Ahhoz, hogy egy SCIM-végpont biztonságos legyen, szüksége lesz egy biztonsági jogkivonatra a kapcsolódás előtt. Az oktatóanyag a `{host}/scim/token` végpontot használja egy önaláírt jogkivonat létrehozásához.

Ennyi az egész! Most már futtathatja a **Poster** -gyűjteményt a scim-végpont funkciójának teszteléséhez.

## <a name="next-steps"></a>Következő lépések

Ha egy SCIM-kompatibilis felhasználói és csoportos végpontot szeretne létrehozni egy ügyfél együttműködési képességével, tekintse meg a [scim-ügyfél implementációja](http://www.simplecloud.info/#Implementations2)című témakört.

> [!div class="nextstepaction"]
> [Oktatóanyag: scim-végpont](use-scim-to-provision-users-and-groups.md) 
>  létesítésének fejlesztése és tervezése [Oktatóanyag: a kiépítés konfigurálása egy Gallery-alkalmazáshoz](configure-automatic-user-provisioning-portal.md)