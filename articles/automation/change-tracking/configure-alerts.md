---
title: Riasztások létrehozása Azure Automation Change Tracking és leltárhoz
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja az Azure-riasztásokat, hogy tájékoztassanak a Change Tracking és a leltár által észlelt változások állapotáról.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 91be2f8641a061d009962cdcd03a8d56048594da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594502"
---
# <a name="how-to-create-alerts-for-change-tracking-and-inventory"></a>Riasztások létrehozása Change Tracking és leltárhoz

Az Azure-beli riasztások proaktív módon értesítik Önt a runbook-feladatok, a szolgáltatás állapotával kapcsolatos problémák vagy az Automation-fiókkal kapcsolatos egyéb forgatókönyvek eredményeiről. A Azure Automation nem tartalmaz előre konfigurált riasztási szabályokat, de saját maga is létrehozhatja a saját által létrehozott adatai alapján. Ez a cikk útmutatást nyújt a riasztási szabályok létrehozásához a Change Tracking és a leltár által azonosított változások alapján.

Ha nem ismeri a Azure Monitor riasztásokat, a Kezdés előtt tekintse meg [a Microsoft Azure riasztások áttekintése](../../azure-monitor/alerts/alerts-overview.md) című témakört. Ha többet szeretne megtudni a naplózási lekérdezéseket használó riasztásokról, tekintse meg a következő témakörben [szereplő riasztásokat: Azure monitor](../../azure-monitor/alerts/alerts-unified-log.md).

## <a name="create-alert"></a>Riasztás létrehozása

A következő példa azt mutatja, hogy a fájl **c:\Windows\System32\drivers\etc\hosts** módosult a gépen. Ez a fájl azért fontos, mert a Windows azt használja az IP-címekhez tartozó állomásnevek feloldására. Ez a művelet elsőbbséget élvez a DNS-sel szemben, és kapcsolódási problémákhoz vezethet. Emellett a rosszindulatú vagy más veszélyes webhelyekre irányuló forgalom átirányításához vezethet.

![A gazdagépek fájljának módosítását bemutató diagram](./media/configure-alerts/changes.png)

Ezt a példát követve megtudhatja, hogyan hozhat létre riasztásokat a változásokon.

1. Az Automation-fiók **change Tracking (változások nyomon követése** ) lapján válassza a **log Analytics** lehetőséget.

2. A naplók keresése területen keresse meg a tartalom módosításait a **hosts** fájlban a lekérdezéssel `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . Ez a lekérdezés a szót tartalmazó teljes elérési úttal rendelkező fájlok tartalmának módosítását keresi `hosts` . Egy adott fájlt is megadhat, ha az elérési út részét a teljes űrlapra módosítja, például a használatával `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Miután a lekérdezés visszaadja az eredményeit, válassza a naplóbeli keresés **új riasztási szabálya** elemet a **riasztás létrehozása** lap megnyitásához. Ezen a lapon a Azure Portal **Azure monitor** is megnyithatja.

4. Vizsgálja meg újra a lekérdezést, és módosítsa a riasztási logikát. Ebben az esetben azt szeretné, hogy a riasztás akkor aktiválódik, ha a környezetben lévő összes gépen még egy változás észlelhető.

    ![Váltás a Hosts fájl változásainak nyomon követése lekérdezésre](./media/configure-alerts/change-query.png)

5. Miután beállította a riasztási logikát, rendeljen műveleti csoportokat a riasztás aktiválására válaszként végrehajtandó műveletek végrehajtásához. Ebben az esetben az elküldött e-maileket és az IT Service Management-(ITSM-) jegyet kell létrehozni.

Kövesse az alábbi lépéseket a riasztások beállításához, hogy megismerje a frissítés központi telepítésének állapotát. Ha még nem ismeri az Azure-riasztásokat, tekintse meg az [Azure-riasztások áttekintése](../../azure-monitor/alerts/alerts-overview.md)című témakört.

## <a name="configure-action-groups-for-your-alerts"></a>Műveleti csoportok konfigurálása a riasztásokhoz

Miután beállította a riasztásokat, beállíthat egy műveleti csoportot, amely a több riasztáson keresztül használható műveletek csoportja. A műveletek tartalmazhatnak e-mailes értesítéseket, runbookok, webhookokat és még sok mást. A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../../azure-monitor/alerts/action-groups.md) szóló cikkben talál.

1. Válasszon ki egy riasztást, majd válassza az **új létrehozása** a **műveleti csoportok** alatt lehetőséget.

2. Adjon meg egy teljes nevet és egy rövid nevet a műveleti csoportnak. A Update Management a rövid nevet használja az értesítések megadott csoport használatával történő küldésekor.

3. A **műveletek** területen adjon meg egy nevet, amely megadja a műveletet, például az **e-mail értesítéseket**.

4. A **Művelettípus** mezőben válassza ki a megfelelő típust, például **e-mail/SMS-üzenet/leküldéses/hang**.

5. A művelet részleteinek szerkesztéséhez válassza a ceruza ikont.

6. Adja meg a művelet típusát a ablaktáblán. Ha például **e-mail/SMS-üzenet/leküldéses/hang** használatával küld e-mailt, írja be a művelet nevét, jelölje be az **e-mail** jelölőnégyzetet, adjon meg egy érvényes e-mail-címet, majd kattintson **az OK gombra**.

    ![E-mail műveletcsoport konfigurálása](./media/configure-alerts/configure-email-action-group.png)

7. A Műveletcsoport hozzáadása ablaktáblán kattintson az **OK** gombra.

8. Riasztási e-mail esetén testreszabhatja az e-mail tárgyát. Válassza a **műveletek testreszabása** a **szabály létrehozása** alatt lehetőséget, majd válassza az **e-mail tárgy** elemet.

9. Ha végzett, kattintson a **Riasztási szabály létrehozása** gombra.

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure monitor riasztásokról](../../azure-monitor/alerts/alerts-overview.md).

* Tudnivalók a Log Analytics-munkaterületről származó adatok lekérdezéséhez és elemzéséhez szükséges [naplókról](../../azure-monitor/logs/log-query-overview.md) .

* A [használat és a költségek kezelése Azure monitor naplókkal](../../azure-monitor/logs/manage-cost-storage.md) : az adatmegőrzési időszak módosításával, valamint az adatfelhasználás elemzésével és riasztásával kapcsolatos költségek szabályozása.