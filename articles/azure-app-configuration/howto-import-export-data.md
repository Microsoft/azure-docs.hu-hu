---
title: Az Azure-alkalmazások konfigurációjának importálása vagy exportálása
description: Megtudhatja, hogyan importálhat és exportálhat konfigurációs információkat az Azure-alkalmazások konfigurációjában vagy az alkalmazásból. Exchange-alapú adatcsere az alkalmazás konfigurációs tárolója és a kód projekt között.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: alkemper
ms.openlocfilehash: 5e4eeb37bb5efa11a656600072e4aa364fc71500
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96931792"
---
# <a name="import-or-export-configuration-data"></a>Konfigurációs adatok importálása vagy exportálása

Az Azure-alkalmazás konfigurációja támogatja az adatok importálási és exportálási műveleteit. Ezekkel a műveletekkel tömegesen dolgozhat a konfigurációs adatokkal, és az alkalmazások konfigurációs tárolója és a kód projektje között cserélheti az adatait. Beállíthat például egy alkalmazás-konfigurációs tárolót tesztelésre, egy másikat pedig éles környezetben. Átmásolhatja az alkalmazások beállításait, így nem kell kétszer megadnia az adatbevitelt.

Ez a cikk útmutatást nyújt az alkalmazások alkalmazás-konfigurációval történő importálásához és exportálásához. Ha egy folyamatos szinkronizálást szeretne beállítani a GitHub-tárházral, tekintse meg a [GitHub-műveletet](./concept-github-action.md).

## <a name="import-data"></a>Adatok importálása

Az importálással konfigurációs adatok importálhatók egy meglévő forrásból származó alkalmazás-konfigurációs tárolóba. Az importálás függvény használatával áttelepítheti az adatokat egy alkalmazás-konfigurációs tárolóba, vagy összesítheti a különböző forrásokból származó adatokat. Az alkalmazás konfigurációja támogatja az importálást JSON, YAML vagy Properties fájlból.

Az adatimportálás az [Azure Portal](https://portal.azure.com) vagy az [Azure CLI](./scripts/cli-import.md)használatával történhet. A Azure Portal hajtsa végre az alábbi lépéseket:

1. Keresse meg az alkalmazás konfigurációs tárolóját, és válassza az **Importálás/exportálás** lehetőséget az **Operations (műveletek** ) menüből.

1. Az **Importálás** lapon válassza a **forrás szolgáltatás**  >  **konfigurációs fájl** elemet.

1. Válassza ki **a nyelvet** , és válassza ki a kívánt bemeneti típust.

1. Válassza ki a **mappa** ikont, és tallózással keresse meg az importálni kívánt fájlt.

    ![Fájl importálása](./media/import-file.png)

1. Válasszon ki egy **elválasztó karaktert**, és opcionálisan adja meg az importált kulcsok nevének **előtagjaként használni kívánt előtagot** .

1. Opcionálisan kijelölhet egy **címkét**.

1. Az importálás befejezéséhez kattintson az **alkalmaz** gombra.

    ![Az importálandó fájl befejeződött](./media/import-file-complete.png)

## <a name="export-data"></a>Adatok exportálása

Az Exportálás az alkalmazás konfigurációjában tárolt konfigurációs adatot egy másik célhelyre írja. Az Exportálás függvénnyel például egy alkalmazás konfigurációs tárolójában lévő adatmentést egy olyan fájlba, amely az alkalmazás kódjába van beágyazva az üzembe helyezés során.

Az adatexportálás az [Azure Portal](https://portal.azure.com) vagy az [Azure CLI](./scripts/cli-export.md)használatával történhet. A Azure Portal hajtsa végre az alábbi lépéseket:

1. Keresse meg az alkalmazás konfigurációs tárolóját, és válassza az **Importálás/exportálás** lehetőséget.

1. Az **Exportálás** lapon válassza a **cél szolgáltatás**  >  **konfigurációs fájl** elemet.

1. Opcionálisan megadhat egy **előtagot** , és kiválaszthat egy **címkét** és egy időpontot az exportálandó kulcsok számára.

1. Válasszon ki egy **Fájltípus**-  >  **elválasztó** elemet.

1. Az Exportálás befejezéséhez kattintson az **alkalmaz** gombra.

    ![A fájl exportálása befejeződött](./media/export-file-complete.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [ASP.NET Core-webalkalmazás létrehozása](./quickstart-aspnet-core-app.md)