---
title: Azure-erőforrások áthelyezése erőforráscsoportok, előfizetések vagy régiók között.
description: Az erőforráscsoportok, előfizetések vagy régiók között áthelyezható Azure-erőforrástípusok áttekintése.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 646c0892ac3609fc531e91f4bfb9c1de6a61bc38
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730458"
---
# <a name="move-azure-resources-across-resource-groups-subscriptions-or-regions"></a>Azure-erőforrások áthelyezése erőforráscsoportok, előfizetések vagy régiók között

Az Azure-erőforrások áthelyezheti egy új erőforráscsoportba vagy előfizetésbe, vagy régiók között.

## <a name="move-resources-across-resource-groups-or-subscriptions"></a>Erőforrások áthelyezése erőforráscsoportok vagy előfizetések között

Az Azure-erőforrásokat áthelyezheti egy másik Azure-előfizetésbe vagy egy másik erőforráscsoportba ugyanabban az előfizetésben. Az erőforrások áthelyezéséhez az Azure Portal, az Azure PowerShell, az Azure CLI vagy a REST API használható. További információ: Erőforrások áthelyezése új [erőforráscsoportba vagy előfizetésbe.](move-resource-group-and-subscription.md)

### <a name="upgrade-a-subscription"></a>Előfizetés frissítése

Ha valóban frissíteni szeretné Azure-előfizetését (például váltani szeretne ingyenesről használat alapú fizetésre), át kell alakítania az előfizetést.

- Az ingyenes próbaverzió frissítéséhez tekintse meg az ingyenes próbaverzió frissítését vagy [Microsoft Imagine Azure-előfizetés](../../cost-management-billing/manage/upgrade-azure-subscription.md)használat alapú fizetésre való frissítését.
- A használat alapú fizetéses fiók módosítása: Használat alapú [fizetéses Azure-előfizetés módosítása egy másik ajánlatra.](../../cost-management-billing/manage/switch-azure-offer.md)

Ha nem tudja átalakítani az előfizetést, hozzon létre egy Azure-támogatás [kérést.](../../azure-portal/supportability/how-to-create-azure-support-request.md) Válassza **az Előfizetés-kezelés** lehetőséget a probléma típusaként.

## <a name="move-resources-across-regions"></a>Erőforrások áthelyezése régiók között

Az Azure globális infrastruktúrájának alapját az Azure földrajzi régiói, régiói és rendelkezésre állási zónái alkotják. Az Azure [földrajzi régiói](https://azure.microsoft.com/global-infrastructure/geographies/) általában kettő vagy több [Azure-régiót tartalmaznak.](https://azure.microsoft.com/global-infrastructure/regions/) A régió egy földrajzi területen belüli terület, amely Availability Zones és több adatközpontot tartalmaz.

Az erőforrások adott Azure-régióban való üzembe helyezése után számos oka lehet annak, hogy az erőforrásokat egy másik régióba szeretné áthelyezni.

- **Igazodjon egy régió indításához:** Helyezze át az erőforrásokat egy újonnan bevezetett Azure-régióba, amely korábban nem volt elérhető.
- **Szolgáltatások/szolgáltatások igazítása:** Áthelyezi az erőforrásokat az adott régióban elérhető szolgáltatások és szolgáltatások előnyeinek kihasználása érdekében.
- **Reagálás az üzleti fejlesztésekre:** Erőforrások áthelyezése egy régióba az üzleti változások, például egyesítések vagy felvásárlások esetén.
- **Igazítás a közelséghez:** Áthelyezi az erőforrásokat a vállalata helyi régiójába.
- **Adatkövetelmények kielégítése:** Áthelyezhet erőforrásokat, hogy igazodjon az adatelhellyel kapcsolatos követelményeknek vagy az adatbesorolási igényeknek. [További információ](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Válasz az üzembe helyezési követelményekre:** Áthelyezheti a hibásan üzembe helyezett erőforrásokat, vagy áthelyezheti őket a kapacitásigénynek megfelelően.
- **Válasz a leszerelésre:** Erőforrások áthelyezése leszerelt régiók miatt.

### <a name="move-resources-with-resource-mover"></a>Erőforrások áthelyezése a Resource Mover-sel

A következővel áthelyezheti az erőforrásokat egy [Azure Resource Mover.](../../resource-mover/overview.md) A Resource Mover a következő eket biztosítja:

- Egyetlen központ az erőforrások régiók közötti áthelyezéséhez.
- Csökkentett áthelyezési idő és összetettség. Minden, amire szüksége van, egyetlen helyen található.
- Egyszerű és konzisztens megoldás a különböző típusú Azure-erőforrások áthelyezéshez.
- Egyszerű módszer az áthelyezni kívánt erőforrások függőségeinek azonosítására. Ez az azonosítás segít a kapcsolódó erőforrások együttes áthelyezésében, hogy az áthelyezés után minden a várt módon működik a célterületen.
- A forrás régióban található erőforrások automatikus törlése, ha az áthelyezés után törölni szeretné azokat.
- Tesztelés: Kipróbálhatja az áthelyezést, majd elvetheti, ha nem szeretne teljes áthelyezést.

Az erőforrásokat több különböző módszerrel is áthelyezheti egy másik régióba:

- **Kezdje meg az erőforrások áthelyezését egy erőforráscsoportból:** Ezzel a módszerrel elindítja a régió áthelyezését egy erőforráscsoportból. Miután kiválasztotta az áthelyezni kívánt erőforrásokat, a folyamat az Erőforrás-áthelyezési központban folytatódik, és ellenőrzi az erőforrás-függőségeket, és összehangolja az áthelyezési folyamatot. [További információ](../../resource-mover/move-region-within-resource-group.md).
- **Kezdje meg az erőforrások áthelyezését közvetlenül a Resource Mover hubról:** Ezzel a módszerrel közvetlenül a központban indítja el a régióátvételi folyamatot. [További információ](../../resource-mover/tutorial-move-region-virtual-machines.md).

## <a name="next-steps"></a>Következő lépések

- Ha ellenőriznie kell, hogy egy erőforrástípus támogatja-e az áthelyezést, tekintse meg az erőforrások [áthelyezési műveletének támogatását.](move-support-resources.md)
- A régióát áthelyezési folyamattal kapcsolatos további információkért lásd: [Tudnivalók az áthelyezési folyamatról.](../../resource-mover/about-move-process.md)
