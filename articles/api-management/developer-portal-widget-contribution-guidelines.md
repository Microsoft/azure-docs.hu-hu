---
title: Widgetek közreműködése a fejlesztői portálon
titleSuffix: Azure API Management
description: Megismeri a widget fejlesztői portál adattárában való közreműködéshez API Management ajánlott irányelveket.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c4d3ed2aeaac57f721d23d7c7aa1c70ef14e4294
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741707"
---
# <a name="how-to-contribute-widgets-to-the-api-management-developer-portal"></a>Widgetek közreműködése a API Management fejlesztői portálon

Ha egy widgetet szeretne a API Management fejlesztői portál [](https://github.com/Azure/api-management-developer-portal)GitHub-adattárában, kövesse az alábbi három lépésből következő folyamatot:

1. Fork the repository.fork the repository.

1. Implementálja a widgetet.

1. Nyisson meg egy lekéréses kérelmet, hogy a widget szerepeljen a hivatalos adattárban.

A widget örökli az adattár licencét. Ez a portál [saját](developer-portal-use-community-widgets.md) üzemeltetett verziójában lesz elérhető a feliratkozásos telepítéshez. A fejlesztői portál csapata dönthet úgy, hogy a portál felügyelt verziójában is szerepel.

Tekintse meg [a widget implementációval](developer-portal-implement-widgets.md) kapcsolatos oktatóanyagot, amely bemutatja, hogyan fejleszthet saját widgetet.

## <a name="contribution-guidelines"></a>Közreműködési irányelvek

Ez az útmutató az ügyfelek és a portálok látogatóinak biztonságát és adatvédelmét hivatott biztosítani. Kövesse az alábbi irányelveket a közreműködése elfogadatásának biztosításához:

1. Helyezze a widgetet a `community/widgets/<your-widget-name>` mappába.

1. A widget nevének kisbetűsnek és alfanumerikusnak kell lennie, és a szavakat kötőjelekkel kell elválasztani. Például: `my-new-widget`.

1. A mappának tartalmaznia kell egy képernyőképet a widgetről egy közzétett portálon.

1. A mappának tartalmaznia kell `readme.md` egy fájlt, amely a fájlból származó sablont `/scaffolds/widget/readme.md` követi.

1. A mappa tartalmazhat egy npm-parancsokat tartalmazó fájlt a widget függőségeinek `npm_dependencies` telepítéséhez vagy kezeléséhez.

    Explicit módon adja meg az összes függőség verzióját. Például:  

    ```console
    npm install azure-storage@2.10.3 axios@0.19.1
    ```

    A widgetnek minimális függőségeket kell igényelni. A felülvizsgálók minden függőséget alaposan meg fognak vizsgálni. A widget fő logikájának nyílt forráskódúnak kell lennie a widget mappájában. Ne csomagolja be npm-csomagba.

1. A widget mappán kívüli fájlok módosítása nem engedélyezett a widget közreműködésének részeként. Ez magában foglalja, de nem kizárólagosan a `/package.json` fájlt.

1. A nyomkövetési szkriptek be injektálása vagy az ügyfelek által létrehozott adatok egyéni szolgáltatásokba való küldése nem engedélyezett.

    > [!NOTE]
    > Az ügyfelek által írt adatokat csak a felületen keresztül `Logger` gyűjtheti.

## <a name="next-steps"></a>Következő lépések

- A hozzájárulásokkal kapcsolatos további információkért tekintse meg a API Management fejlesztői portál [GitHub-adattárát.](https://github.com/Azure/api-management-developer-portal/)

- A [widgetek implementálja](developer-portal-implement-widgets.md) a widgeteket, és megtudhatja, hogyan fejleszthet saját widgeteket lépésről lépésre.

- A [közösség által közzétett widgetek](developer-portal-use-community-widgets.md) használatának elsajátításért lásd: Közösségi widgetek használata.