---
title: Egyéni riasztási szabályok az Azure Security Centerben | Microsoft Docs
description: Ebből a dokumentumból megismerheti az Azure Security Center egyéni riasztási szabályainak létrehozásához szükséges lépéseket.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: ab7165c3315e3a53f90900be8eaf1b9c614a2b07
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341129"
---
# <a name="custom-alert-rules-in-azure-security-center-preview"></a>Egyéni riasztási szabályok az Azure Security Centerben (előzetes verzió)
Ebből a dokumentumból megismerheti az Azure Security Center egyéni riasztási szabályainak létrehozásához szükséges lépéseket.

> [!NOTE]
> Egyéni riasztások jén kivonjuk a forgalomból 2019. június 30.

## <a name="retirement-of-custom-alert-rules-in-azure-security-center"></a>Használatból való kivonást egyaránt az egyéni riasztási szabályok az Azure Security Centerben

Egyéni riasztásainak kivonjuk a forgalomból 2019. június 30., használatból való kivonást egyaránt az alapul szolgáló infrastruktúra-alapú miatt. Amíg elavult időkeretét, a felhasználók tudni meglévő egyéni riasztási szabályok szerkesztése, de nem fogja tudni újakat vehet fel. Elavulással kapcsolatos bármely definiált egyéni riasztások nem lépnek érvénybe a biztonsági riasztások alapján egyéni értesítések ezeket a szabályokat nem jön létre.
Felhasználók vagy az alábbi intézkedéseket javasoljuk:
- Engedélyezése [Azure Sentinel-](https://azure.microsoft.com/services/azure-sentinel/) és a beépített [analytics](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) funkció újból létrehozni a riasztási szabályok
- Hozza létre újból a riasztásokat az Azure Monitor riasztások
                                     
Tartani a meglévő riasztásokat, és Azure Sentinel-telepítheti át őket, kérjük, [indítsa el az Azure-Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview). Első lépésként válassza ki a munkaterületet, az egyéni értesítések tárolására, és válassza a "Analytics" menüpont a riasztások egyéni szabályok beállítása. Látogasson el a [dokumentáció](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) további információt.

> [!NOTE]
> Egyéni riasztások használatával [keresési](https://docs.microsoft.com/azure/azure-monitor/log-query/search-queries) vagy [Union](https://docs-analytics-eus.azurewebsites.net/queryLanguage/query_language_unionoperator.html) utasítások lekérdezések nem támogatottak az Azure-Sentinel. Ezek a riasztások kérjük, módosítsa az áttelepítés előtt.

Újra létre az Azure Monitor riasztások riasztásokról, tekintse meg: [Létrehozása, megtekintése és kezelése az Azure Monitor használatával riasztások](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) riasztások létrehozásával kapcsolatos útmutatást. Kattintson az Azure Monitor riasztások általános áttekintéséért [Itt](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

## <a name="what-are-custom-alert-rules-in-security-center"></a>Mire szolgálnak a Security Center egyéni riasztási szabályai?

A Security Center előre definiált [biztonsági riasztások](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) készletével rendelkezik, amelyeket a rendszer a fenyegetések vagy gyanús tevékenységek bekövetkeztekor aktivál. Egyes forgatókönyvekben előfordulhat, hogy szeretne egyéni, a környezet speciális igényeinek megfelelő riasztásokat létrehozni.

A Security Center egyéni riasztási szabályaival új biztonsági riasztásokat definiálhat a környezetből már begyűjtött adatok alapján. Létrehozhat lekérdezéseket, amelyek eredményeit használhatja az egyéni szabály kritériumaiként, és amint ezek teljesülnek, a rendszer végrehajtja a szabályt. Az egyéni lekérdezések létrehozásához használhatja a számítógépek biztonsági eseményeit, a partnerek biztonsági megoldásainak naplóit vagy az API-kon keresztül feldolgozott adatokat.

> [!NOTE]
> Egyéni riasztások nem támogatottak a Security Center [vizsgálati funkciójáról](security-center-investigation.md).
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Egyéni riasztási szabály létrehozása a Security Centerben

Egyéni riasztási szabály létrehozásához nyissa meg a **Security Center** irányítópultját, és kövesse az alábbi lépéseket:

1.  A bal oldali panelen, a **Felderítés** területen kattintson az **Egyéni riasztási szabályok (előzetes verzió)** lehetőségre.
2.  A **Security Center – Egyéni riasztási szabályok (előzetes verzió)** oldalon kattintson az **Új egyéni riasztási szabály** gombra.

    ![Egyéni riasztás](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)

3.  Megjelenik az Egyéni riasztási szabály létrehozása lap a következő beállításokkal:

    ![Hozzon létre](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  A **Név** mezőben adja meg az egyéni szabály nevét.
5.  A **Leírás** mezőben adjon meg egy rövid leírást, amely ismerteti a szabály célját.
6.  A **Súlyosság** mezőben igény szerint válassza ki a súlyossági szintet (Magas, Közepes, Alacsony).
7.  Az **Előfizetés** mezőben válassza ki az előfizetést, amelyre a szabály vonatkozik.
8.  Válassza ki a munkaterületet, amely a szabállyal figyelni szeretné a **munkaterület** mező, majd a a **keresési lekérdezés** mező, a lekérdezést, amely az eredmények lekéréséhez használni kívánt.

    > [!NOTE]
    > A munkaterületen, az egyéni riasztási tárolni kívánt írási engedély szükséges.
    >
    >

    A lekérdezés eredménye aktiválja a riasztást. Figyelje meg, hogy ha egy érvényes lekérdezést ír be, megjelenik a zöld pipa a mező jobb sarkában:

    ![Lekérdezés](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. Válassza ki az **Időszak** mezőben a fenti lekérdezés végrehajtásának időtartományát. Figyelje meg, hogy a mező alján a keresési eredmények a kiválasztott időtartomány függvényében változnak.

    ![Időszak](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. Az **Értékelés** mezőben válassza ki, hogy a szabály milyen gyakran legyen kiértékelve és végrehajtva.
12. Az **Eredmények száma** mezőben válassza ki az operátort (nagyobb vagy kisebb, mint).
13. A **Küszöbérték** mezőbe írja be azt a számot, amelyet az előbb kiválasztott operátor referenciaként használ.
14. Engedélyezze a **Riasztások mellőzése** beállítást, ha szeretné, hogy a Security Center várjon egy bizonyos ideig, mielőtt új riasztást küldene ezzel a szabállyal kapcsolatban.
15. A befejezéshez kattintson az **OK** gombra.

Az új riasztási szabály a létrehozást követően megjelenik az egyéni riasztási szabályok listájában. Amint a szabályban megadott feltételek teljesülnek, aktiválódik egy új riasztás, amely a **Biztonsági riasztások** irányítópulton jelenik meg.

![Riasztás](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

Figyelje meg, hogy az egyéni szabály létrehozásakor meghatározott paraméterek (keresési lekérdezés, küszöbérték stb.) elérhetők a riasztásban.

## <a name="see-also"></a>Lásd még
Ebben a dokumentumban megismerkedhetett az Azure Security Center egyéni riasztási szabályainak létrehozásával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Azure Security Center – Hibaelhárítási útmutató](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
