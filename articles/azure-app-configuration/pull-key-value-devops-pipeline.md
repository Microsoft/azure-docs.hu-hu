---
title: Settingsfromo-alkalmazások konfigurációjának lekérése az Azure-folyamatokkal
description: Ismerje meg, hogyan használhatja az Azure-folyamatokat a kulcsok értékének az alkalmazás-konfigurációs tárolóba való lekéréséhez
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 1c01984f6a359c0fd1f5d06d26d97d4a84973f57
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056787"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Beállítások lekérése az alkalmazás konfigurálásához az Azure-folyamatokkal

Az [Azure-alkalmazás konfigurációs](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) feladata lekéri a kulcs-értékeket az alkalmazás konfigurációs tárolójából, és Azure-feldolgozási változókként állítja be azokat, amelyeket a későbbi feladatok felhasználhatnak. Ez a feladat kiegészíti az [Azure app Configuration leküldéses](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) feladatot, amely egy konfigurációs fájl kulcs-értékeit leküldi az alkalmazás konfigurációs tárolójába. További információ: [leküldéses beállítások alkalmazása az alkalmazások konfigurálására az Azure-folyamatokkal](push-kv-devops-pipeline.md).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- Alkalmazás-konfigurációs tároló – hozzon létre egyet ingyen a [Azure Portal](https://portal.azure.com).
- Azure DevOps-projekt – [hozzon létre egyet ingyen](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure-alkalmazás konfigurációs feladata – ingyenesen letölthető a [Visual Studio piactérről](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.).  

## <a name="create-a-service-connection"></a>Szolgáltatási kapcsolatok létrehozása

A [szolgáltatási kapcsolatok](/azure/devops/pipelines/library/service-endpoints) lehetővé teszik az Azure-előfizetéshez tartozó erőforrások elérését az Azure DevOps-projektből.

1. Az Azure DevOps nyissa meg a cél folyamatot tartalmazó projektet, és a bal alsó sarokban nyissa meg a **projekt beállításait** .
1. A **folyamatok** területen válassza a **szolgáltatás kapcsolatai** lehetőséget.
1. Ha nem rendelkezik meglévő szolgáltatási kapcsolatokkal, kattintson a képernyő közepén található **szolgáltatás-kapcsolat létrehozása** gombra. Egyéb esetben kattintson az oldal jobb felső sarkában található **új szolgáltatás-összekötő** elemre.
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

## <a name="add-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Rendelje hozzá a megfelelő alkalmazás-konfigurációs szerepkört a feladaton belül használt szolgáltatási kapcsolathoz, hogy a feladat hozzáférhessen az alkalmazás konfigurációs tárolójához.

1. Navigáljon a cél alkalmazás konfigurációs tárolójához. Az alkalmazás konfigurációs tárolójának beállításával kapcsolatban lásd: alkalmazás- [konfigurációs tároló létrehozása](./quickstart-dotnet-core-app.md#create-an-app-configuration-store) az Azure app Configuration gyors üzembe helyezési útmutatójában.
1. A bal oldalon válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
1. A jobb oldalon kattintson a szerepkör- **hozzárendelések hozzáadása** gombra.
![Képernyőfelvétel: a szerepkör-hozzárendelések hozzáadása ](./media/add-role-assignment-button.png) gomb.
1. A **szerepkör** területen válassza **az alkalmazás-konfigurációs Adatolvasó** lehetőséget. Ez a szerepkör lehetővé teszi a feladat számára az alkalmazás-konfigurációs tárolóból való olvasást. 
1. Válassza ki az előző szakaszban létrehozott szolgáltatási kapcsolatban társított szolgáltatásnevet.
![Képernyőfelvétel: a szerepkör-hozzárendelés hozzáadása párbeszédpanel.](./media/add-role-assignment-reader.png)

> [!NOTE]
> Az alkalmazás-konfiguráción belüli Azure Key Vault-hivatkozások megoldásához a szolgáltatás kapcsolódásának engedélyt kell adni a titkos kulcsok olvasásához a hivatkozott Azure Key Vaultban.
  
## <a name="use-in-builds"></a>Használat a buildekben

Ez a szakasz bemutatja, hogyan használható az Azure-alkalmazás konfigurációs feladata egy Azure DevOps Build-folyamaton.

1. Navigáljon a folyamat létrehozása lapra a **folyamatok**  >  **folyamatai** elemre kattintva. A folyamat felépítésével kapcsolatos dokumentációért lásd:  [az első folyamat létrehozása](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser).
      - Ha új Build-folyamatot hoz létre, a folyamat utolsó lépése a **felülvizsgálat** lapon válassza a **Segéd megjelenítése** lehetőséget a folyamat jobb oldalán.
      ![Képernyőfelvétel: az új folyamathoz tartozó Segéd megjelenítése gomb.](./media/new-pipeline-show-assistant.png)
      - Ha meglévő Build-folyamatot használ, kattintson a jobb felső sarokban található **Szerkesztés** gombra.
      ![A képernyőképen egy meglévő folyamat szerkesztés gombja látható.](./media/existing-pipeline-show-assistant.png)
1. Keresse meg az **Azure-alkalmazás konfigurációs** feladatát.
![Képernyőfelvétel: a feladat hozzáadása párbeszédpanel az Azure-alkalmazás konfigurációjában a keresőmezőbe.](./media/add-azure-app-configuration-task.png)
1. Konfigurálja a szükséges paramétereket ahhoz, hogy a feladat lekérje a kulcs-értékeket az alkalmazás konfigurációs tárolójából. A paraméterek leírása az alábbi **Paraméterek** szakaszban és az egyes paraméterek melletti elemleírásokban érhető el.
      - Állítsa be az **Azure-előfizetési** paramétert az előző lépésben létrehozott szolgáltatási kapcsolatok nevére.
      - Állítsa be az **alkalmazás konfigurációjának nevét** az alkalmazás konfigurációs tárolójának az erőforrás nevére.
      - Hagyja meg az alapértelmezett értékeket a többi paraméternél.
![A képernyőképen az alkalmazás konfigurációs feladatának paraméterei láthatók.](./media/azure-app-configuration-parameters.png)
1. Hozzon létre egy buildet, és a várólistára. A létrehozási napló megjeleníti a feladat végrehajtása során bekövetkezett hibákat.

## <a name="use-in-releases"></a>Használat a kiadásokban

Ez a szakasz bemutatja, hogyan használható az Azure-alkalmazás konfigurációs feladata az Azure DevOps kiadási folyamatában.

1. Navigáljon a folyamat kiadása lapra a **folyamatok** kiadásai lehetőség kiválasztásával  >  . A kiadási folyamat dokumentációjában tekintse meg a [folyamatok kiadásával](/azure/devops/pipelines/release)foglalkozó témakört.
1. Válasszon ki egy meglévő kiadási folyamatot. Ha még nem rendelkezik ilyennel, kattintson az **új folyamat** elemre, és hozzon létre egy újat.
1. A kiadási folyamat szerkesztéséhez kattintson a jobb felső sarokban található **Szerkesztés** gombra.
1. A **feladatok** legördülő listából válassza ki azt a **szakaszt** , amelyhez hozzá szeretné adni a feladatot. További információt a szakaszokról [itt](/azure/devops/pipelines/release/environments)találhat.
![Képernyőfelvétel: a feladatok legördülő menüjében a kijelölt szakasz látható.](./media/pipeline-stage-tasks.png)
1. Kattintson **+** a Tovább gombra ahhoz a feladathoz, amelyhez új feladatot szeretne felvenni.
![Képernyőfelvétel: a feladatok melletti plusz gomb.](./media/add-task-to-job.png)
1. Keresse meg az **Azure-alkalmazás konfigurációs** feladatát.
![Képernyőfelvétel: a feladat hozzáadása párbeszédpanel az Azure-alkalmazás konfigurációjában a keresőmezőbe.](./media/add-azure-app-configuration-task.png)
1. Konfigurálja a szükséges paramétereket a feladaton belül, hogy lekérje a kulcs-értékeket az alkalmazás konfigurációs tárolójából. A paraméterek leírása az alábbi **Paraméterek** szakaszban és az egyes paraméterek melletti elemleírásokban érhető el.
      - Állítsa be az **Azure-előfizetési** paramétert az előző lépésben létrehozott szolgáltatási kapcsolatok nevére.
      - Állítsa be az **alkalmazás konfigurációjának nevét** az alkalmazás konfigurációs tárolójának az erőforrás nevére.
      - Hagyja meg az alapértelmezett értékeket a többi paraméternél.
1. Kiadás mentése és várólistára helyezése. A kiadási napló megjeleníti a feladat végrehajtása során felmerülő hibákat.

## <a name="parameters"></a>Paraméterek

Az Azure-alkalmazás konfigurációs feladata a következő paramétereket használja:

- **Azure-előfizetés**: egy legördülő lista, amely tartalmazza az elérhető Azure-szolgáltatások kapcsolatait. Az elérhető Azure-szolgáltatási kapcsolatok listájának frissítéséhez és frissítéséhez kattintson az **Azure-előfizetés frissítése** gombra a szövegmezőtől jobbra.
- **Alkalmazás konfigurációja neve**: egy legördülő lista, amely betölti az elérhető konfigurációs tárolókat a kijelölt előfizetésben. Az elérhető konfigurációs tárolók listájának frissítéséhez és frissítéséhez kattintson az **alkalmazás konfigurációjának frissítése** gombra a szövegmezőtől jobbra.
- **Kulcs szűrő**: a szűrő segítségével kiválaszthatja, hogy milyen kulcs-értékeket kér az Azure-alkalmazás konfigurációja. A * érték * kijelöli az összes kulcs-értéket. További információ: [lekérdezési kulcs értékei](concept-key-value.md#query-key-values).
- **Label**: Megadja, hogy melyik címkét kell használni, amikor az alkalmazás konfigurációs tárolójából kiválasztja a kulcs-érték elemet. Ha nincs megadva címke, a rendszer lekéri a nem címkével ellátott kulcs-értékeket. A következő karakterek nem engedélyezettek:, *.
- **Vágási kulcs előtagja**: egy vagy több olyan előtagokat ad meg, amelyeket az alkalmazás konfigurációs kulcsaiból kell kimetszeni, mielőtt változóként beállítja őket. Több előtag is elválasztható egy új sor karakterrel.

## <a name="use-key-values-in-subsequent-tasks"></a>A következő feladatokban szereplő kulcs-értékek használata

Az alkalmazás-konfigurációból beolvasott kulcs-értékek feldolgozási változóként vannak beállítva, amelyek környezeti változókként érhetők el. A környezeti változó kulcsa az alkalmazás-konfigurációból beolvasott kulcs értéke az előtag kivágása után, ha meg van adva.

Ha például egy későbbi feladat PowerShell-parancsfájlt futtat, akkor a következőhöz hasonló kulcs-érték használható: "myBuildSetting".
```powershell
echo "$env:myBuildSetting"
```
És a rendszer kinyomtatja az értéket a konzolon.

> [!NOTE]
> Az alkalmazás-konfiguráción belüli Azure Key Vault-hivatkozások feloldhatók és [titkos változókként](/azure/devops/pipelines/process/variables#secret-variables)lesznek beállítva. Az Azure-folyamatokban a titkos változók a naplóból vannak kitakarva. Környezeti változókként nem lesznek átadva a feladatoknak, és ehelyett bemenetként kell átadni őket. 

## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem várt hiba történik, a hibakeresési naplók engedélyezéséhez állítsa a folyamat változót a következőre: `system.debug` `true` .

## <a name="faq"></a>GYIK

**Hogyan a konfigurációt több kulcsból és címkéből is összeállítani?**

Időnként előfordulhat, hogy a konfigurációnak több címkéből kell állnia, például az alapértelmezett és a fejlesztői. Egy folyamaton belül több alkalmazás-konfigurációs feladat is használható a forgatókönyv megvalósításához. Egy későbbi lépésben egy tevékenység által beolvasott kulcs-értékek felülírják az előző lépések összes értékét. A fenti példában egy feladat használatával kiválaszthatja az alapértelmezett címkével rendelkező kulcs-értékeket, míg egy második feladat kiválaszthatja a kulcs-értékeit a fejlesztői címkével. A fejlesztői címkével rendelkező kulcsok felülbírálják az alapértelmezett címkével megegyező kulcsokat.
