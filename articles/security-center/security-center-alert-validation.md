---
title: Riasztások érvényesítése a Azure Security Centerban | Microsoft Docs
description: Megtudhatja, hogyan ellenőrizheti, hogy a biztonsági riasztások megfelelően vannak-e konfigurálva Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 3bdc0af2ea581954cb5edd2c7d00d286f814506c
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633597"
---
# <a name="alert-validation-in-azure-security-center"></a>Riasztások érvényesítése Azure Security Center
A dokumentum ismerteti, hogyan ellenőrizheti, hogy a rendszere megfelelően konfigurálva van-e az Azure Security Center riasztásaihoz.

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?
A riasztások olyan értesítések, amelyeket a Security Center hoz létre, ha fenyegetést észlel egy erőforrásban. Rangsorolja és felsorolja a riasztásokat, valamint azokat az információkat, amelyek a probléma gyors kivizsgálásához szükségesek. A Security Center javaslatokat is felkínál a támadás elhárítására.
További információ: [biztonsági riasztások a Security Centerban](security-center-alerts-overview.md) , valamint a [biztonsági riasztások kezelése és válaszadás](security-center-managing-and-responding-alerts.md)


## <a name="generate-sample-azure-defender-alerts"></a>Azure Defender-mintariasztások létrehozása

Ha az új, előzetes verziójú riasztásokat használja a [Azure Security Center biztonsági riasztások kezelése és](security-center-managing-and-responding-alerts.md)az azokra való reagálás című témakörben leírtak szerint, néhány kattintással létrehozhat minta riasztásokat a Azure Portal biztonsági riasztások oldaláról.

Használjon példákat a következőhöz:

- Az Azure Defender értékének és funkcióinak kiértékelése
- érvényesítse a biztonsági riasztásokhoz (például SIEM-integrációhoz, a munkafolyamat-automatizáláshoz és az e-mail-értesítésekhez) létrehozott konfigurációkat.

Példa riasztások létrehozásához:

1. A riasztások lap eszköztárán válassza a **minta riasztások létrehozása** lehetőséget. 
1. Válassza ki az előfizetést.
1. Válassza ki a megfelelő Azure Defender-csomagot/-t, amelynek riasztásait meg szeretné jeleníteni. 
1. Válassza a **minta riasztások létrehozása** lehetőséget.

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="A minta riasztások létrehozásának lépései Azure Security Center":::
    
    Megjelenik egy értesítés, amely tájékoztatja, hogy a rendszer létrehozza a minta riasztásokat:

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="Értesítés arról, hogy a rendszer létrehozza a minta riasztásait.":::

    Néhány perc elteltével a riasztások a biztonsági riasztások lapon jelennek meg. Ezen kívül bárhol máshol is megjelennek a Azure Security Center biztonsági riasztások (csatlakoztatott SIEM-EK, e-mail-értesítések stb.) fogadására konfiguráltak.

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="Riasztások a biztonsági riasztások listájában":::

    > [!TIP]
    > A riasztások szimulált erőforrásokra vonatkoznak.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Riasztások szimulálása Azure-beli virtuális gépeken (Windows) <a name="validate-windows"></a>

Miután telepítette Security Center ügynököt a számítógépre, hajtsa végre az alábbi lépéseket azon a számítógépen, amelyen a riasztás megtámadott erőforrását szeretné használni:

1. Másoljon egy végrehajtható fájlt (például **calc.exe**) a számítógép asztalára vagy más kényelmi címtárba, és nevezze át **ASC_AlertTest_662jfi039N.exeként**.
1. Nyissa meg a parancssort, és hajtsa végre a fájlt argumentummal (csak egy hamis argumentum neve), például: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Várjon 5-10 percet, és nyissa meg a Security Center riasztásait. Riasztásnak kell megjelennie.

> [!NOTE]
> A Windows rendszerhez készült teszt-riasztások megtekintésekor ellenőrizze, hogy az **argumentumok naplózása engedélyezve** van-e **igaz**. Ha **hamis**, akkor engedélyeznie kell a parancssori argumentumok naplózását. Az engedélyezéshez használja a következő parancsot: 
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Riasztások szimulálása Azure-beli virtuális gépeken (Linux) <a name="validate-linux"></a>

Miután telepítette Security Center ügynököt a számítógépre, hajtsa végre az alábbi lépéseket azon a számítógépen, amelyen a riasztás megtámadott erőforrását szeretné használni:
1. Másolja a végrehajtható fájlt egy kényelmes helyre, és nevezze át a (z **)./asc_alerttest_662jfi039nre**, például:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Nyissa meg a parancssort, és hajtsa végre a következő fájlt:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Várjon 5-10 percet, és nyissa meg a Security Center riasztásait. Riasztásnak kell megjelennie.


## <a name="simulate-alerts-on-kubernetes"></a>Riasztások szimulálása a Kubernetes <a name="validate-kubernetes"></a>

Ha az Azure Kubernetes szolgáltatást a Security Centerrel integrálta, tesztelheti, hogy a riasztások a következő kubectl-paranccsal működnek-e:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

A Kubernetes-csomópontok és-fürtök védelmével kapcsolatos további információkért lásd: [Bevezetés az Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md) használatába

## <a name="next-steps"></a>Következő lépések
Ez a cikk a riasztások érvényesítési folyamatát mutatta be. Most, hogy már ismeri az érvényesítést, tekintse meg a következő cikkeket:

* [Azure Key Vault-fenyegetésészlelés érvényesítése az Azure Security Centerben](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Biztonsági riasztások kezelése és válaszadás a Azure Security Centerban](security-center-managing-and-responding-alerts.md) – Ismerje meg, hogyan kezelheti a riasztásokat, és hogyan reagálhat a Security Center biztonsági incidensekre.
* A [Azure Security Center biztonsági állapotának monitorozása](security-center-monitoring.md) – megismerheti az Azure-erőforrások állapotának figyelését.
* A [biztonsági riasztások megismerése Azure Security Centerban](./security-center-alerts-overview.md) – a különböző típusú biztonsági riasztások ismertetése.