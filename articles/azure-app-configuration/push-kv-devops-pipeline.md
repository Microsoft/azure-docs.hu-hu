---
title: Beállítások leküldése az alkalmazás konfigurációjához az Azure-folyamatokkal
description: Ismerje meg, hogyan használhatja az Azure-folyamatokat kulcs-értékek küldésére az alkalmazás konfigurációs tárolójába
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 02/23/2021
ms.author: alkemper
ms.openlocfilehash: e1a4fb52a5f9622758e9ed805bf9380f5f608870
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068252"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Beállítások leküldése az alkalmazás konfigurációjához az Azure-folyamatokkal

Az [Azure-alkalmazás konfigurálása leküldéses](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) feladat leküldi a kulcs-értékeket egy konfigurációs fájlból az alkalmazás konfigurációs tárolójába. Ez a feladat engedélyezi a teljes kört a folyamaton belül, mivel most már képes a beállítások lekérésére az alkalmazás konfigurációs tárolójából, valamint leküldéses beállításokat az alkalmazás konfigurációs tárolójába.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- Alkalmazás-konfigurációs erőforrás – hozzon létre egyet ingyen a [Azure Portal](https://portal.azure.com).
- Azure DevOps-projekt – [hozzon létre egyet ingyen](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure-alkalmazás konfigurálása leküldéses feladat – ingyenesen letölthető a [Visual Studio piactérről](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push).

## <a name="create-a-service-connection"></a>Szolgáltatási kapcsolatok létrehozása

A [szolgáltatási kapcsolatok](/azure/devops/pipelines/library/service-endpoints) lehetővé teszik az Azure-előfizetéshez tartozó erőforrások elérését az Azure DevOps-projektből.

1. Az Azure DevOps nyissa meg a cél folyamatot tartalmazó projektet, és a bal alsó sarokban nyissa meg a **projekt beállításait** .
1. A **folyamatok** területen válassza a **szolgáltatás kapcsolatai** lehetőséget, majd a jobb felső sarokban válassza az **új szolgáltatás-kapcsolat** lehetőséget.
1. Válassza a **Azure Resource Manager** lehetőséget.
![Képernyőfelvétel: a Azure Resource Manager kiválasztása az új szolgáltatási kapcsolatok legördülő listából.](./media/new-service-connection.png)
1. A **hitelesítési módszer** párbeszédpanelen válassza az **egyszerű szolgáltatásnév (automatikus)** lehetőséget.
    > [!NOTE]
    > A **felügyelt identitások** hitelesítése jelenleg nem támogatott az alkalmazás konfigurációs feladatához.
1. Töltse ki az előfizetést és az erőforrást. Adjon nevet a szolgáltatásnak.

Most, hogy létrejött a szolgáltatás-Kapcsolódás, keresse meg a hozzá rendelt egyszerű szolgáltatásnév nevét. A következő lépésben új szerepkör-hozzárendelést fog hozzáadni ehhez a szolgáltatáshoz.

1. Lépjen a **Project Settings**  >  **szolgáltatás kapcsolataihoz**.
1. Válassza ki az előző szakaszban létrehozott szolgáltatási kapcsolatokat.
1. Válassza az **egyszerű szolgáltatás kezelése** lehetőséget.
1. Jegyezze fel a **megjelenített nevet** .
![Képernyőfelvétel: az egyszerű szolgáltatásnév megjelenített neve.](./media/service-principal-display-name.png)

## <a name="add-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Rendelje hozzá a megfelelő alkalmazás-konfigurációs szerepkör-hozzárendeléseket a feladatban használt hitelesítő adatokhoz, így a feladat elérheti az alkalmazás konfigurációs tárolóját.

1. Navigáljon a cél alkalmazás konfigurációs tárolójához. 
1. A bal oldalon válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
1. A jobb oldalon kattintson a szerepkör- **hozzárendelések hozzáadása** gombra.
![Képernyőfelvétel: a szerepkör-hozzárendelések hozzáadása gomb.](./media/add-role-assignment-button.png)
1. A **szerepkör** területen válassza az **alkalmazás-konfigurációs adat tulajdonosa** elemet. Ez a szerepkör lehetővé teszi a feladat számára, hogy beolvassa az alkalmazást, és beírja az alkalmazás konfigurációs tárolójába. 
1. Válassza ki az előző szakaszban létrehozott szolgáltatási kapcsolatban társított szolgáltatásnevet.
![Képernyőfelvétel: a szerepkör-hozzárendelés hozzáadása párbeszédpanel.](./media/add-role-assignment.png)

  
## <a name="use-in-builds"></a>Használat a buildekben

Ez a szakasz bemutatja, hogyan használható az Azure app Configuration leküldéses feladat az Azure DevOps Build-folyamatokban.

1. Navigáljon a folyamat létrehozása lapra a **folyamatok**  >  **folyamatai** elemre kattintva. A folyamatok létrehozásához szükséges dokumentáció [itt](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2)található.
      - Ha új Build-folyamatot hoz létre, a folyamat utolsó lépése a **felülvizsgálat** lapon válassza a **Segéd megjelenítése** lehetőséget a folyamat jobb oldalán.
      ![Képernyőfelvétel: az új folyamathoz tartozó Segéd megjelenítése gomb.](./media/new-pipeline-show-assistant.png)
      - Ha meglévő Build-folyamatot használ, kattintson a jobb felső sarokban található **Szerkesztés** gombra.
      ![A képernyőképen egy meglévő folyamat szerkesztés gombja látható.](./media/existing-pipeline-show-assistant.png)
1. Keresse meg az **Azure app Configuration leküldéses** feladatot.
![Képernyőfelvétel: a feladat hozzáadása párbeszédpanel az Azure app Configuration leküldése a keresőmezőbe.](./media/add-azure-app-configuration-push-task.png)
1. Adja meg a szükséges paramétereket ahhoz, hogy a feladat a kulcs-értékeket a konfigurációs fájlból az alkalmazás konfigurációs tárolójába küldje. A paraméterek magyarázatai az alábbi **Paraméterek** szakaszban, valamint az egyes paraméterek melletti elemleírásokban érhetők el.
![Képernyőfelvétel: az alkalmazás konfigurációjának leküldéses tevékenységének paraméterei.](./media/azure-app-configuration-push-parameters.png)
1. Hozzon létre egy buildet, és a várólistára. A létrehozási napló megjeleníti a feladat végrehajtása során bekövetkezett hibákat.

## <a name="use-in-releases"></a>Használat a kiadásokban

Ez a szakasz bemutatja, hogyan használható az Azure app Configuration leküldéses feladat egy Azure DevOps kiadási folyamatokban.

1. Navigáljon a folyamat kiadása lapra a **folyamatok** kiadásai lehetőség kiválasztásával  >  . A kiadási folyamatok dokumentációja [itt](/azure/devops/pipelines/release)található.
1. Válasszon ki egy meglévő kiadási folyamatot. Ha még nem rendelkezik ilyennel, válassza az **+ új** lehetőséget, hogy újat hozzon létre.
1. A kiadási folyamat szerkesztéséhez kattintson a jobb felső sarokban található **Szerkesztés** gombra.
1. A **feladatok** legördülő listából válassza ki azt a **szakaszt** , amelyhez hozzá szeretné adni a feladatot. További információt a szakaszokról [itt](/azure/devops/pipelines/release/environments)találhat.
![Képernyőfelvétel: a feladatok legördülő menüjében a kijelölt szakasz látható.](./media/pipeline-stage-tasks.png)
1. Kattintson **+** a Tovább gombra ahhoz a feladathoz, amelyhez új feladatot szeretne felvenni.
![Képernyőfelvétel: a feladatok melletti plusz gomb.](./media/add-task-to-job.png)
1. A **feladatok hozzáadása** párbeszédpanelen írja be az **Azure-alkalmazás konfigurációjának leküldését** a keresőmezőbe, és jelölje ki.
1. Konfigurálja a szükséges paramétereket a feladaton belül, hogy a kulcs-értékeket a konfigurációs fájlból az alkalmazás konfigurációs tárolójába küldje el. A paraméterek magyarázatai az alábbi **Paraméterek** szakaszban, valamint az egyes paraméterek melletti elemleírásokban érhetők el.
1. Kiadás mentése és várólistára helyezése. A kiadási napló megjeleníti a feladat végrehajtása során felmerülő hibákat.

## <a name="parameters"></a>Paraméterek

Az alkalmazás-konfiguráció leküldéses feladata a következő paramétereket használja:

- **Azure-előfizetés**: egy legördülő lista, amely tartalmazza az elérhető Azure-szolgáltatások kapcsolatait. Az elérhető Azure-szolgáltatási kapcsolatok listájának frissítéséhez és frissítéséhez kattintson az **Azure-előfizetés frissítése** gombra a szövegmezőtől jobbra.
- **Alkalmazás konfigurációja neve**: egy legördülő lista, amely betölti az elérhető konfigurációs tárolókat a kijelölt előfizetésben. Az elérhető konfigurációs tárolók listájának frissítéséhez és frissítéséhez kattintson az **alkalmazás konfigurációjának frissítése** gombra a szövegmezőtől jobbra.
- **Konfigurációs fájl elérési útja**: a konfigurációs fájl elérési útja. A **konfigurációs fájl elérési útja** paraméter a fájl tárházának gyökerénél kezdődik. A konfigurációs fájl kiválasztásához Böngésszen a Build-összetevőn. ( `...` a szövegmezőtől jobbra látható gomb). A támogatott fájlformátumok a következők: YAML, JSON, Properties. A következő példában egy JSON formátumú konfigurációs fájl szerepel.
    ```json
    {
        "TestApp:Settings:BackgroundColor":"#FFF",
        "TestApp:Settings:FontColor":"#000",
        "TestApp:Settings:FontSize":"24",
        "TestApp:Settings:Message": "Message data"
    }
    ```
- **Elválasztó**: a. JSON-és. YML-fájlok összevonásához használt elválasztó.
- **Mélység**: az a mélység, amelyet a. JSON-és a. YML-fájlok összeolvasztása végez.
- **Előtag**: az alkalmazás-konfigurációs tárolóba leküldett kulcsok elejéhez fűzött karakterlánc.
- **Label (címke**): az egyes kulcs-értékekhez hozzáadott karakterlánc, amely az alkalmazás konfigurációs tárolójában található címke.
- **Content Type (tartalomtípus**): az egyes kulcs-értékekhez hozzáadott karakterlánc, amely az alkalmazás konfigurációs tárolójában található tartalomtípus.
- **Címkék**: egy JSON-objektum a (z `{"tag1":"val1", "tag2":"val2"}` ) formátumban, amely meghatározza az alkalmazás-konfigurációs tárolóba leküldett minden egyes kulcshoz hozzáadott címkéket.
- **A tárolóban lévő összes többi Key-Values törlése a megadott előtaggal és címkével: az** alapértelmezett érték nincs **bejelölve**.
  - **Bejelölve**: eltávolítja az alkalmazás-konfigurációs tároló összes olyan kulcs-értékét, amely megegyezik a megadott előtaggal és címkével, mielőtt az új kulcs-értékeket kikényszeríti a konfigurációs fájlból.
  - **Nincs bejelölve**: leküldi a konfigurációs fájl összes kulcs-értékét az alkalmazás konfigurációs tárolójába, és az alkalmazás konfigurációs tárolójában lévő minden más elemet érintetlenül hagy.



## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem várt hiba történik, a hibakeresési naplók engedélyezéséhez állítsa a folyamat változót a következőre: `system.debug` `true` .

## <a name="faq"></a>GYIK

**Hogyan tölthetők fel több konfigurációs fájl?**

Az Azure app Configuration leküldéses feladat több példányának létrehozása ugyanazon a folyamaton belül több konfigurációs fájl leküldéséhez az alkalmazás konfigurációs tárolójába.

**Miért kapok 409-es hibát, amikor megpróbálok leküldeni a kulcs-értékeket a konfigurációs tárolóba?**

409 ütközési hibaüzenet jelenik meg, ha a feladat egy olyan kulcs-érték törlését vagy felülírását kísérli meg, amely zárolva van az alkalmazás konfigurációs tárolójában.
