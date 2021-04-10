---
title: Alkalmazás-változási elemzés – Azure Monitor
description: Ismerje meg, hogy miként lehet elhárítani a problémákat az Application Change Analysis szolgáltatásban.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 7200978ce31a47f7bd0d023cecf359b10a1c96de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100521087"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>Alkalmazás-változási elemzés (előzetes verzió) – problémamegoldás

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>Hiba történt a Microsoft regisztrálásakor. Elemzési erőforrás-szolgáltató módosítása a korábbi módosítások lapról

Ha első alkalommal tekinti meg a változási előzményeket az alkalmazás-módosítási elemzéssel való integráció után, akkor azt fogja látni, hogy automatikusan regisztrálja a **Microsoft. ChangeAnalysis** erőforrás-szolgáltatót. Ritka esetekben előfordulhat, hogy a következő okok miatt sikertelenek lehetnek:

- **Nem rendelkezik elegendő engedélyekkel a Microsoft. ChangeAnalysis erőforrás-szolgáltató regisztrálásához**. Ez a hibaüzenet azt jelenti, hogy a jelenlegi előfizetésben szereplő szerepkörhöz nincs társítva a **Microsoft. support/register/Action** hatókör. Ez akkor fordulhat elő, ha nem Ön az előfizetés tulajdonosa, és megosztott hozzáférési engedélyekkel rendelkezik egy munkatárson keresztül (azaz egy erőforráscsoport hozzáférésének megtekintése). A probléma megoldásához vegye fel a kapcsolatot az előfizetés tulajdonosával a **Microsoft. ChangeAnalysis** erőforrás-szolgáltató regisztrálásához. Ez a Azure Portal előfizetéseken keresztül is elvégezhető **| Erőforrás-szolgáltatók** és keresés ```Microsoft.ChangeAnalysis``` és regisztrálás a felhasználói felületen, vagy Azure PowerShell vagy az Azure CLI-n keresztül.

    Erőforrás-szolgáltató regisztrálása a PowerShell-lel:
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Nem sikerült regisztrálni a Microsoft. ChangeAnalysis erőforrás-szolgáltatót**. Ez az üzenet azt jelenti, hogy a felhasználói felület azonnal leállt, mert az erőforrás-szolgáltató regisztrálására irányuló kérést küldött, és nem kapcsolódik a jogosultsági problémákhoz. Valószínűleg ideiglenes internetkapcsolati probléma lehet. Próbálja meg frissíteni a lapot, és ellenőrizze az internetkapcsolatot. Ha a hiba továbbra is fennáll, forduljon a következőhöz: changeanalysishelp@microsoft.com

