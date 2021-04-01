---
title: Python 2 csomagok kezelése Azure Automation
description: Ebből a cikkből megtudhatja, hogyan kezelheti a Python 2-csomagokat Azure Automationban.
services: automation
ms.subservice: process-automation
ms.date: 12/17/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: fd830afd5628591019902ca583f9cbc8e2a7ecad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97683398"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Python 2 csomagok kezelése Azure Automation

Azure Automation lehetővé teszi a Python 2 runbookok futtatását az Azure-ban és a Linux hibrid Runbook-feldolgozókon. A runbookok egyszerűsítése érdekében a Python-csomagokat használhatja a szükséges modulok importálásához. Ez a cikk bemutatja, hogyan kezelhetők és használhatók a Python-csomagok a Azure Automationban.

## <a name="import-packages"></a>Csomagok importálása

Az Automation-fiókban válassza a **Python 2 csomag** lehetőséget a **megosztott erőforrások** területen. Kattintson **a + Python 2 csomag hozzáadása** lehetőségre.

:::image type="content" source="media/python-packages/add-python-package.png" alt-text="A Python 2 csomagok oldalának képernyőképe a bal oldali menüben a Python 2 csomagokat jeleníti meg, és egy kiemelt Python 2 csomagot ad hozzá.":::

A Python 2 csomag hozzáadása lapon válasszon ki egy helyi csomagot a feltöltéshez. A csomag lehet egy **. WHL** vagy **. tar. gz** fájl. Ha a csomag ki van választva, kattintson **az OK** gombra a feltöltéshez.

:::image type="content" source="media/python-packages/upload-package.png" alt-text="A képernyőképen a Python 2 csomag hozzáadása oldal jelenik meg, amelyhez feltöltött tar. gz fájl van kijelölve.":::

A csomag importálása után a rendszer az Automation-fiók Python 2 csomagok lapján jelenik meg. Ha el kell távolítania egy csomagot, válassza ki a csomagot, és kattintson a **Törlés** gombra.

:::image type="content" source="media/python-packages/package-list.png" alt-text="A csomag importálása után a képernyőkép a Python 2 csomagok oldalát jeleníti meg.":::

## <a name="import-packages-with-dependencies"></a>Csomagok importálása függőségekkel

Az Azure Automation nem oldja meg a Python-csomagok függőségeit az importálási folyamat során. A csomagok két módon importálhatók az összes függőségével. A csomagok az Automation-fiókba történő importálásához csak az alábbi lépések egyikét kell használni.

### <a name="manually-download"></a>Manuális Letöltés

Egy olyan Windows 64 bites gépen, amelyen telepítve van a [Python 2.7](https://www.python.org/downloads/release/latest/python2) és a [pip](https://pip.pypa.io/en/stable/) , futtassa a következő parancsot egy csomag és annak összes függőségének letöltéséhez:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

A csomagok letöltése után importálhatja őket az Automation-fiókjába.

### <a name="runbook"></a>Forgatókönyv

 Egy runbook beszerzéséhez [importálja a Python 2 csomagokat a Azure Automation PyPI-ből](https://github.com/azureautomation/import-python-2-packages-from-pypi-into-azure-automation-account) a Azure Automation GitHub-szervezetből az Automation-fiókjába. Győződjön meg arról, hogy a futtatási beállítások az **Azure** -ra vannak beállítva, és a paraméterekkel indítják el a runbook. A runbook használatához az Automation-fiók működéséhez futtató fiókra van szükség. Minden paraméternél győződjön meg arról, hogy az alábbi listában és képen látható kapcsolóval indítja el:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* – a \<automationAccount\>
* – m \<modulePackage\>

:::image type="content" source="media/python-packages/import-python-runbook.png" alt-text="A képernyőképen a jobb oldalon található Start Runbook (első lépések) ablaktáblán látható a import_py2package_from_pypi áttekintése oldal.":::

A runbook lehetővé teszi a letölteni kívánt csomag megadását. A paraméter használata például `Azure` letölti az összes Azure-modult és az összes függőséget (körülbelül 105).

A runbook befejezése után az Automation-fiókban található **megosztott erőforrások** alatt ellenőrizheti a **Python 2 csomagokat** , így ellenőrizheti, hogy a csomag importálása megfelelő volt-e.

## <a name="use-a-package-in-a-runbook"></a>Csomag használata runbook

Az importált csomaggal a runbook használható. A következő példa a [Azure Automation segédprogram-csomagot](https://github.com/azureautomation/azure_automation_utility)használja. Ez a csomag megkönnyíti a Python és a Azure Automation használatát. A csomag használatához kövesse a GitHub-tárház utasításait, és adja hozzá a runbook. A használatával például `from azure_automation_utility import get_automation_runas_credential` importálhatja a függvényt a futtató fiók lekéréséhez.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Runbookok offline fejlesztése és tesztelése

A Python 2 runbookok offline fejlesztéséhez és teszteléséhez használja a [Azure Automation Python által emulált eszközök](https://github.com/azureautomation/python_emulated_assets) modult a githubon. Ez a modul lehetővé teszi a megosztott erőforrások, például a hitelesítő adatok, a változók, a kapcsolatok és a tanúsítványok hivatkozását.

## <a name="next-steps"></a>Következő lépések

Python-runbook előkészítéséhez tekintse meg [a Python-Runbook létrehozása](learn/automation-tutorial-runbook-textual-python2.md)című témakört.
