---
title: Csoportos elemzési és AI-környezet
titleSuffix: Azure Data Science Virtual Machine
description: Minták a Data Science VM vállalati környezetben való üzembe helyezéséhez.
keywords: Deep learning, AI, adatelemzési eszközök, adatelemzési virtuális gép, térinformatikai elemzés, csoportos adatelemzési folyamat
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: a22b253081a6695758a737a506620f2f56e90227
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519694"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Data Science Virtual Machine-alapú Team Analytics és AI-környezet 
A [Data Science Virtual Machine](overview.md) (DSVM) az Azure platformon gazdag környezetet biztosít, amely a mesterséges intelligencia (AI) és az adatelemzések előre elkészített szoftvereit tartalmazza.

A DSVM hagyományosan egyedi analitikai asztali szolgáltatásként használták. Az egyes adatszakértők a közös, előre elkészített elemzési környezettel vehetik igénybe a hatékonyságot. A nagyméretű elemzési csapatok az adatszakértők és a mesterséges intelligencia-fejlesztők számára tervezik meg a környezeteket, és az ismétlődő témák egyike egy közös elemzési infrastruktúra a fejlesztéshez és kísérletezéshez. Ezt az infrastruktúrát a vállalati informatikai szabályzatoknak megfelelően kezelik, amelyek az adatelemzési és az elemzési csapatok együttműködését és következetességét is megkönnyítik.

A megosztott infrastruktúra lehetővé teszi az elemzési környezet jobb kihasználtságát. Egyes szervezetek a Team-alapú adatelemzési/elemzési infrastruktúrát egy *elemzési homokozóban* hívják meg. Lehetővé teszi az adatszakértők számára a különböző adategységek elérését az adatok gyors megismerése érdekében. Ez a homokozó-környezet lehetővé teszi az adatszakértők számára a kísérletek futtatását, a hipotézisek érvényesítését és a prediktív modellek kialakítását anélkül, hogy ez hatással lenne az éles környezetre.

Mivel a DSVM az Azure-infrastruktúra szintjén működnek, a rendszergazdák könnyedén konfigurálhatják a DSVM, hogy azok megfeleljenek a vállalat informatikai szabályzatának. A DSVM teljes rugalmasságot biztosít a különböző megosztási architektúrák megvalósításában, miközben felügyelt módon biztosít hozzáférést a vállalati adategységekhez.

Ez a szakasz azokat a mintákat és irányelveket ismerteti, amelyek segítségével a DSVM a Team-alapú adatelemzési infrastruktúrában helyezheti üzembe. Mivel ezek a minták építőelemei az Azure-infrastruktúra szolgáltatásként (IaaS) származnak, minden Azure-beli virtuális gépre érvényesek. Ez a cikksorozat a szabványos Azure-infrastruktúra funkcióinak a DSVM való alkalmazására koncentrál.

A vállalati csapat elemzési környezetének kulcsfontosságú építőelemei a következők:

* [Dsvm-készlet](dsvm-pools.md)
* [Közös identitás és hozzáférés egy munkaterülethez a készlet bármelyik Dsvm](dsvm-common-identity.md)
* [Az adatforrásokhoz való biztonságos hozzáférés](dsvm-secure-access-keys.md)


Ez a sorozat az előző témakörökhöz nyújt útmutatást és mutatókat. Nem fedi le az Dsvm nagyméretű vállalati konfigurációkban való üzembe helyezésével kapcsolatos szempontokat és követelményeket. Íme néhány további Azure-erőforrás, amelyet a vállalati DSVM-példányok megvalósítása során használhat:

* [Hálózati biztonság](../../security/fundamentals/network-overview.md)
* [Figyelés](../../azure-monitor/insights/monitor-vm-azure.md) és [felügyelet](../../virtual-machines/maintenance-and-updates.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json%252c%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json%253ftoc%253d%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Naplózás](../../security/fundamentals/log-audit.md)
* [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)
* [Házirend-beállítás és kényszerítés](../../governance/policy/overview.md)
* [Kártevőirtó](../../security/fundamentals/antimalware.md)
* [Titkosítás](../../virtual-machines/windows/disk-encryption-overview.md)
* [Adatfelderítés és-irányítás](../../data-catalog/index.yml)

Végül a [Azure Architecture Center](/azure/architecture/) részletes, végpontok közötti architektúrát és modelleket biztosít a felhőalapú elemzési infrastruktúra kiépítéséhez és kezeléséhez.