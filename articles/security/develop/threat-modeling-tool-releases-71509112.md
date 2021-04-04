---
title: Microsoft Threat Modeling Tool kiadás 9/12/2018
titleSuffix: Azure
description: Olvassa el a 9/12/2018-on közzétett Microsoft Threat Modeling Tool kibocsátási megjegyzéseit. A megjegyzések közé tartoznak a funkciók változásai és a hibajavítások.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: 7b0afdd90f2a1413c7f8364fc4518c4d1116454a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913585"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Threat Modeling Tool GA kiadás 7.1.50911.2-9/12/2018

Örömmel jelentjük be, hogy a Microsoft Threat Modeling Tool mostantól támogatott általánosan elérhető (GA) kiadásként tölthető le. Ez a kiadás fontos adatvédelmi és biztonsági frissítéseket, valamint hibajavításokat, szolgáltatás-frissítéseket és stabilitási javításokat tartalmaz. A rendszer a 2017 előzetes verziójának meglévő felhasználóinak frissítését kéri a ClickOnce-technológián keresztül a-ügyfél megnyitását követően. Az eszköz új felhasználói számára [letöltheti az ügyfelet](https://aka.ms/threatmodelingtool).

Ebben a kiadásban a 2017 előzetes verziójának támogatása megszűnik, és az előzetes frissítés összes felhasználóját ajánljuk a GA kiadásra. Az 15 2018-as vagy későbbi verziókban a Threat Modeling Tool minimálisan szükséges ClickOnce-verzióját állítja be, és a frissítéshez minden előzetes verziójú ügyfélre szükség lesz.

A [Microsoft letöltőközpontból](https://www.microsoft.com/en-us/download/details.aspx?id=49168)elérhető Microsoft Threat Modeling Tool 2016 a csak a kritikus biztonsági javítások esetében 1 2019 támogatott.

## <a name="feature-changes"></a>Szolgáltatások módosításai

### <a name="azure-stencil-updates"></a>Azure-rajzsablon frissítései

További Azure-rajzsablonok és a hozzájuk kapcsolódó fenyegetések és enyhítések lettek hozzáadva ehhez a kiadáshoz a stencil set szállításhoz. Jelentős változások történtek az "Azure App Services", az "Azure Database-ajánlatok" és az "Azure Storage" Kiemelt területein.

![Azure-rajzsablon frissítései](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>OneDrive-integrációs funkció eltávolítva

A "Mentés a OneDrive", a "Megnyitás a OneDrive" és a "hivatkozás megosztása" funkció el lett távolítva. A OneDrive felhasználói a Microsoft [OneDrive for Windows](https://onedrive.live.com/about/en-us/download/) Client használatával férhetnek hozzá a OneDrive tárolt fájlokhoz a szabványos fájl mentése és megnyitása párbeszédpaneleken.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Az ügyfelek által jelentett jelentős hibajavítások

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>A TMT előzetes verziójában az eszköz összeomlik a standard sablon használatakor

- Ha egy általános rajzsablon (például "általános adatfolyam") bekerül a rajzolási felületre, és fenyegetéseket hoz létre, akkor az eszköz összeomlhat. A probléma kijavítva.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>A TMT előzetes verziójában a jelentések mentésekor vagy a fenyegetések másolásakor a kockázati szintek helytelenek.

- Ha a felhasználó módosítja az adott fenyegetések kockázati szintjét, majd menti a jelentést, vagy átmásolja a kockázatokat, a kockázati szint visszatérhet a "magas" értékre. A probléma kijavítva.

## <a name="known-issues-and-faq"></a>Ismert problémák és gyakori kérdések

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>A nagy felbontású képernyők felhasználói kis méretű szövegeket tapasztalhatnak a veszélyforrás tulajdonságaiban

#### <a name="issue"></a>Probléma

Az eszköz Analysis nézetében, ha a felhasználó egy nagy felbontású képernyőt tartalmaz, amely alapértelmezés szerint be van állítva az olvashatóság érdekében a Windowsban, a fenyegetés lehetséges enyhítési szakasza kis szöveggel is megjelenhet.

![Ismert probléma a nagy felbontású képernyőkkel](./media/threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Áthidaló megoldás

A felhasználó rákattinthat a kockázatcsökkentő szövegre, és a normál Windows nagyítás-vezérléssel (Ctrl-egér görgővel) növelheti a szakasz nagyítását.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Előfordulhat, hogy a főablak "nemrég megnyitott modellek" szakaszában lévő fájlok nem nyithatók meg

#### <a name="issue"></a>Probléma

Az előzetes verzió "Megnyitás a OneDrive" funkciójának eltávolítása megtörtént. A OneDrive mentett "legutóbb megnyitott modellekkel" rendelkező felhasználók a következő hibaüzenetet kapják.

![A képernyőképek hibaüzenetet jelenítenek meg: a fájl objektum hivatkozásának megnyitása nem az objektum egy példányára van beállítva.](./media/threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Áthidaló megoldás

A OneDrive felhasználói a Microsoft [OneDrive for Windows](https://onedrive.live.com/about/en-us/download/) Client használatával férhetnek hozzá a OneDrive tárolt fájlokhoz a standard és a "modell megnyitása" párbeszédablakon keresztül.

![A képernyőkép a modell megnyitása párbeszédpanelen kiválasztott OneDrive jeleníti meg.](./media/threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>A szervezetem az eszköz 2016-es verzióját használja, használhatom az Azure stencil set szolgáltatást?

Igen, lehet! Az [Azure stencil-készlet a githubon érhető el](https://github.com/Microsoft/threat-modeling-templates/), és az eszköz 2016-es verziójában tölthető be. Ha egy új modellt szeretne létrehozni az Azure stencil készlettel, használja a főmenü képernyőjén a "sablon az új modellekhez" párbeszédpanelt. A TMT 2016 nem tudja megjeleníteni az Azure stencil "lehetséges enyhítések" mezőjében található hivatkozásokat, ezért HTML-címkékként megjelenített hivatkozásokat is láthat.

![Azure stencil-frissítések 2016-ügyfélen](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>System requirements (Rendszerkövetelmények)

- Támogatott operációs rendszerek
  - Microsoft Windows 10
- .NET-verzió szükséges
  - .NET 3.5.2
- További követelmények
  - Az eszköz és a sablonok frissítéseinek fogadásához internetkapcsolat szükséges.

## <a name="documentation-and-feedback"></a>Dokumentáció és visszajelzés

- A Threat Modeling Tool dokumentációja a [docs.microsoft.com](threat-modeling-tool.md)található, és [az eszköz használatával](threat-modeling-tool-getting-started.md)kapcsolatos információkat tartalmaz.

## <a name="next-steps"></a>Következő lépések

Töltse le a [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)legújabb verzióját.
