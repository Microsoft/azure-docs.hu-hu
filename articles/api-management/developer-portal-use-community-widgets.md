---
title: Közösségi widgetek használata a fejlesztői portálon
titleSuffix: Azure API Management
description: Ismerje meg a fejlesztői portál közösségi widgetjeit API Management a kódba való be- és használatukról.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: de6160aa2e556297287ae9e9ecd58a93e54d863f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741676"
---
# <a name="use-community-widgets-in-the-developer-portal"></a>Közösségi widgetek használata a fejlesztői portálon

Minden fejlesztő a közös használatú widgeteket a fejlesztői portál `/community/widgets/` [GitHub-API Management mappájában helyezze el.](https://github.com/Azure/api-management-developer-portal) A fejlesztői portál csapata elfogadta mindegyiket. A widgeteket úgy használhatja, hogy [](developer-portal-self-host.md) a saját üzemeltetett verziójába injektálja őket. A fejlesztői portál felügyelt verziója jelenleg nem támogatja a közösségi widgeteket.

> [!NOTE]
> A fejlesztői portál csapata alaposan átvizsgálja a közzétett widgeteket és azok függőségeit. A csapat azonban nem tudja garantálni, hogy biztonságosan betöltheti a widgeteket. Saját döntés alapján dönt a közösség által közzétett widget használata mellett. A megelőző [intézkedésekről a widgetek hozzájárulási](developer-portal-widget-contribution-guidelines.md#contribution-guidelines) útmutatója tartalmaz további információt.

## <a name="inject-and-use-external-widgets"></a>Külső widgetek be- és használata

1. Állítson be [egy helyi környezetet](developer-portal-self-host.md#step-1-set-up-local-environment) a fejlesztői portál legújabb kiadásának.

1. Ugrás a widget mappájába a `/community/widgets` könyvtárban. Olvassa el a widget leírását a `readme.md` fájlban.

1. Regisztrálja a widgetet a portál moduljaiban:

    1. `src/apim.design.module.ts` – egy modul, amely a tervezéskor szükséges függőségeket regisztrálja.
    
        ```typescript
        import { WidgetNameDesignModule } from "../community/widgets/<widget-name>/widget.design.module";
    
        ...
    
            injector.bindModule(new WidgetNameDesignModule());
        ```
    
    1. `src/apim.publish.module.ts` – egy modul, amely a közzétételi idő függőségeit regisztrálja.
    
        ```typescript
        import { WidgetNamePublishModule } from "../community/widgets/<widget-name>/widget.publish.module";
    
        ...
    
            injector.bindModule(new WidgetNamePublishModule());
        ```
    
    1. `src/apim.runtime.module.ts` – a futásidő függőségeit regisztráló modul.
    
        ```typescript
        import { WidgetNameRuntimeModule } from "../community/widgets/<widget-name>/widget.runtime.module";
    
        ...
    
            injector.bindModule(new WidgetNameRuntimeModule());
        ```

1. Ellenőrizze, hogy a widget rendelkezik-e `npm_dependencies` fájllal.

1. Ha igen, másolja ki a parancsokat a fájlból, és futtassa őket az adattár felső könyvtárában.

    Ezzel telepíti a widget függőségeit.

1. Futtassa az `npm start` parancsot.

A widget a Közösség kategóriában **látható** a widgetválasztóban.

:::image type="content" source="media/developer-portal-use-community-widgets/widget-selector.png" alt-text="Képernyőkép a widgetválasztóról":::


## <a name="next-steps"></a>Következő lépések


További információ a fejlesztői portálról:

- [Az Azure API Management fejlesztői portál áttekintése](api-management-howto-developer-portal.md)

- [Közreműködési widgetek](developer-portal-widget-contribution-guidelines.md)
