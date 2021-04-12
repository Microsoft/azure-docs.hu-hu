---
title: A Microsoft Azure Automation esetében az eset megnyitásakor begyűjtött adatok | Microsoft Docs
description: Ez a cikk ismerteti azokat az információkat, amelyeket össze kell gyűjteni, mielőtt megnyitná a Azure Automation Microsoft Azure támogatással rendelkező esetet.
services: automation
ms.subservice: ''
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.openlocfilehash: 3d2c0cf780b992d7bb464969c50d60ffa5093e8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896562"
---
# <a name="data-to-collect-when-opening-a-case-for-microsoft-azure-automation"></a>Összegyűjtendő adatok egy Microsoft Azure Automation-eset megnyitásakor

Ez a cikk néhány olyan információt ismertet, amelyet össze kell gyűjtenie, mielőtt megnyit egy esetet a Azure Automation Microsoft Azure-támogatással. Ez az információ nem szükséges az eset megnyitásához. Ugyanakkor a Microsoft segítséget nyújthat a probléma gyorsabb megoldásában. Emellett előfordulhat, hogy a támogatási mérnök ezt az adatkérést az eset megnyitása után is megkéri.

## <a name="basic-data"></a>Alapszintű adatok

Gyűjtse össze a Tudásbázis 4034605. cikkében ismertetett alapszintű adatokat [– Hogyan rögzíthet Azure Automation-parancsfájlokat](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>A Linuxon Update Management problémák

1. A KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)-ban felsorolt elemek mellett futtassa a következő log Collection eszközt:

   [OMS Linux-ügynök naplójának gyűjtője](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Tömörítse a **/var/opt/Microsoft/omsagent/Run/automationworker/** mappa tartalmát, majd küldje el a tömörített fájlt az Azure támogatási szolgálatának.
 
3. Győződjön meg arról, hogy a Log Analytics-ügynök által a Linux-jelentésekhez használt munkaterülethez tartozó azonosító megegyezik a frissítések figyeléséhez használt munkaterület azonosítójával.

## <a name="data-for-update-management-issues-on-windows"></a>A Windows Update Management problémáinak adatvédelme

1. Adatgyűjtés az [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)-ben felsorolt elemekhez.

2. Exportálja a következő eseménynaplókat a EVTX formátumba:

   * Rendszer
   * Alkalmazás
   * Biztonság
   * Operations Manager
   * Microsoft-SMA/működési

3. Ellenőrizze, hogy az ügynök által jelentett munkaterület azonosítója megegyezik-e a Windows-frissítések által figyelt munkaterület azonosítójával.

## <a name="data-for-job-issues"></a>A feladatok problémáinak adatai

1. Adatgyűjtés az [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)-ben felsorolt elemekhez.

2. Gyűjtse össze a feladatokhoz tartozó AZONOSÍTÓJÚ feladatot, amely a következő hibával rendelkezik:

   1. A Azure Portal lépjen az Automation- **fiókok** elemre.
   2. Válassza ki a hibaelhárításhoz használt Automation-fiókot, és jegyezze fel a nevet.
   3. Válassza a **feladatok** lehetőséget.
   4. Válassza ki a hibaelhárításhoz szükséges feladatot.
   5. A feladatok összegzése ablaktáblán keresse meg a GUID értéket a **Job ID**-ben.

   ![Feladatok azonosítója a feladatok összegzése ablaktáblán belül](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Gyűjtsön egy mintát a futtatott parancsfájlból.

4. A naplófájlok összegyűjtése:

   1. A Azure Portal lépjen az Automation- **fiókok** elemre.
   2. Válassza ki a hibaelhárításhoz használt Automation-fiókot.
   3. Válassza a **feladatok** lehetőséget.
   4. Válassza ki a hibaelhárításhoz szükséges feladatot.
   5. Válassza ki **az összes naplót**.
   6. Az eredményül kapott ablaktáblában Gyűjtse össze az adatokat.

   ![Az összes naplóban felsorolt adat](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>A modulokkal kapcsolatos problémák

Az [alapszintű adatelemeken](#basic-data)kívül az alábbi adatokat is össze kell gyűjteni:

* A követett lépések, hogy a probléma reprodukálható legyen.
* Képernyőképek az összes hibaüzenetről.
* Képernyőképek az aktuális modulokról és azok verziószámáról.

## <a name="next-steps"></a>Következő lépések

Ha további segítségre van szüksége:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* Az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával, a hivatalos Microsoft Azure fiókkal való csatlakozással javíthatja az ügyfelek élményét: válaszokat, támogatást és szakértőket.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése** lehetőséget.
