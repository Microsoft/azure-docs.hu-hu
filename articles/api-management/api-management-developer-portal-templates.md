---
title: A API Management fejlesztői portál testreszabása sablonok használatával
titleSuffix: Azure API Management
description: Megtudhatja, hogyan szabhatja testre az Azure API Management fejlesztői portált sablonok használatával.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 16c0b3e6dc4bd9097312e3a8c43618e66781ef85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92092885"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Az Azure API Management fejlesztői portál testreszabása sablonok használatával

A fejlesztői portál három alapvető módon szabható testre az Azure API Managementben:

* [A statikus oldalak tartalmának és oldalelrendezési elemeinek szerkesztése][modify-content-layout]
* [A teljes fejlesztői portál oldalelemeihez használt stílusok frissítése][customize-styles]
* A [portál által létrehozott lapokhoz használt sablonok módosítása][portal-templates] (az útmutatóban ismertetett)

A sablonok segítségével testre szabhatja a rendszer által létrehozott fejlesztői portál lapjainak tartalmát (például API-dokumentumok, termékek, felhasználói hitelesítés stb.). A [DotLiquid](http://dotliquidmarkup.org/) szintaxissal, valamint a honosított karakterlánc-erőforrások, ikonok és lapok vezérlőelemek egy adott halmazával nagy rugalmassággal konfigurálhatja a lapok tartalmát, ahogy az illik.

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>A fejlesztői portál sablonjainak áttekintése

A sablonok szerkesztését a **fejlesztői portálon** végezheti el, miközben rendszergazdaként jelentkezett be. Először nyissa meg a Azure Portal, majd kattintson a **fejlesztői portál** elemre az API Management példányának szolgáltatás eszköztárán.

A fejlesztői portál sablonjainak eléréséhez kattintson a bal oldali Testreszabás ikonra a Testreszabás menü megjelenítéséhez, majd kattintson a **sablonok** elemre.

![Képernyőkép, amely kiemeli a Testreszabás ikont a testreszabási menü megjelenítéséhez.][api-management-customize-menu]

A sablonok listája a fejlesztői portál különböző lapjaira kiterjedő sablonok különböző kategóriáit jeleníti meg. Az egyes sablonok eltérőek, de a szerkesztési lépések és a módosítások közzétételének lépései megegyeznek. Sablon szerkesztéséhez kattintson a sablon nevére.

![Fejlesztői portál sablonjai][api-management-templates-menu]

A sablonra kattintva a sablon által testreszabható fejlesztői portálra kerül. Ebben a példában a **Terméklista** sablon jelenik meg. A **Terméklista** sablon a vörös téglalap által jelzett képernyő területét szabályozza.

![Terméklista sablon][api-management-developer-portal-templates-overview]

Egyes sablonok, például a **felhasználói profilok** sablonjai, testre szabják ugyanazon oldal különböző részeit.

![Felhasználói profil sablonjai][api-management-user-profile-templates]

Az egyes fejlesztői portálokhoz tartozó szerkesztő két szakaszt tartalmaz, amelyek az oldal alján jelennek meg. A bal oldali oldalon a sablon szerkesztési ablaktáblája látható, a jobb oldalon pedig a sablon adatmodellje látható.

A sablon szerkesztése ablaktábla tartalmazza azt a jelölést, amely a fejlesztői portál megfelelő oldalának megjelenését és viselkedését szabályozza. A sablonban lévő kód a [DotLiquid](http://dotliquidmarkup.org/) szintaxist használja. A DotLiquid egyik népszerű szerkesztője a [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). A sablonban a szerkesztés során végrehajtott módosítások valós időben jelennek meg a böngészőben, de nem láthatók az ügyfelek számára, amíg nem [menti](#to-save-a-template) és nem [teszi közzé](#to-publish-a-template) a sablont.

![Sablon jelölése][api-management-template]

A **sablon adatai** ablaktábla egy adott sablonban használható entitások adatmodelljének útmutatóját mutatja be. Ez az útmutató a fejlesztői portálon jelenleg megjelenített élő adatértékek megjelenítésével érhető el. A sablon ablaktáblák kibontásához kattintson a **sablon** adatpaneljének jobb felső sarkában található négyszögre.

![Sablon adatmodellje][api-management-template-data]

Az előző példában két olyan termék jelenik meg a fejlesztői portálon, amely a **sablon adatainak** ablaktáblájában megjelenő adatokból lett lekérve, ahogy az az alábbi példában is látható:

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
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
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

A **Terméklista** sablonban szereplő jelölés feldolgozza az adatokat, hogy a termékek gyűjteményén keresztül megismételve adja meg a kívánt kimenetet, valamint az egyes termékekre mutató hivatkozást. Jegyezze `<search-control>` fel a és a `<page-control>` Markup elemeit. Ezek vezérlik a keresés és a lapozás vezérlőelem megjelenítését az oldalon. `ProductsStrings|PageTitleProducts` egy honosított karakterlánc-hivatkozás, amely tartalmazza az `h2` oldal fejlécének szövegét. A fejlesztői portál sablonjaiban használható karakterlánc-erőforrások, oldal-vezérlőelemek és ikonok listáját itt tekintheti meg: [API Management fejlesztői portál sablonok referenciája](api-management-developer-portal-templates-reference.md).

```html
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

## <a name="to-save-a-template"></a>Sablon mentése
Sablon mentéséhez kattintson a Save (Mentés) gombra a sablon szerkesztőjében.

![Sablon mentése][api-management-save-template]

A mentett módosítások a fejlesztői portálon nem jelennek meg, amíg közzé nem tesznek.

## <a name="to-publish-a-template"></a>Sablon közzététele
A mentett sablonok egyenként vagy akár egyszerre is közzétehető. Egyéni sablon közzétételéhez kattintson a közzététel elemre a sablon szerkesztőjében.

![Sablon közzététele][api-management-publish-template]

Az **Igen** gombra kattintva erősítse meg, hogy a sablon élő legyen a fejlesztői portálon.

![Képernyőfelvétel: az Igen gombot választva a sablon élővé tétele.][api-management-publish-template-confirm]

A jelenleg közzé nem tett sablonok összes verziójának közzétételéhez kattintson a **Közzététel** elemre a sablonok listájában. A nem közzétett sablonokat a sablon nevét követő csillag jelöli. Ebben a példában a **Terméklista** **és a** sablonfájlok közzététel alatt állnak.

![Sablonok közzététele][api-management-publish-templates]

A megerősítéshez kattintson a **testreszabások közzététele** elemre.

![Közzététel megerősítése][api-management-publish-customizations]

Az újonnan közzétett sablonok azonnal érvénybe lépnek a fejlesztői portálon.

## <a name="to-revert-a-template-to-the-previous-version"></a>Sablon visszaállítása az előző verzióra
Ha vissza szeretne állítani egy sablont az előző közzétett verzióra, kattintson a Recover (vissza) gombra a sablon szerkesztőjében.

![Képernyőfelvétel: a sablon visszavonásához használt ikon kiemelése.][api-management-revert-template]

Kattintson a **Yes** (Igen) gombra a megerősítéshez.

![Képernyőfelvétel: az Igen gombra kattintva erősítse meg a módosításokat.][api-management-revert-template-confirm]

A sablon korábban közzétett verziója a fejlesztői portálon érhető el, miután a visszavert művelet befejeződött.

## <a name="to-restore-a-template-to-the-default-version"></a>Sablon visszaállítása az alapértelmezett verzióra
A sablonok alapértelmezett verzióra való visszaállítása kétlépéses folyamat. Először a sablonokat kell visszaállítani, majd közzé kell tenni a visszaállított verziókat.

Ha egyetlen sablont szeretne visszaállítani az alapértelmezett verzióra, kattintson a visszaállítás elemre a sablon szerkesztőjében.

![Sablon visszaállítása][api-management-reset-template]

Kattintson a **Yes** (Igen) gombra a megerősítéshez.

![Jóváhagyás][api-management-reset-template-confirm]

Ha az összes sablont az alapértelmezett verzióra szeretné visszaállítani, kattintson az **alapértelmezett sablonok visszaállítása** lehetőségre a sablon listán.

![Sablonok visszaállítása][api-management-restore-templates]

A visszaállított sablonokat ezután egyenként vagy egyszerre kell közzétenni a [sablon közzétételéhez](#to-publish-a-template)szükséges lépések követésével.

## <a name="next-steps"></a>Következő lépések
A fejlesztői portál sablonjaival, karakterlánc-erőforrásaival, ikonjaival és oldal-vezérlőelemekkel kapcsolatos információkért lásd: [API Management fejlesztői portál sablonok referenciája](api-management-developer-portal-templates-reference.md).

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png
