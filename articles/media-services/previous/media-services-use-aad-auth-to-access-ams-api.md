---
title: Hozzáférés a Azure Media Services API-hoz Azure Active Directory hitelesítéssel | Microsoft Docs
description: Tudnivalók a Azure Media Services API-hoz való hozzáférés hitelesítéséhez Azure Active Directory (Azure AD) használatához szükséges fogalmakról és lépésekről.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 3688f0f9266844ce584f3f2d2e1a7c39ed3e4f70
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009014"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Az Azure Media Services API hozzáférése Azure AD-hitelesítéssel 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

A Azure Media Services API egy REST API. A használatával REST API vagy az elérhető ügyféloldali SDK-k használatával végezhet műveleteket a média erőforrásain. A Azure Media Services Media Services ügyféloldali SDK-t kínál Microsoft .NEThoz. A Media Services-erőforrások és a Media Services API eléréséhez először hitelesítenie kell magát. 

Media Services támogatja a [Azure Active Directory (Azure ad)-alapú hitelesítést](../../active-directory/fundamentals/active-directory-whatis.md). Az Azure Media REST szolgáltatáshoz az szükséges, hogy a REST API kérelmeket használó felhasználó vagy alkalmazás vagy a **közreműködő** vagy a **tulajdonos** szerepkörrel rendelkezzen az erőforrásokhoz való hozzáféréshez. További információ: [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md).  

Ez a dokumentum áttekintést nyújt arról, hogyan érheti el a Media Services API-t REST vagy .NET API-k használatával.

> [!NOTE]
> A Access Control engedélyezése 2018. június 1-jén elavult.

## <a name="access-control"></a>Hozzáférés-vezérlés

Ahhoz, hogy az Azure Media REST-kérelme sikeres legyen, a hívó felhasználónak rendelkeznie kell közreműködői vagy tulajdonosi szerepkörrel ahhoz a Media Services fiókhoz, amelyhez hozzáférést próbál.  
Csak a tulajdonosi szerepkörrel rendelkező felhasználók biztosíthatják a Media Resource (fiók) hozzáférését az új felhasználókhoz vagy alkalmazásokhoz. A közreműködői szerepkör csak a média erőforráshoz fér hozzá.
A jogosulatlan kérelmek sikertelenek, a 401-es állapotkód. Ha ezt a hibakódot látja, ellenőrizze, hogy a felhasználó rendelkezik-e a felhasználó Media Services fiókjához hozzárendelt közreműködői vagy tulajdonosi szerepkörrel. Ezt a Azure Portalban tekintheti meg. Keresse meg a Media-fiókját, majd kattintson a **hozzáférés-vezérlés** fülre. 