- **Ez a vártnál több időt vesz** igénybe. Ez az üzenet azt jelenti, hogy a regisztráció 2 percnél hosszabb időt vesz igénybe. Ez szokatlan, de nem feltétlenül jelenti azt, hogy valamilyen hiba történt. Megtekintheti az **előfizetéseket | Erőforrás-szolgáltató** a **Microsoft. ChangeAnalysis** erőforrás-szolgáltató regisztrációs állapotának kereséséhez. A felhasználói felület használatával törölheti, újra regisztrálhatja vagy frissítheti, és megtekintheti, hogy ez segít-e. Ha a probléma továbbra is fennáll, forduljon changeanalysishelp@microsoft.com a támogatási szolgálathoz.
    ![Az RP-regisztráció túl hosszú ideig történő hibaelhárítása](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![Képernyőfelvétel a problémák diagnosztizálása és megoldása eszközről egy olyan virtuális géphez, amelyen a hibaelhárítási eszközök vannak kiválasztva.](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Képernyőkép a virtuális gép legutóbbi változásainak hibaelhárítási eszközének csempéről.](./media/change-analysis/analyze-recent-changes.png)

## <a name="azure-lighthouse-subscription-is-not-supported"></a>Az Azure Lighthouse-előfizetés nem támogatott

- Nem **sikerült lekérdezni a Microsoft. ChangeAnalysis erőforrás-szolgáltatót** az *Azure Lighthouse-előfizetéssel nem támogatott, a módosítások csak az előfizetés otthoni bérlője esetében érhetők el*. A Change Analysis erőforrás-szolgáltatóra vonatkozó korlátozás most már regisztrálva van az Azure Lighthouse-előfizetésen keresztül a hazai bérlőn kívüli felhasználók számára. Dolgozunk ennek a korlátozásnak a kezelésén. Ha ez egy blokkolási probléma az Ön számára, van egy áthidaló megoldás, amely magában foglalja egy egyszerű szolgáltatásnév létrehozását és a szerepkör explicit módon történő hozzárendelését a hozzáférés engedélyezéséhez.  További információért forduljon a szolgáltatáshoz changeanalysishelp@microsoft.com .

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>Hiba történt a módosítások beolvasása közben. Frissítse a lapot, vagy térjen vissza később a módosítások megtekintéséhez

Ez az Application Change Analysis Service által bemutatott általános hibaüzenet, ha a módosítások nem tölthetők be. Néhány ismert ok:

- Internetkapcsolattal kapcsolatos hiba az ügyfél-eszközön
- Az Analysis Service átmenetileg nem érhető el, néhány perc elteltével a rendszer általában kijavítja ezt a problémát. Ha a hiba továbbra is fennáll, forduljon a következőhöz: changeanalysishelp@micorosoft.com

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>Nem rendelkezik elegendő engedélyekkel a módosítások megtekintéséhez. Forduljon az Azure-előfizetés rendszergazdájához

Ez az általános jogosulatlan hibaüzenet, amely azt ismerteti, hogy az aktuális felhasználó nem rendelkezik megfelelő engedélyekkel a módosítás megtekintéséhez. Az erőforráson legalább olvasási hozzáféréssel kell rendelkeznie az Azure Resource Graph és a Azure Resource Manager által visszaadott infrastruktúra-módosítások megtekintéséhez. A Web App in-Guest file Changes és a Configuration Changes esetében legalább közreműködői szerepkörre van szükség.

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Nem sikerült regisztrálni a Microsoft. ChangeAnalysis erőforrás-szolgáltatót

Ez az üzenet azt jelenti, hogy a felhasználói felület azonnal leállt, mert az erőforrás-szolgáltató regisztrálására irányuló kérést küldött, és nem kapcsolódik a jogosultsági problémákhoz. Valószínűleg ideiglenes internetkapcsolati probléma lehet. Próbálja meg frissíteni a lapot, és ellenőrizze az internetkapcsolatot. Ha a hiba továbbra is fennáll, forduljon a következőhöz: changeanalysishelp@microsoft.com

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Nem rendelkezik elegendő engedélyekkel a Microsoft. ChangeAnalysis erőforrás-szolgáltató regisztrálásához. Forduljon az Azure-előfizetés rendszergazdájához

Ez a hibaüzenet azt jelenti, hogy a jelenlegi előfizetésben szereplő szerepkörhöz nincs társítva a **Microsoft. support/register/Action** hatókör. Ez akkor fordulhat elő, ha nem Ön az előfizetés tulajdonosa, és megosztott hozzáférési engedélyekkel rendelkezik egy munkatárson keresztül (azaz egy erőforráscsoport hozzáférésének megtekintése). A probléma megoldásához vegye fel a kapcsolatot az előfizetés tulajdonosával a **Microsoft. ChangeAnalysis** erőforrás-szolgáltató regisztrálásához. Ez a Azure Portal előfizetéseken keresztül is elvégezhető **| Erőforrás-szolgáltatók** és keresés ```Microsoft.ChangeAnalysis``` és regisztrálás a felhasználói felületen, vagy Azure PowerShell vagy az Azure CLI-n keresztül.

Erőforrás-szolgáltató regisztrálása a PowerShell-lel:

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Resource Graph](../../governance/resource-graph/overview.md)-ról, amely segít a energiagazdálkodási változások elemzésében.