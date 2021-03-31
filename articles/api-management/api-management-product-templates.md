---
title: Termékcsoportok az Azure API Managementban | Microsoft Docs
description: Megtudhatja, hogyan szabhatja testre a termék oldalain lévő tartalmat az Azure API Management fejlesztői portálon.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 4c8cd4aa3e91c5d69c40e47683818ed8bc9be338
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86249903"
---
# <a name="product-templates-in-azure-api-management"></a>Termékcsoportok az Azure API Management

Az Azure API Management lehetővé teszi a fejlesztői portál oldalai tartalmának testreszabását a tartalmukat konfiguráló sablonok használatával. A [DotLiquid](http://dotliquidmarkup.org/) szintaxisának és a választott szerkesztőnek, például a [tervezők DotLiquid](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), valamint a honosított [karakterlánc-erőforrások](api-management-template-resources.md#strings), a karakterjel- [erőforrások](api-management-template-resources.md#glyphs)és a [lap vezérlőelemeknek](api-management-page-controls.md)a használatával nagyszerű rugalmasságot biztosít a lapok tartalmának konfigurálásához, ahogy az a fenti sablonok használatával illik.  
  
 Az ebben a szakaszban található sablonok segítségével testre szabhatja a termékek oldalain lévő tartalmat a fejlesztői portálon.  
  
-   [Terméklista](#ProductList)  
  
-   [Product](#Product)  
  
> [!NOTE]
>  A minta alapértelmezett sablonjai a következő dokumentációban szerepelnek, de a folyamatos fejlődés miatt változhatnak. Az élő alapértelmezett sablonokat a fejlesztői portálon tekintheti meg, ha a kívánt egyéni sablonokat navigálja. További információ a sablonok használatáról: [API Management fejlesztői portál testreszabása sablonok használatával](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="product-list"></a><a name="ProductList"></a> Terméklista  
 A **Terméklista** sablon lehetővé teszi a Terméklista oldal törzsének testreszabását a fejlesztői portálon.  
  
 ![Termékek listája](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<search-control></search-control>  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if products.size > 0 %}  
    <ul class="list-unstyled">  
    {% for product in products %}  
        <li>  
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>  
            {{product.description}}  
        </li>     
    {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>Vezérlők  
 A `Product list` sablon a következő [lap vezérlőit](api-management-page-controls.md)használhatja.  
  
-   [Lapozás – vezérlés](api-management-page-controls.md#paging-control)  
  
-   [Keresés – vezérlés](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Lapozás|[Lapozófájl](api-management-template-data-model-reference.md#Paging) entitása.|A termékek gyűjteményének lapozási adatai.|  
|Szűrés|Entitás [szűrése](api-management-template-data-model-reference.md#Filtering) .|A Products List lap szűrési információi.|  
|Termékek|A [termék](api-management-template-data-model-reference.md#Product) entitások gyűjteménye.|Az aktuális felhasználó számára látható termékek.|  
  
### <a name="sample-template-data"></a>Minta sablonjának adatterülete  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 2,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Filtering": {  
        "Pattern": null,  
        "Placeholder": "Search products"  
    },  
    "Products": [  
        {  
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
            "Title": "Unlimited",  
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "Terms": null,  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        }  
    ]  
}  
```  
  
##  <a name="product"></a><a name="Product"></a> Termék  
 A **termék** sablonja lehetővé teszi a termék oldal törzsének testreszabását a fejlesztői portálon.  
  
 ![Fejlesztői portál termék lapja](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Vezérlők  
 A `Product list` sablon a következő [lap vezérlőit](api-management-page-controls.md)használhatja.  
  
-   [előfizetés – gomb](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Termék|[Product](api-management-template-data-model-reference.md#Product)|A megadott termék.|  
|IsDeveloperSubscribed|boolean|Azt határozza meg, hogy az aktuális felhasználó előfizetett-e erre a termékre.|  
|SubscriptionState|szám|Az előfizetés állapota. A lehetséges állapotok a következők:<br /><br /> -   `0 - suspended` – az előfizetés le van tiltva, és az előfizető nem hívhat meg semmilyen API-t a termékről.<br />-   `1 - active` – az előfizetés aktív.<br />-   `2 - expired` – az előfizetés elérte a lejárati dátumot, és inaktiválva lett.<br />-   `3 - submitted` – az előfizetési kérést a fejlesztő hozta létre, de még nem hagyta jóvá vagy nem utasította el.<br />-   `4 - rejected` – az előfizetési kérelmet a rendszergazda megtagadta.<br />-   `5 - cancelled` – a fejlesztő vagy a rendszergazda megszakította az előfizetést.|  
|Korlátok|array|Ez a tulajdonság elavult, és nem használható.|  
|DelegatedSubscriptionEnabled|boolean|Engedélyezve van-e a [delegálás](./api-management-howto-setup-delegation.md) ehhez az előfizetéshez.|  
|DelegatedSubscriptionUrl|sztring|Ha a delegálás engedélyezve van, a delegált előfizetési URL-cím.|  
|IsAgreed|boolean|Ha a termék rendelkezik feltételekkel, azt, hogy az aktuális felhasználó elfogadta-e a feltételeket.|  
|Előfizetések|[Előfizetés összegző](api-management-template-data-model-reference.md#SubscriptionSummary) entitások gyűjteménye.|A termékre vonatkozó előfizetések.|  
|API|[API](api-management-template-data-model-reference.md#API) -entitások gyűjteménye.|A termékben található API-k.|  
|CannotAddBecauseSubscriptionNumberLimitReached|boolean|Azt határozza meg, hogy az aktuális felhasználó jogosult-e előfizetni erre a termékre az előfizetési korlát tekintetében.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|boolean|Azt határozza meg, hogy az aktuális felhasználó jogosult-e előfizetni erre a termékre több előfizetésre vonatkozóan.|  
  
### <a name="sample-template-data"></a>Minta sablonjának adatterülete  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>Következő lépések
További információ a sablonok használatáról: [API Management fejlesztői portál testreszabása sablonok használatával](api-management-developer-portal-templates.md).