![Hozzáférés-vezérlés lap](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Hitelesítési típusok 
 
Ha az Azure AD-hitelesítést Azure Media Services használatával használja, két hitelesítési lehetőség közül választhat:

- **Felhasználói hitelesítés**. Az alkalmazást használó személy hitelesítése Media Services erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználót a felhasználó hitelesítő adatainak megadására. Ilyen alkalmazás lehet például egy olyan felügyeletikonzol-alkalmazás, amelyet a jogosultsággal rendelkező felhasználók a kódolási feladatok és az élő streamelés monitorozására használnak. 
- **Egyszerű szolgáltatás hitelesítése**. Szolgáltatás hitelesítése. Ezt a hitelesítési módszert általában a démonszolgáltatásokat, közepes szintű szolgáltatásokat vagy ütemezett feladatokat futtató alkalmazások használják. Ilyenek például a Web Apps, a Function apps, a Logic apps, az API és a Service.

### <a name="user-authentication"></a>Felhasználóhitelesítés 

A felhasználói hitelesítési módszert használó alkalmazások a natív alkalmazások felügyelete vagy figyelése: Mobile apps, Windows apps és Console Applications. Ez a típusú megoldás akkor hasznos, ha az emberi interakciót szeretné használni a szolgáltatással a következő esetekben:

- A kódolási feladatok irányítópultjának figyelése.
- Monitorozási irányítópult az élő streamekhez.
- Felügyeleti alkalmazás asztali vagy mobil felhasználók számára az erőforrások Media Services-fiókban való felügyeletéhez.

> [!NOTE]
> Ezt a hitelesítési módszert nem szabad használni a felhasználók felé irányuló alkalmazásokhoz. 

Egy natív alkalmazásnak először hozzáférési jogkivonatot kell bekérnie az Azure AD-ből, majd azt akkor kell használnia, amikor HTTP-kéréseket végez a Media Services REST API. Adja hozzá a hozzáférési jogkivonatot a kérelem fejlécébe. 

Az alábbi ábrán egy tipikus interaktív alkalmazás-hitelesítési folyamat látható: 

![Natív alkalmazások diagramja](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

Az előző ábrán a számok kronológiai sorrendben jelölik a kérelmek folyamatát.

> [!NOTE]
> Ha a felhasználói hitelesítési módszert használja, az összes alkalmazás azonos (alapértelmezett) natív alkalmazás ügyfél-AZONOSÍTÓval és natív alkalmazás-átirányítási URI-vel rendelkezik. 

1. Hitelesítő adatok kérése a felhasználótól.
2. Igényeljen Azure AD hozzáférési jogkivonatot a következő paraméterekkel:  

   * Azure AD-bérlői végpont.

       A bérlő adatai a Azure Portalból kérhetők le. Vigye a kurzort a bejelentkezett felhasználó nevére a jobb felső sarokban.
   * Media Services erőforrás URI-ja. 

       Ez az URI ugyanaz az Azure-környezetben található Media Services-fiókoknál (például https: \/ /Rest.Media.Azure.net).

   * Media Services (natív) alkalmazás ügyfél-azonosítója.
   * Media Services (natív) alkalmazás átirányítási URI-ja.
   * A REST Media Services erőforrás-URI-ja.
        
       Az URI a REST API végpontot jelöli (például: https://test03.restv2.westus.media.azure.net/api/) .

     A paraméterek értékeinek lekéréséhez lásd: [a Azure Portal használata az Azure ad-hitelesítési beállítások eléréséhez](media-services-portal-get-started-with-aad.md) a felhasználói hitelesítési lehetőség használatával.

3. Az Azure AD hozzáférési jogkivonatot a rendszer elküldi az ügyfélnek.
4. Az ügyfél kérelmet küld az Azure Media REST APInak az Azure AD hozzáférési jogkivonattal.
5. Az ügyfél visszaküldi az adatait a Media Servicesból.

További információ arról, hogyan használható az Azure AD-hitelesítés a REST-kérelmekkel való kommunikációhoz a Media Services .NET Client SDK használatával: az [Azure ad-hitelesítés használata a Media Services API .net-tel való eléréséhez](media-services-dotnet-get-started-with-aad.md). 

Ha nem használja a Media Services .NET Client SDK-t, manuálisan kell létrehoznia egy Azure AD hozzáférési jogkivonat-kérést a 2. lépésben ismertetett paraméterek használatával. További információ: az Azure [ad-hitelesítési függvénytár használata az Azure ad-jogkivonat beszerzéséhez](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Azok az alkalmazások, amelyek gyakran használják ezt a hitelesítési módszert olyan alkalmazások, amelyek középső szintű szolgáltatásokat és ütemezett feladatokat futtatnak: webalkalmazások, Function apps, Logic apps, API-k és a-szolgáltatások. Ez a hitelesítési módszer olyan interaktív alkalmazásokhoz is alkalmas, amelyekben az erőforrások kezeléséhez érdemes lehet szolgáltatásfiókot használni.

Ha a szolgáltatás egyszerű hitelesítési módszerét használja a fogyasztói forgatókönyvek létrehozásához, a hitelesítés általában a középső szinten történik (néhány API-n keresztül), és nem közvetlenül a mobil-vagy asztali alkalmazásokban. 

Ennek a módszernek a használatához hozzon létre egy Azure AD-alkalmazást és egy szolgáltatásnevet a saját bérlőben. Az alkalmazás létrehozása után adja meg az alkalmazás közreműködői vagy tulajdonosi szerepkörének hozzáférését az Media Services fiókhoz. Ezt a Azure Portal az Azure CLI használatával vagy egy PowerShell-parancsfájllal teheti meg. Meglévő Azure AD-alkalmazást is használhat. Az Azure AD-alkalmazás és az egyszerű szolgáltatásnév regisztrálása és kezelése [a Azure Portalban](media-services-portal-get-started-with-aad.md)végezhető el. Ezt az [Azure CLI](media-services-use-aad-auth-to-access-ams-api.md) vagy a [PowerShell](media-services-powershell-create-and-configure-aad-app.md)használatával is megteheti. 

![Közepes szintű alkalmazások](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Az Azure AD-alkalmazás létrehozása után a következő beállításokhoz tartozó értékeket kapja meg. A hitelesítéshez ezeket az értékeket kell megadnia:

- Ügyfél-azonosító 
- Titkos ügyfélkulcs 

Az előző ábrán a számok kronológiai sorrendben jelölik a kérelmek folyamatát:
    
1. A középső rétegbeli alkalmazás (webes API vagy webalkalmazás) egy Azure AD hozzáférési jogkivonatot kér, amely a következő paraméterekkel rendelkezik:  

   * Azure AD-bérlői végpont.

       A bérlő adatai a Azure Portalból kérhetők le. Vigye a kurzort a bejelentkezett felhasználó nevére a jobb felső sarokban.
   * Media Services erőforrás URI-ja. 

       Ez az URI ugyanaz az Azure-környezetben található Media Services-fiókok esetében (például https: \/ /Rest.Media.Azure.net).

   * A REST Media Services erőforrás-URI-ja.

       Az URI a REST API végpontot jelöli (például: https://test03.restv2.westus.media.azure.net/api/) .

   * Azure AD-alkalmazás értékei: az ügyfél-azonosító és az ügyfél titka.
    
     A paraméterek értékeinek lekéréséhez lásd: [a Azure Portal használata az Azure ad-hitelesítési beállítások eléréséhez](media-services-portal-get-started-with-aad.md) az egyszerű szolgáltatás hitelesítése lehetőség használatával.

2. Az Azure AD hozzáférési jogkivonatot a középső szinten küldik el.
4. A középső szintű kérelem küldése az Azure Media REST API az Azure AD-jogkivonattal.
5. A középső szinten a Media Services származó adatok kerülnek vissza.

További információ arról, hogyan használható az Azure AD-hitelesítés a REST-kérelmekkel való kommunikációhoz a Media Services .NET Client SDK használatával: az [Azure ad-hitelesítés használata az Azure Media Services API .net-tel való eléréséhez](media-services-dotnet-get-started-with-aad.md). 

Ha nem használja a Media Services .NET Client SDK-t, manuálisan létre kell hoznia egy Azure AD-jogkivonat-kérelmet az 1. lépésben ismertetett paraméterek használatával. További információ: az Azure [ad-hitelesítési függvénytár használata az Azure ad-jogkivonat beszerzéséhez](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Hibaelhárítás

Kivétel: "a távoli kiszolgáló a következő hibát adta vissza: (401) nem engedélyezett."

Megoldás: a Media Services REST-kérelem sikeres végrehajtásához a hívó felhasználónak közreműködőnek vagy tulajdonosi szerepkörnek kell lennie abban a Media Services-fiókban, amelyhez hozzáférni próbál. További információ: [hozzáférés-vezérlés](media-services-use-aad-auth-to-access-ams-api.md#access-control) szakasz.

## <a name="resources"></a>További források

A következő cikkek az Azure AD-hitelesítési fogalmak áttekintését ismertetik: 

- [Az Azure AD által kezelt hitelesítési forgatókönyvek](../../active-directory/develop/authentication-vs-authorization.md)
- [Alkalmazás hozzáadása, frissítése vagy eltávolítása az Azure AD-ben](../../active-directory/develop/quickstart-register-app.md)
- [Azure-beli szerepkör-hozzárendelés hozzáadása vagy eltávolítása az Azure PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Következő lépések

* A Azure Portal használatával [férhet hozzá az Azure ad-hitelesítéshez Azure Media Services API](media-services-portal-get-started-with-aad.md)felhasználásához.
* Az Azure AD-hitelesítés használatával [férhet hozzá Azure Media Services API-hoz a .net](media-services-dotnet-get-started-with-aad.md)-tel.
