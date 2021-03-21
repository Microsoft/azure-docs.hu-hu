---
title: Az Azure statikus Web Apps alkalmazás beállításainak konfigurálása
description: Ismerje meg, hogyan konfigurálhatja az Azure statikus Web Apps alkalmazás beállításait
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 2886cb6433c4f8a65eb0661eded0ddd08248e137
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102219182"
---
# <a name="configure-application-settings-for-azure-static-web-apps-preview"></a>Alkalmazásbeállítások konfigurálása az Azure statikus Web Apps előzetes verziójának beállításához

Az Alkalmazásbeállítások megtartják a konfigurációs beállításokat a megváltozható értékeknél, például az adatbázis-kapcsolatok karakterláncait. Az Alkalmazásbeállítások hozzáadásával módosíthatja az alkalmazás konfigurációs bemenetét anélkül, hogy módosítania kellene az alkalmazás kódját.

Az alkalmazás beállításai a következők:

- Titkosított inaktív állapotban
- Másolás [átmeneti](review-publish-pull-requests.md) és éles környezetbe

Az Alkalmazásbeállítások más néven környezeti változók.

> [!IMPORTANT]
> A cikkben ismertetett Alkalmazásbeállítások csak az Azure-beli statikus webalkalmazások háttér-API-ra vonatkoznak.
>
> A környezeti változók az előtér-webalkalmazással történő használatáról további információt a JavaScript- [keretrendszer](#javascript-frameworks-and-libraries) vagy a [statikus site Generator](#static-site-generators)dokumentációjában talál.

## <a name="prerequisites"></a>Előfeltételek

- Azure-beli statikus Web Apps alkalmazás
- [Azure CLI](/cli/azure/install-azure-cli)

## <a name="types-of-application-settings"></a>Az Alkalmazásbeállítások típusai

Az Azure statikus Web Apps alkalmazásnak jellemzően két aspektusa van. Az első az a webalkalmazás vagy statikus tartalom, amelyet a HTML, a CSS, a JavaScript és a képek jelképeznek. A második a háttérbeli API, amelyet egy Azure Functions alkalmazás működtet.

Ez a cikk bemutatja, hogyan kezelheti az alkalmazás beállításait a háttérbeli API-ban Azure Functionsban.

A cikkben ismertetett Alkalmazásbeállítások nem használhatók statikus webalkalmazásokban, illetve nem hivatkozhatnak rájuk. Számos előtér-keretrendszer és statikus hely generátora azonban lehetővé teszi környezeti változók használatát a fejlesztés során. A felépítési időszakban ezeket a változókat a generált HTML-vagy JavaScript-értékük váltja fel. Mivel a HTML-ben és a JavaScriptben tárolt adatok könnyen felderíthetők a webhely látogatói számára, el szeretné kerülni a bizalmas adatok elhelyezését az előtér-alkalmazásban. A bizalmas adatokat tároló beállítások a legmegfelelőbbek az alkalmazás API-részében.

A környezeti változók JavaScript-keretrendszerrel vagy-könyvtárral történő használatával kapcsolatos további információkért tekintse meg az alábbi cikkeket.

### <a name="javascript-frameworks-and-libraries"></a>JavaScript-keretrendszerek és-kódtárak

- [Angular](https://angular.io/guide/build#configuring-application-environments)
- [React](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Svelte](https://linguinecode.com/post/how-to-add-environment-variables-to-your-svelte-js-app)
- [Vue](https://cli.vuejs.org/guide/mode-and-env.html)

### <a name="static-site-generators"></a>Statikus hely generátorai

- [Gatsby](https://www.gatsbyjs.org/docs/environment-variables/)
- [Hugo](https://gohugo.io/getting-started/configuration/)
- [Jekyll](https://jekyllrb.com/docs/configuration/environments/)

## <a name="about-api-app-settings"></a>Az API-alkalmazások beállításai

Az Azure statikus Web Apps API-jai Azure Functions-t használnak, ami lehetővé teszi az Alkalmazásbeállítások megadását a _local.settings.js_ fájlban. Ez a fájl határozza meg az alkalmazás beállításait a `Values` konfiguráció tulajdonságában.

A következő minta _local.settings.js_ bemutatja, hogyan adhat hozzá értéket a számára `DATABASE_CONNECTION_STRING` .

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

A tulajdonságban definiált beállítások a `Values` kódban, az objektumból elérhető környezeti változókként is hivatkozhatók `process.env` .

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

A `local.settings.json` GitHub-tárház nem követi nyomon a fájlt, mert a bizalmas információk, például az adatbázis-kapcsolatok karakterláncai gyakran szerepelnek a fájlban. Mivel a helyi beállítások megmaradnak a gépen, manuálisan fel kell töltenie a beállításait az Azure-ba.

A beállítások feltöltése általában ritkán történik, és minden buildhez nem szükséges.

## <a name="uploading-application-settings"></a>Alkalmazásbeállítások feltöltése

Az Alkalmazásbeállítások az Azure Portal vagy az Azure CLI használatával konfigurálhatók.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A Azure Portal egy felületet biztosít az Alkalmazásbeállítások létrehozásához, frissítéséhez és törléséhez.

1. Navigáljon a [Azure Portal](https://portal.azure.com).

1. A keresési sávban keresse meg és válassza a **statikus Web Apps** elemet.

1. Kattintson a **konfiguráció** lehetőségre az oldalsávon.

1. Válassza ki azt a környezetet, amelyre alkalmazni kívánja az Alkalmazásbeállítások beállításait. Az átmeneti környezetek létrehozásakor a rendszer automatikusan létrehozza a lekéréses kérelmeket, és a lekéréses kérelem egyesítése után előkészíti azokat éles környezetben. Az Alkalmazásbeállítások megadható környezet alapján.

1. Kattintson a **Hozzáadás gombra** egy új Alkalmazásbeállítás hozzáadásához.

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Azure statikus Web Apps konfiguráció nézet":::

1. Adjon meg egy **nevet** és egy **értéket**.

1. Kattintson az **OK** gombra.

1. Kattintson a **Mentés** gombra.

### <a name="using-the-azure-cli"></a>Az Azure CLI-vel

A parancs használatával a `az rest` Beállítások tömeges feltöltését végezheti el az Azure-ban. A parancs JSON-objektumokként fogadja el az Alkalmazásbeállítások egy nevű szülő tulajdonságban `properties` .

A megfelelő értékekkel rendelkező JSON-fájlok létrehozásának legegyszerűbb módja a _local.settings.js_ fájlon létrehozott módosított verziójának létrehozása.

1. Annak biztosítása érdekében, hogy az új, bizalmas adatokkal nem rendelkező fájlok nyilvánosan elérhetők legyenek, adja hozzá a következő bejegyzést a _. gitignore_ -fájlhoz.

   ```bash
   local.settings*.json
   ```

2. Ezután készítsen másolatot a _local.settings.js_ fájlról, és nevezze el _local.settings.properties.jsa_ következőn:.

3. Az új fájlban távolítsa el az összes többi fájlt a fájlból, kivéve az alkalmazás beállításait, és nevezze át a következőre: `Values` `properties` .

   A fájlnak most az alábbi példához hasonlóan kell kinéznie:

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

Az Azure CLI-parancshoz a feltöltés futtatásához a fiókhoz tartozó értékek száma szükséges. A statikus Web Apps erőforrás _Áttekintés_ ablakában a következő információk érhetők el:

1. Statikus hely neve
2. Erőforráscsoport neve
3. Előfizetés azonosítója

:::image type="content" source="media/application-settings/overview.png" alt-text="Az Azure statikus Web Apps áttekintése":::

4. Futtassa a következő parancsot egy terminálról vagy parancssorból. Ügyeljen rá, hogy a, a és az érték helyőrzőit cserélje le az `<YOUR_STATIC_SITE_NAME>` `<YOUR_RESOURCE_GROUP_NAME>` `<YOUR_SUBSCRIPTION_ID>` _áttekintő_ ablak értékeire.

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

> [!IMPORTANT]
> A "local.settings.properties.json" fájlnak ugyanabban a könyvtárban kell lennie, ahol ez a parancs fut. A fájl neve tetszőleges. A név nem jelentős.

### <a name="view-application-settings-with-the-azure-cli"></a>Alkalmazásbeállítások megtekintése az Azure CLI-vel

Az Alkalmazásbeállítások az Azure CLI-n keresztül tekinthetők meg.

- Futtassa a következő parancsot egy terminálról vagy parancssorból. Ügyeljen rá, hogy a helyőrzőket cserélje le az `<YOUR_SUBSCRIPTION_ID>` `<YOUR_RESOURCE_GROUP_NAME>` `<YOUR_STATIC_SITE_NAME>` értékekre.

   ```bash
   az rest --method post --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Helyi fejlesztés beállítása](local-development.md)
