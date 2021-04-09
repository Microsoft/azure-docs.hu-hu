---
title: Cloud Services használata (bővített támogatás) (előzetes verzió)
description: Ismerje meg, hogyan hozhat létre és helyezhet üzembe Azure Cloud Service-t a Visual Studio Azure Resource Manager használatával
author: ghogen
ms.service: cloud-services-extended-support
manager: jillfra
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: tutorial
ms.date: 10/5/2020
ms.author: ghogen
ms.openlocfilehash: 80aa160c53b278137467dba2afa41384c7c4f378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101722670"
---
# <a name="create-and-deploy-a-azure-cloud-service-extended-support-using-visual-studio"></a>Azure Cloud Service (bővített támogatás) létrehozása és üzembe helyezése a Visual Studióval

A [Visual Studio 2019 16,9](https://visualstudio.microsoft.com/vs/preview/) -es verziója (jelenleg előzetes verzióban érhető el) a Cloud services segítségével Azure Resource Manager (ARM) használatával dolgozhat, ami nagy mértékben leegyszerűsíti és modernizálja az Azure-erőforrások karbantartását és kezelését. Ezt olyan új Azure-szolgáltatás engedélyezi, amely Cloud Services (kiterjesztett támogatás) néven is ismert. Egy meglévő felhőalapú szolgáltatást Cloud Services (kiterjesztett támogatás) tehet közzé. További információ erről az Azure-szolgáltatásról: [Cloud Services (bővített támogatás) dokumentáció](overview.md).

> [!IMPORTANT]
> A Cloud Services (bővített támogatás) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-feature-for-your-subscription"></a>Az előfizetéshez tartozó funkció regisztrálása
A Cloud Services (kiterjesztett támogatás) jelenleg előzetes verzióban érhető el. Regisztrálja az előfizetéshez tartozó szolgáltatást a következőképpen:

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```
További információ: [Cloud Services üzembe helyezésének előfeltételei (kiterjesztett támogatás)](deploy-prerequisite.md)

## <a name="create-a-project"></a>Projekt létrehozása

A Visual Studio egy olyan projektfájlt biztosít, amely lehetővé teszi, hogy egy **Azure Cloud Service (bővített támogatás)** nevű Azure Cloud-szolgáltatást hozzon létre a kiterjesztett támogatással. A Cloud Service egy egyszerű, általános célú Azure-szolgáltatás. A projekt létrehozása után a Visual Studio lehetővé teszi a felhőalapú szolgáltatás konfigurálását, hibakeresését és üzembe helyezését az Azure-ban.

### <a name="to-create-an-azure-cloud-service-extended-support-project-in-visual-studio"></a>Azure Cloud Service (bővített támogatás) projekt létrehozása a Visual Studióban

Ez a szakasz végigvezeti egy Azure Cloud Service-projekt létrehozásán a Visual Studióban egy vagy több webes szerepkörrel.

1. A Start ablakban válassza az **új projekt létrehozása** lehetőséget.

1. A keresőmezőbe írja be a *felhő* kifejezést, majd válassza az **Azure Cloud Service (bővített támogatás)** lehetőséget.

   ![Új Azure Cloud Service kiterjesztett támogatással](./media/choose-project-template.png)

1. Adjon nevet a projektnek, és válassza a **Létrehozás** lehetőséget.

   ![Adja meg a projekt nevét](./media/configure-new-project.png)

1. Az **új Microsoft Azure Cloud Service** párbeszédpanelen válassza ki a hozzáadni kívánt szerepköröket, majd a jobbra mutató nyílra kattintva adja hozzá őket a megoldáshoz.

    ![Új Azure Cloud Service-szerepkörök kiválasztása](./media/choose-roles.png)

1. Egy hozzáadott szerepkör átnevezéséhez vigye az egérmutatót a szerepkörre az **új Microsoft Azure Cloud Service** párbeszédpanelen, és a helyi menüben válassza az **Átnevezés** elemet. A megoldáson belüli szerepkört is átnevezheti (a **megoldáskezelő**) a hozzáadása után.

    ![Azure Cloud Service-szerepkör átnevezése](./media/new-cloud-service-rename.png)

A Visual Studio Azure Project társításokkal rendelkezik a megoldásban szereplő szerepkör-projektekhez. A projekt tartalmazza a *szolgáltatás-definíció fájlját* és a *szolgáltatás konfigurációs fájlját* is:

- **Szolgáltatás-definíciós fájl** – meghatározza az alkalmazás futásidejű beállításait, beleértve a szükséges szerepköröket, a végpontokat és a virtuális gépek méretét.
- **Szolgáltatás-konfigurációs fájl** – beállítja, hogy egy szerepkör hány példánya fusson, és milyen beállítások vannak definiálva a szerepkörhöz.

További információ ezekről a fájlokról: [Azure Cloud Service-szerepkörök konfigurálása a Visual Studióval](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service).

## <a name="publish-a-cloud-service"></a>Felhőalapú szolgáltatás közzététele

1. Hozzon létre vagy nyisson meg egy Azure Cloud Service-projektet a Visual Studióban.

1. **Megoldáskezelő** kattintson a jobb gombbal a projektre, és a helyi menüben válassza a **Közzététel** lehetőséget.

   ![Bejelentkezési oldal](./media/publish-step-1.png)

1. **Fiók** – válasszon ki egy fiókot, vagy válassza a fiók **hozzáadása** lehetőséget a fiók legördülő listában.

1. **Válassza ki az előfizetést** – válassza ki az üzemelő példányhoz használni kívánt előfizetést. A Cloud Services üzembe helyezéséhez használt előfizetés (kiterjesztett támogatás) tulajdonosi vagy közreműködői szerepkörökkel kell rendelkeznie a szerepköralapú hozzáférés-vezérlés (RBAC) használatával. Ha az előfizetése nem rendelkezik ezekkel a szerepkörökkel, a folytatás előtt tekintse meg [a szerepkör-hozzárendelés hozzáadásához szükséges lépéseket](../role-based-access-control/role-assignments-steps.md) .

1. Kattintson a **tovább** gombra a **Beállítások** lapra való áttéréshez.

   ![Általános beállítások](./media/publish-settings.png)

1. **Cloud Service** – a legördülő listából válasszon ki egy meglévő felhőalapú szolgáltatást, vagy válassza az **új létrehozása** lehetőséget, és hozzon létre egy felhőalapú szolgáltatást. Az adatközpont minden felhőalapú szolgáltatás esetében zárójelben jelenik meg. Azt javasoljuk, hogy a felhőalapú szolgáltatás Adatközpont-helye azonos legyen a Storage-fiók adatközpontjának helyével.

   Ha úgy dönt, hogy létrehoz egy új felhőalapú szolgáltatást, megjelenik a **Cloud Service létrehozása (bővített támogatás)** párbeszédpanel. Itt adhatja meg a felhőalapú szolgáltatáshoz használni kívánt helyet és erőforráscsoportot.

   ![Felhőalapú szolgáltatás létrehozása kiterjesztett támogatással](./media/extended-support-dialog.png)

1. **Konfiguráció létrehozása** – válassza a **hibakeresés** vagy a **kiadás** lehetőséget.

1. **Szolgáltatás konfigurációja** – válassza a **felhő** vagy a **helyi** lehetőséget.

1. **Storage-fiók** – válassza ki a központi telepítéshez használni kívánt Storage-fiókot, vagy **hozzon létre új** fiókot a Storage-fiók létrehozásához. A régió minden egyes Storage-fiók esetében zárójelben jelenik meg. Azt javasoljuk, hogy a Storage-fiók Adatközpont-helye azonos legyen a Cloud Service adatközpontjának helyével (közös beállítások).

   Az Azure Storage-fiók tárolja a csomagot az alkalmazás központi telepítéséhez. Az alkalmazás telepítése után a csomag el lesz távolítva a Storage-fiókból.

1. **Key Vault** – a felhőalapú szolgáltatás titkos kulcsait tartalmazó Key Vault megadása. Ez akkor engedélyezett, ha a távoli asztal engedélyezve van, vagy a tanúsítványok hozzá vannak adva a konfigurációhoz.

1. **Távoli asztal engedélyezése minden szerepkörhöz** – válassza ezt a lehetőséget, ha távolról szeretne csatlakozni a szolgáltatáshoz. A rendszer kérni fogja a hitelesítő adatok megadását.

   ![Távoli asztal beállításai](./media/remote-desktop-configuration.png)

1. Kattintson a **tovább** gombra a **diagnosztikai beállítások** lapra való áttéréshez.

   ![Diagnosztikai beállítások](./media/diagnostics-settings.png)

   A diagnosztika lehetővé teszi az Azure Cloud Service (vagy Azure-beli virtuális gép) hibáinak megoldását. További információ a diagnosztika szolgáltatásról: [a diagnosztika konfigurálása az Azure Cloud Services és Virtual Machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines). További információ a Application Insightsről: [Mi az Application Insights?](../azure-monitor/app/app-insights-overview.md)

1. Az **Összefoglalás** lapra való áttéréshez kattintson a **tovább** gombra.

   ![Összefoglalás](./media/publish-summary.png)

1. **Cél profil** – létrehozhat egy közzétételi profilt a választott beállítások közül. Létrehozhat például egy profilt egy tesztkörnyezetben, egy másikat pedig az éles környezetben. A profil mentéséhez válassza a **Mentés** ikont. A varázsló létrehozza a profilt, és menti azt a Visual Studio-projektben. A profil nevének módosításához nyissa meg a **cél profil** listát, majd kattintson a **kezelés...** elemre.

   > [!Note]
   > A közzétételi profil megjelenik Megoldáskezelő a Visual Studióban, és a Profilbeállítások egy. azurePubxml kiterjesztésű fájlba íródnak. A beállítások XML-címkék attribútumaiként lesznek mentve.

1. Miután konfigurálta a projekt központi telepítésének összes beállítását, válassza a **Közzététel** elemet a párbeszédpanel alján. A folyamat állapotát az **Azure-tevékenység naplójának** kimenete ablakban követheti a Visual Studióban.

Gratulálunk! Közzétette a kiterjesztett támogatási Cloud Service-projektet az Azure-ban. Ha újra közzé szeretné tenni ugyanezeket a beállításokat, újra felhasználhatja a közzétételi profilt, vagy megismételheti ezeket a lépéseket egy új létrehozásához.

## <a name="clean-up-azure-resources"></a>Azure-erőforrások karbantartása

Az oktatóanyag alapján létrehozott Azure-erőforrások törléséhez nyissa meg a [Azure Portal](https://portal.azure.com), válassza az **erőforráscsoportok** lehetőséget, keresse meg és nyissa meg a szolgáltatás létrehozásához használt erőforráscsoportot, majd válassza az **erőforráscsoport törlése** elemet.

## <a name="next-steps"></a>Következő lépések

Állítsa be a folyamatos integrációt (CI) a **közzétételi** képernyőn a **configure (Konfigurálás** ) gomb használatával. További információt az Azure- [folyamatok dokumentációjában](/azure/devops/pipelines)talál.