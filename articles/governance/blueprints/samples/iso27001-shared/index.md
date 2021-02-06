---
title: ISO 27001 Megosztott szolgáltatások tervminta – Áttekintés
description: Az ISO 27001 Megosztott szolgáltatások tervmintájának áttekintése és architektúrája. Ennek a tervmintának a segítségével az ügyfelek felmérhetik az ISO 27001 adott vezérlőit.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: d5ac88fc7a4fe21cef74ee23af2336a5a376471a
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627650"
---
# <a name="overview-of-the-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001 Azure Blueprints Megosztott szolgáltatások tervmintájának áttekintése

Az ISO 27001 Megosztott szolgáltatások tervmintája a szabványoknak megfelelő infrastruktúra-minták és szabályzati védőkorlátok együttesét biztosítja, amelyek segítséget nyújtanak az ISO 27001-tanúsítvány megszerzéséhez. E terv segítségével az ügyfelek olyan felhőalapú architektúrákat helyezhetnek üzembe, amelyek akkreditációs vagy megfelelőségi követelményekkel rendelkező alkalmazási helyzetekre kínálnak megoldást.

Az [ISO 27001 App Service Environment/SQL Database-számításifeladat](../iso27001-ase-sql-workload/index.md) tervminta e minta kiegészítéseként szolgál.

## <a name="architecture"></a>Architektúra

Az ISO 27001 Megosztott szolgáltatások tervminta alapszintű infrastruktúrát helyez üzembe az Azure-ban, amelynek segítségével a szervezetek több, virtuális adatközponti (VDC) megközelítésen alapuló számítási feladatot üzemeltethetnek.
A VDC referenciaarchitektúrák, automatizáló eszközök és bevonási modellek bevált együttese, amelyet a Microsoft legnagyobb vállalati ügyfelei használnak. A Megosztott szolgáltatások tervminta az alább látható, teljesen natív Azure VDC-környezeten alapul.

:::image type="content" source="../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png" alt-text="Az ISO 27001 Megosztott szolgáltatások tervminta kialakítása" border="false":::

Ez a környezet több Azure-szolgáltatásból épül fel, és ISO 27001 szabványokon alapuló, biztonságos, teljes körűen monitorozott, vállalati használatra kész megosztottszolgáltatás-infrastruktúrát biztosít. A környezet összetevői:

- A feladatkörök vezérlősík szempontjából való elkülönítésére használható [Azure-szerepkörök](../../../../role-based-access-control/overview.md). Az infrastruktúra üzembe helyezését megelőzően a következő három szerepkör lett meghatározva:
  - A NetOps szerepkör a hálózati környezet, például a tűzfalbeállítások, NSG-beállítások, útválasztás és egyéb hálózatkezelési funkciók kezeléséhez rendelkezik jogosultsággal
  - A SecOps szerepkör az [Azure Security Center](../../../../security-center/security-center-introduction.md) kezeléséhez és üzembe helyezéséhez és [Azure Policy](../../../policy/overview.md)-definíciók meghatározásához szükséges, illetve egyéb, biztonsághoz kapcsolódó jogosultságokkal is rendelkezik
  - A SysOps szerepkör az egyéb működési jogosultságok mellett az [Azure Policy](../../../policy/overview.md)-definíciók előfizetésen belüli meghatározásához, valamint a [Log Analytics](../../../../azure-monitor/overview.md) teljes környezetre kiterjedő kezeléséhez szükséges jogosultságokkal is rendelkezik
- A [Log Analytics](../../../../azure-monitor/overview.md) elsőként lesz üzembe helyezve az Azure-szolgáltatások közül. Ez biztosítja, hogy a biztonságos üzembe helyezés megkezdésétől fogva az összes művelet és szolgáltatás naplózása egyetlen központi helyen történjen
- Alhálózatokat támogató virtuális hálózat helyszíni adatközponthoz való csatlakozáshoz, bemeneti és kimeneti verem az internetkapcsolathoz, valamint NSG-ket és ASG-ket használó megosztott szolgáltatási alhálózat a teljes körű mikroszegmentáláshoz, amely a következőket foglalja magába:
  - Felügyeleti célra használt jumpbox vagy bástyagazdagép, amely csak a bemeneti verem alhálózatán üzembe helyezett [Azure-tűzfalon](../../../../firewall/overview.md) keresztül érhető el
  - Az Azure Active Directory Domain Servicest (Azure AD DS) futtató két virtuális gép és a DNS csak a jumpboxon keresztül érhető el, és kizárólag úgy konfigurálható, hogy az AD-t VPN-en vagy [ExpressRoute-kapcsolaton](../../../../expressroute/expressroute-introduction.md) keresztül replikálja (ezt a terv nem helyezi üzembe)
  - Az [Azure Net Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) és a standard DDoS Protection használata
- Az [Azure Key Vault](../../../../key-vault/general/overview.md) egy példánya, amely a megosztott szolgáltatások környezetében üzembe helyezett virtuális gépek titkos kulcsainak tárolására szolgál

A fentiek mindegyike teljesíti az [Azure Architecture Center referenciaarchitektúrákkal foglalkozó részében](/azure/architecture/reference-architectures/) közzétett, bevált módszerek követelményeit.

> [!NOTE]
> Az ISO 27001 Megosztott szolgáltatások infrastruktúra adja a számítási feladatok alapvető architektúráját.
> Ezen alapvető architektúrán kívül továbbra is üzembe kell helyezni számítási feladatokat.

További információért tekintse meg a [virtuális adatközpont dokumentációját](/azure/architecture/vdc/).

## <a name="next-steps"></a>További lépések

Ezzel megismerte az ISO 27001 Megosztott szolgáltatások tervmintájának áttekintését és architektúráját.
Következő lépésként tekintse meg az alábbi cikkeket, amelyek a vezérlőelem-leképezést és a minta üzembe helyezését ismertetik:

> [!div class="nextstepaction"]
> [ISO 27001 Megosztott szolgáltatások terve – Vezérlőelem-leképezés](./control-mapping.md)
> [ISO 27001 Megosztott szolgáltatások terve – Üzembehelyezési lépések](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.