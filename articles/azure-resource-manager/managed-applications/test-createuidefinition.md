---
title: A felhasználói felület definíciós fájljának tesztelése
description: Ez a cikk azt ismerteti, hogyan tesztelhető a felhasználói felület az Azure Managed Application portálon keresztüli létrehozásához.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: eeef454e4c5706b39d07261ade1c2f0ffbc942ad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478896"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Tesztelje a portál felületét a Azure Managed Applications

Miután [létrehozta createUiDefinition.jsfelügyelt](create-uidefinition-overview.md) alkalmazás fájljában, tesztelni kell a felhasználói élményt. A tesztelés leegyszerűsítése érdekében használjon egy tesztkörnyezetet, amely betölti a fájlt a portálon. Nem kell ténylegesen üzembe helyeznie a felügyelt alkalmazást. A sandbox az aktuális, teljes képernyős portálon mutatja be a felhasználói felületet. Vagy használhat szkriptet is a felület tesztelésére. Ebben a cikkben mindkét megközelítés látható. A felület előnézetének ajánlott módja a védőfal.

## <a name="prerequisites"></a>Előfeltételek

* Egy **createUiDefinition.jsfájlban.** Ha nem tudja ezt a fájlt, másolja ki a [mintafájlt.](https://github.com/Azure/azure-quickstart-templates/blob/master/demos/100-marketplace-sample/createUiDefinition.json)

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="use-sandbox"></a>A sandbox használata

1. Nyissa meg [a Felhasználói felület definíciójának létrehozása sandboxot.](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)

   ![A sandbox megjelenítése](./media/test-createuidefinition/show-sandbox.png)

1. Cserélje le az üres definíciót a fájlban createUiDefinition.jstartalmára. Válassza az **Előnézet lehetőséget.**

   ![Előnézet kiválasztása](./media/test-createuidefinition/select-preview.png)

1. Megjelenik a létrehozott űrlap. Végigléptetheti a felhasználói élményt, és kitöltheti az értékeket.

   ![Űrlap megjelenítése](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Hibaelhárítás

Ha az űrlap nem jelenik meg az Előnézet kiválasztása után, szintaktikai hiba jelenhet meg. Keresse meg a piros jelzőt a jobb görgetősávon, és navigáljon hozzá.

![Szintaktikai hiba megjelenítése](./media/test-createuidefinition/show-syntax-error.png)

Ha az űrlap nem jelenik meg, és ehelyett egy felhő ikonja jelenik meg a leszakadással, az űrlap hibaüzenetet kap, például egy hiányzó tulajdonságot. Nyissa meg a Fejlesztői eszközök a böngészőben. A **konzol** fontos üzeneteket jelenít meg a felülettel kapcsolatban.

![Hiba mutatása](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Tesztelési szkript használata

A felület a portálon való teszteléshez másolja az alábbi szkriptek egyikét a helyi gépre:

* [PowerShell side-load script – Az Module](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell- oldalbetöltési szkript – Azure-modul](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI – oldalbetöltési szkript](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

A felületfájl a portálon való megnyitásához futtassa a letöltött szkriptet. A szkript létrehoz egy tárfiókot az Azure-előfizetésében, és feltölti createUiDefinition.jsa fájlban található adatokat a tárfiókba. A tárfiók a szkript első futtatásakor vagy a tárfiók törlésekor jön létre. Ha a tárfiók már létezik az Azure-előfizetésében, a szkript újra felhasználja azt. A szkript megnyitja a portált, és betölti a fájlt a tárfiókból.

Adja meg a tárfiók helyét, és adja meg azt a mappát, createUiDefinition.jsfájlban található.

PowerShell esetén használja az alábbi parancsot:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Azure CLI esetén használja az alábbi parancsot:

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Ha createUiDefinition.jsfájlban lévő fájl ugyanabban a mappában van, mint a szkript, és már létrehozta a tárfiókot, nem kell meg adnia ezeket a paramétereket.

PowerShell esetén használja az alábbi parancsot:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Azure CLI esetén használja az alábbi parancsot:

```bash
./sideload-createuidef.sh
```

A szkript egy új lapot nyit meg a böngészőben. Megjeleníti a portált a felügyelt alkalmazás létrehozásához szükséges felülettel.

Adja meg a mezők értékeit. Amikor végzett, láthatja a sablonnak átadott értékeket, amelyek a böngésző fejlesztői eszközeinek konzolján találhatók.

![Értékek megjelenítése](./media/test-createuidefinition/show-json.png)

Ezeket az értékeket használhatja paraméterfájlként az üzembe helyezési sablon teszteléséhez.

Ha a portál lefagy az összefoglaló képernyőn, a kimeneti szakaszban hiba lehet. Előfordulhat például, hogy olyan vezérlőre hivatkozott, amely nem létezik. Ha a kimenet egyik paramétere üres, előfordulhat, hogy a paraméter egy nem létező tulajdonságra hivatkozik. A vezérlőre való hivatkozás például érvényes, de a tulajdonsághivatkozás nem érvényes.

## <a name="test-your-solution-files"></a>A megoldásfájlok tesztelése

Most, hogy ellenőrizte, hogy a portál felülete a várt módon működik-e, ideje ellenőrizni, hogy a createUiDefinition fájl megfelelően van-e mainTemplate.jsfájlba. Egy érvényesítési szkriptteszt futtatásával tesztelheti a megoldásfájlok tartalmát, beleértve a createUiDefinition fájlt is. A szkript ellenőrzi a JSON-szintaxist, reguláris kifejezéseket keres a szövegmezőkben, és ellenőrzi, hogy a portálfelület kimeneti értékei megegyeznek-e a sablon paramétereivel. A szkript futtatásával kapcsolatos információkért lásd: [Statikus érvényesítési ellenőrzések futtatása sablonokhoz.](https://aka.ms/arm-ttk)

## <a name="next-steps"></a>Következő lépések

A portál felületének érvényességét követően megtudhatja, hogyan teszi elérhetővé azure-beli felügyelt alkalmazását [a Marketplace-en.](../../marketplace/create-new-azure-apps-offer.md)
