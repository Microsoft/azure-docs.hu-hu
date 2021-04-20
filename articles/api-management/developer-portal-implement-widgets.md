---
title: Widgetek implementálva a fejlesztői portálon
titleSuffix: Azure API Management
description: Megtudhatja, hogyan implementálhat külső API-kból adatokat felhozó widgeteket, és hogyan jelenítse meg API Management fejlesztői portálon.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 4bda136a1abe8f9ffb443973731ebbe13b56ac3b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741691"
---
# <a name="implement-widgets-in-the-developer-portal"></a>Widgetek implementálva a fejlesztői portálon

Ebben az oktatóanyagban egy olyan widgetet implementál, amely egy külső API-ból származó adatokat használ fel, és megjeleníti azokat API Management fejlesztői portálon.

A widget lekéri a munkamenet-leírásokat a [Conference API-mintából.](https://conferenceapi.azurewebsites.net/?format=json) A munkamenet-azonosító egy kijelölt widgetszerkesztőben lesz beállítva.

A fejlesztési folyamattal kapcsolatos segítségért tekintse meg a teljes widgetet, amely a fejlesztői portál `examples` GitHub API Management tár mappájában [található:](https://github.com/Azure/api-management-developer-portal/) `/examples/widgets/conference-session` .

:::image type="content" source="media/developer-portal-implement-widgets/widget-published.png" alt-text="A közzétett widget képernyőképe":::

## <a name="prerequisites"></a>Előfeltételek

* Állítson be [egy helyi környezetet](developer-portal-self-host.md#step-1-set-up-local-environment) a fejlesztői portál legújabb kiadásában.

* Tisztában kell lennie [a Paperbits widget felattájájval.](https://paperbits.io/wiki/widget-anatomy)


## <a name="copy-the-scaffold"></a>A állvány másolása

Az új widget felépítéséhez használja a mappa egyik állványát kiindulási `widget` `/scaffolds` pontként.

1. Másolja a mappát a `/scaffolds/widget` `/community/widgets` következőbe: .
1. Nevezze át a mappát a `conference-session` következőre: .

## <a name="rename-exported-module-classes"></a>Exportált modulosztályok átnevezése

Nevezze át az exportált modulosztályokat úgy, hogy lecseréli az előtagot a következőre `Widget` `ConferenceSession` ezekben a fájlokban:

- `widget.design.module.ts`

- `widget.publish.module.ts`

- `widget.runtime.module.ts`
    
A fájlban például `widget.design.module.ts` módosítsa a `WidgetDesignModule` következőre: `ConferenceSessionDesignModule` :
    
```typescript
export class WidgetDesignModule implements IInjectorModule {
```
felhasználóként a(z) 
    
```typescript
export class ConferenceSessionDesignModule implements IInjectorModule {
```
    
   
## <a name="register-the-widget"></a>A widget regisztrálása

Regisztrálja a widget moduljait a portál gyökérmoduljaiban a következő sorok a megfelelő fájlokban való hozzáadásával:

1. `src/apim.design.module.ts` – egy modul, amely a tervezéskor szükséges függőségeket regisztrálja.

   ```typescript
   import { ConferenceSessionDesignModule } from "../community/widgets/conference-session/widget.design.module";
   
   ...
   injector.bindModule(new ConferenceSessionDesignModule());
   ```
1. `src/apim.publish.module.ts` – egy modul, amely regisztrálja a közzétételi idő függőségeit.

   ```typescript
   import { ConferenceSessionPublishModule } from "../community/widgets/conference-session/widget.publish.module";

    ...

    injector.bindModule(new ConferenceSessionPublishModule());
    ```

1. `src/apim.runtime.module.ts` – futásidejű függőségek.

    ```typescript
    import { ConferenceSessionRuntimeModule } from "../community/widgets/conference-session/widget.runtime.module";

    ...

    injector.bindModule(new ConferenceSessionRuntimeModule());
    ```

## <a name="place-the-widget-in-the-portal"></a>A widget a portálon való helyezése

Most már készen áll a duplikált állvány csatlakoztatására és a fejlesztői portálon való használatára.

1. Futtassa a következő parancsot: `npm start`.

1. Az alkalmazás betöltésekor helyezze az új widgetet egy oldalra. A widgetválasztóban a kategória neve alatt `Your widget` `Community` található.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-selector.png" alt-text="Képernyőkép a widgetválasztóról":::

1. Mentse az oldalt a **Ctrl** + **S** billentyű lenyomásával (vagy **⌘** + **S** macOS rendszeren).

    > [!NOTE]
    > A tervezés során továbbra is használhatja a webhelyet a **Ctrl** (vagy ⌘) **billentyű** lenyomva tartása mellett.

## <a name="add-custom-properties"></a>Egyéni tulajdonságok hozzáadása

Ahhoz, hogy a widget lekérni tudja a munkamenet-leírásokat, tisztában kell lennie a munkamenet-azonosítóval. Adja hozzá `Session ID` a tulajdonságot a megfelelő felületekhez és osztályokhoz:

Ahhoz, hogy a widget lekérje a munkamenet leírását, tisztában kell lennie a munkamenet-azonosítóval. Adja hozzá a munkamenet-azonosító tulajdonságot a megfelelő felületekhez és osztályokhoz:

1. `widgetContract.ts` – adatszerződés (adatréteg), amely a widget konfigurációjának megőrzését határozza meg.

    ```typescript
    export interface WidgetContract extends Contract {
        sessionNumber: string;
    }
    ```

1. `widgetModel.ts` - model (üzleti réteg) – a widget elsődleges ábrázolása a rendszerben. A szerkesztők frissítik, és a bemutató réteg rendereli.

    ```typescript
    export class WidgetModel {
        public sessionNumber: string;
    }
    ```

1. `ko/widgetViewModel.ts` - nézetmodell (bemutató réteg) – felhasználói felületi keretrendszer-specifikus objektum, amely a fejlesztői portálon renderelhető a HTML-sablonnal.

    > [!NOTE]
    > Ebben a fájlban semmit sem kell módosítania.

## <a name="configure-binders"></a>Kötések konfigurálása

Engedélyezze a adatfolyamot `sessionNumber` az adatforrásból a widget bemutatóba. Szerkessze `ModelBinder` a és `ViewModelBinder` az entitást:

1. `widgetModelBinder.ts` segít előkészíteni a modellt a szerződésben leírt adatok használatával.

    ```typescript
    export class WidgetModelBinder implements IModelBinder<WidgetModel> {
        public async contractToModel(contract: WidgetContract): Promise<WidgetModel> {
            model.sessionNumber = contract.sessionNumber || "107"; // 107 is the default session id
            ...
        }
    
        public modelToContract(model: WidgetModel): Contract {
            const contract: WidgetContract = {
                sessionNumber: model.sessionNumber
                ...
            };
            ...
        }
    }
    ```

1. `ko/widgetViewModelBinder.ts` tudja, hogy a fejlesztői portálnak hogyan kell bemutatnia a modellt (nézetmodellként) egy adott felhasználói felületi keretrendszerben.

    ```typescript
    ...
    public async updateViewModel(model: WidgetModel, viewModel: WidgetViewModel): Promise<void> {
            viewModel.runtimeConfig(JSON.stringify({
                sessionNumber: model.sessionNumber
            }));
        }
    }
    ...
    ```

## <a name="adjust-design-time-widget-template"></a>Tervezéskor használható widgetsablon módosítása

Az egyes hatókörök összetevői egymástól függetlenül futnak. Külön függőséginjektálásos tárolóval, saját konfigurációval, életciklussal stb. vannak. Akár különböző felhasználói felületi keretrendszerek is működtethetnek ilyeneket (ebben a példában ez a AzOut JS).

Tervezési szempontból minden futásidejű összetevő csak egy bizonyos attribútumokkal és/vagy tartalommal rendelkezik HTML-címke. Szükség esetén a konfiguráció egyszerű jelölővel lesz átkedve. Egyszerű esetekben, mint ebben a példában, a paramétert a attribútumban adhatja át. Ha a konfiguráció összetettebb, egy kijelölt konfigurációszolgáltató (például ) által lekért kötelező beállítás(k) azonosítóját is `ISettingsProvider` használhatja.

1. Frissítse a `ko/widgetView.html` fájlt:

    ```html
    <widget-runtime data-bind="attr: { params: runtimeConfig }"></widget-runtime>
    ```

    Amikor a fejlesztői portál tervezési vagy közzétételi időben futtatja a kötést, az eredményül kapott HTML a `attr` következő:  

    ```html
    <widget-runtime params="{ sessionNumber: 107 }"></widget-runtime>
    ```

    Ezután a futásidőben az összetevő beolvassa és `widget-runtime` `sessionNumber` használja az inicializálási kódban (lásd alább).

1. Frissítse a `widgetHandlers.ts` fájlt, hogy a létrehozáskor hozzárendelje a munkamenet-azonosítót:

    ```typescript
    ...
    createModel: async () => {
        var model = new ConferenceSessionModel();
        model.sessionNumber = "107";
            return model;
        }
    ...
    ```

## <a name="revise-runtime-view-model"></a>A futásidejű nézetmodell felülvizsgálata

A futtatókörnyezeti összetevők a webhelyen futó kódok. A API Management fejlesztői portálon például mind a dinamikus összetevők (például API-adatok, *API-konzol)* mögötti szkriptek, amelyek olyan műveleteket kezelnek, mint a kódminta létrehozása, a kérések küldése stb. 

A futásidejű összetevő nézetmodelljének a következő metódusokkal és tulajdonságokkal kell rendelkezik:

- A dekorátorral jelölt tulajdonság, amely a kívülről átadott összetevő bemeneti paramétereként használatos (a tervezés során létrehozott jelölő; lásd az `sessionNumber` `Param` előző lépést).
- A `sessionDescription` widgetsablonhoz kötött tulajdonság (lásd `widget-runtime.html` a cikk egy későbbi bekezdését).
- A widget létrehozása és az összes paraméterének hozzárendelése után meghívott metódus `initialize` (a `OnMounted` dekorátorral együtt). Ez jó hely a olvasásához és az API meghívásához `sessionNumber` a `HttpClient` használatával. A `HttpClient` egy, az IoC-tároló által injektált függőség (a Vezérlés inverziója).

- Először a fejlesztői portál hozza létre a widgetet, és hozzárendeli annak összes paraméterét. Ezután meghívja a `initialize` metódust.

    ```typescript
    ...
    import * as ko from "knockout";
    import { Component, RuntimeComponent, OnMounted, OnDestroyed, Param } from "@paperbits/common/ko/decorators";
    import { HttpClient, HttpRequest } from "@paperbits/common/http";
    ...

    export class WidgetRuntime {
        public readonly sessionDescription: ko.Observable<string>;

        constructor(private readonly httpClient: HttpClient) {
            ...
            this.sessionNumber = ko.observable();
            this.sessionDescription = ko.observable();
            ...
        }

        @Param()
        public readonly sessionNumber: ko.Observable<string>;

        @OnMounted()
        public async initialize(): Promise<void> {
            ...
            const sessionNumber = this.sessionNumber();

            const request: HttpRequest = {
                url: `https://conferenceapi.azurewebsites.net/session/${sessionNumber}`,
                method: "GET"
            };

            const response = await this.httpClient.send<string>(request);
            const sessionDescription = response.toText();
    
            this.sessionDescription(sessionDescription);
            ...
        }
        ...
    }
    ```

## <a name="tweak-the-widget-template"></a>A widgetsablon finomhangolása

Frissítse a widgetet a munkamenet leírásának megjelenítéséhez.

Használjon bekezdéscímkét és `markdown` egy (vagy ) kötést a `text` `ko/runtime/widget-runtime.html` fájlban a leírás megjelenítéséhez:

```html
<p data-bind="markdown: sessionDescription"></p>
```

## <a name="add-the-widget-editor"></a>A widgetszerkesztő hozzáadása

A widget most már úgy van konfigurálva, hogy lekéri a munkamenet `107` leírását. A `107` kódban az alapértelmezett munkamenetet adja meg. Annak ellenőrzéséhez, hogy mindent rendben tett-e, futtassa a következőt: , és győződjön meg arról, hogy a fejlesztői portálon megjelenik `npm start` a leírás az oldalon.

Most végezze el az alábbi lépéseket, hogy a felhasználó beállítsa a munkamenet-azonosítót egy widgetszerkesztőben:

1. Frissítse a `ko/widgetEditorViewModel.ts` fájlt:

    ```typescript
    export class WidgetEditor implements WidgetEditor<WidgetModel> {
        public readonly sessionNumber: ko.Observable<string>;

        constructor() {
            this.sessionNumber = ko.observable();
        }

        @Param()
        public model: WidgetModel;

        @Event()
        public onChange: (model: WidgetModel) => void;

        @OnMounted()
        public async initialize(): Promise<void> {
            this.sessionNumber(this.model.sessionNumber);
            this.sessionNumber.subscribe(this.applyChanges);
        }

        private applyChanges(): void {
            this.model.sessionNumber = this.sessionNumber();
            this.onChange(this.model);
        }
    }
    ```

    A szerkesztő nézetmodellje ugyanazt a megközelítést használja, mint korábban, de van egy új tulajdonság, amely a `onChange` következővel van ékesítve: `@Event()` . A visszahívással értesíti a figyelőket (ebben az esetben a tartalomszerkesztőt) a modell változásairól.

1. Frissítse a `ko/widgetEditorView.html` fájlt:

    ```html
    <input type="text" class="form-control" data-bind="textInput: sessionNumber" />
    ```

1. Futtassa ismét az `npm start` parancsot. A widgetszerkesztőben módosítania `sessionNumber` kell. Módosítsa az azonosítót a következőre: `108` , mentse a módosításokat, és frissítse a böngésző lapját. Ha problémákat tapasztal, előfordulhat, hogy újra hozzá kell adni a widgetet az oldalhoz.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-editor.png" alt-text="Képernyőkép a widgetszerkesztőről":::

## <a name="rename-the-widget"></a>A widget átnevezése

Módosítsa a widget nevét a `constants.ts` fájlban:

```typescript
...
export const widgetName = "conference-session";
export const widgetDisplayName = "Conference session";
...
```

> [!NOTE]
> Ha a widgetet az adattárhoz járul hozzá, a elemnek meg kell egyezni a mappa nevével, és a megjelenítendő névből kell származtatva lennie (kisbetűkből és szóközökből kell származtatva, kötőjelekkel `widgetName` helyettesítve). A kategóriának a következőnek kell `Community` maradnia: .

## <a name="next-steps"></a>Következő lépések


További információ a fejlesztői portálról:

- [Az Azure API Management fejlesztői portál áttekintése](api-management-howto-developer-portal.md)

- [Közreműködés widgetek –](developer-portal-widget-contribution-guidelines.md) szívesen fogadjuk és bátorítjuk a közösségi közreműködést.

- A [közösség által közzétett widgetek](developer-portal-use-community-widgets.md) használatának elsajátításért lásd: Közösségi widgetek használata.
