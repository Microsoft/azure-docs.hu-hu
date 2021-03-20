---
title: Az Azure API Management oldalának vezérlői | Microsoft Docs
description: Ismerje meg az Azure API Management fejlesztői portál-sablonjaiban használható oldal-vezérlőelemeket.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: b826c986a0af26bb79fb0823e4e8626f0165d460
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86250022"
---
# <a name="azure-api-management-page-controls"></a>Az Azure API Management oldalának vezérlői
Az Azure API Management a következő vezérlőket biztosítja a fejlesztői portál sablonjaiban való használathoz.  
  
Ha vezérlőt szeretne használni, helyezze a kívánt helyre a fejlesztői portál sablonjában. Egyes vezérlők, például az [app-Actions](#app-actions) vezérlő paraméterekkel rendelkeznek, ahogy az az alábbi példában is látható:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
A paraméterek értékeit a rendszer az adatmodell részeként adja át a sablonhoz. A legtöbb esetben egyszerűen beillesztheti a megadott példát az egyes vezérlők megfelelő működéséhez. A paraméterek értékével kapcsolatos további információkért tekintse meg az adatmodell szakaszt minden olyan sablon esetében, amelyben a vezérlő használható.  

További információ a sablonok használatáról: [API Management fejlesztői portál testreszabása sablonok használatával](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Fejlesztői portál sablonjának vezérlői  
  
-   [alkalmazások – műveletek](#app-actions)  
-   [alapszintű – bejelentkezési](#basic-signin)  
-   [Lapozás – vezérlés](#paging-control)  
-   [szolgáltatók](#providers)  
-   [Keresés – vezérlés](#search-control)  
-   [regisztráció](#sign-up)  
-   [előfizetés – gomb](#subscribe-button)  
-   [előfizetés – megszakítás](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a> alkalmazások – műveletek  
 A `app-actions` vezérlő felhasználói felületet biztosít a fejlesztői portál felhasználói profil lapján lévő alkalmazásokkal való interakcióhoz.  
  
 ![App&#45;műveletek vezérlése](./media/api-management-page-controls/APIM-app-actions-control.png "APIM alkalmazás – műveletek vezérlése")  
  
### <a name="usage"></a>Használat  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Paraméterek  
  
|Paraméter|Leírás|  
|---------------|-----------------|  
|appId|Az alkalmazás azonosítója.|  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `app-actions` vezérlőt a következő fejlesztői portál-sablonokban lehet használni:  
  
-   [Alkalmazások](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a> alapszintű – bejelentkezési  
 A `basic-signin` vezérlő a felhasználói bejelentkezési adatok gyűjtésének vezérlését biztosítja a fejlesztői portál bejelentkezési oldalán.  
  
 ![alapszintű&#45;bejelentkezési-vezérlés](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM alapszintű – bejelentkezési vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincsenek.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `basic-signin` vezérlőt a következő fejlesztői portál-sablonokban lehet használni:  
  
-   [Bejelentkezés](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a> Lapozás – vezérlés  
 Az megjeleníti a `paging-control` lapozási funkciót a fejlesztői portálon az elemek listáját megjelenítő lapokon.  
  
 ![lapozási vezérlő](./media/api-management-page-controls/APIM-paging-control.png "APIM lapozási vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincsenek.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `paging-control` vezérlőt a következő fejlesztői portál-sablonokban lehet használni:  
  
-   [API-lista](api-management-api-templates.md#APIList)  
  
-   [Problémák listája](api-management-issue-templates.md#IssueList)  
  
-   [Terméklista](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a> szolgáltatók  
 A `providers` vezérlő a hitelesítési szolgáltatók kiválasztására szolgáló vezérlőelemet biztosít a fejlesztői portál bejelentkezési oldalán.  
  
 ![szolgáltatók vezérlése](./media/api-management-page-controls/APIM-providers-control.png "APIM-szolgáltatók vezérlése")  
  
### <a name="usage"></a>Használat  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincsenek.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `providers` vezérlőt a következő fejlesztői portál-sablonokban lehet használni:  
  
-   [Bejelentkezés](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a> Keresés – vezérlés  
 A az `search-control` elemek listáját megjelenítő fejlesztői portálon keresési funkciókat biztosít.  
  
 ![keresési vezérlő](./media/api-management-page-controls/APIM-search-control.png "APIM keresési vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincsenek.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `search-control` vezérlőt a következő fejlesztői portál-sablonokban lehet használni:  
  
-   [API-lista](api-management-api-templates.md#APIList)  
  
-   [Terméklista](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a> regisztráció  
 A `sign-up` vezérlő a felhasználói profil adatainak gyűjtésére szolgáló vezérlőelemet biztosít a fejlesztői portál regisztrációs oldalán.  
  
 ![&#45;vezérlőelem aláírása](./media/api-management-page-controls/APIM-sign-up-control.png "APIM-regisztráció vezérlése")  
  
### <a name="usage"></a>Használat  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincsenek.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `sign-up` vezérlőt a következő fejlesztői portál-sablonokban lehet használni:  
  
-   [Regisztráció](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a> előfizetés – gomb  
 A `subscribe-button` lehetővé teszi a felhasználó termékre való feliratkozásának vezérlését.  
  
 ![előfizetés&#45;gomb vezérlőelemre](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM előfizetés – gomb vezérlőelem")  
  
### <a name="usage"></a>Használat  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincsenek.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `subscribe-button` vezérlőt a következő fejlesztői portál-sablonokban lehet használni:  
  
-   [Product](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a> előfizetés – megszakítás  
 A `subscription-cancel` vezérlő a fejlesztői portál felhasználói profil lapján lévő termékre vonatkozó előfizetés megszakítására szolgáló vezérlőelemet biztosít.  
  
 ![előfizetés&#45;a vezérlés megszakítása](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM-előfizetés – a vezérlés megszakítása")  
  
### <a name="usage"></a>Használat  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Paraméterek  
  
|Paraméter|Leírás|  
|---------------|-----------------|  
|subscriptionId|A megszakítani kívánt előfizetés azonosítója.|  
|cancelUrl|Az előfizetés megszakítja az URL-címet.|  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `subscription-cancel` vezérlőt a következő fejlesztői portál-sablonokban lehet használni:  
  
-   [Product](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Következő lépések
További információ a sablonok használatáról: [API Management fejlesztői portál testreszabása sablonok használatával](api-management-developer-portal-templates.md).
