---
title: A klasszikus üzembe helyezés előnyei a Azure AD Domain Servicesban | Microsoft Docs
description: További információ a Azure Active Directory Domain Services klasszikus üzembe helyezésének előnyeiről a Resource Manager-alapú üzemi modellre
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: justinha
ms.openlocfilehash: 8cc5f5ebf389d35df02474d0561dc7827cde4d0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620086"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>A Klasszikusról Resource Manager-alapú üzemi modellre való Migrálás előnyei Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) lehetővé teszi egy meglévő felügyelt tartomány áttelepítését, amely a klasszikus üzemi modellt használja a Resource Manager-alapú üzemi modellre. Az Azure AD DS a Resource Manager-alapú üzemi modellt használó felügyelt tartományok olyan további funkciókat biztosítanak, mint például a részletes jelszóházirendek, a naplók és a fiókzárolás elleni védelem.

Ez a cikk az áttelepítés előnyeit ismerteti. Első lépésként tekintse [meg a Azure ad Domain Services áttelepítését a klasszikus virtuális hálózati modellből a Resource Managerbe][howto-migrate]című témakört.

> [!NOTE]
> A (z) 2017-es verziójában Azure AD Domain Services elérhetővé vált a Azure Resource Manager hálózaton lévő gazdagép számára. Azóta egy biztonságosabb szolgáltatást hoztunk létre a Azure Resource Manager modern képességeinek használatával. Mivel Azure Resource Manager központi telepítések teljes mértékben lecserélik a klasszikus üzemelő példányokat, az Azure-AD DS a klasszikus virtuális hálózati telepítések 2023. március 1-től megszűnnek.
>
> További információkért lásd a [hivatalos elavult közleményt](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/) .

## <a name="migration-benefits"></a>Áttelepítési előnyök

Az áttelepítési folyamat egy meglévő felügyelt tartományt használ, amely a klasszikus telepítési modellt használja, és a Resource Manager-alapú üzemi modellt használja. Amikor felügyelt tartományt telepít át a Klasszikusról Resource Manager-alapú üzemi modellre, nem kell újracsatlakoztatni a gépeket a felügyelt tartományhoz, vagy törölnie kell a felügyelt tartományt, és létre kell hoznia egy teljesen új példányt. A virtuális gépek továbbra is csatlakozni fognak a felügyelt tartományhoz az áttelepítési folyamat végén.

Az áttelepítés után az Azure AD DS számos olyan funkciót biztosít, amelyek csak a Resource Manager-alapú üzemi modellt használó tartományokhoz érhetők el, például a következőkkel:

* A [részletes jelszóházirendek támogatása][password-policy].
* Gyorsabb szinkronizációs sebesség az Azure AD és a Azure AD Domain Services között.
* Két új [attribútum, amelyek az Azure ad][attributes]  -  *managerből* és az *AlkalmazottKód*-ből szinkronizálhatók.
* Hozzáférés a nagyobb teljesítményű tartományvezérlőkhöz [az SKU frissítésekor][skus].
* AD-fiók zárolásának védelme.
* [E-mail-értesítések a felügyelt tartományon lévő riasztásokról][email-alerts].
* [Az Azure-munkafüzetek és az Azure monitor használatával megtekintheti a naplókat és a bejelentkezési tevékenységeket][workbooks].
* A támogatott régiókban [Azure Availability Zones][availability-zones].
* Integráció más Azure-termékekkel, például a [Azure Files][azure-files], a [HD-ismeretekkel][hd-insights]és a [Windowsos virtuális asztallal][wvd].
* A támogatás több telemetria férhet hozzá, és hatékonyabban tudja elhárítani a hibakeresést.
* A felügyelt tartományvezérlőkön lévő adatok [Azure Managed Disks][managed-disks] használatával történő titkosítása.

A Resource Manager-alapú üzemi modellt használó felügyelt tartományok a legújabb új funkciókkal naprakészek maradnak. A klasszikus üzemi modellt használó felügyelt tartományok esetében nem érhetők el új funkciók.

## <a name="next-steps"></a>Következő lépések

Első lépésként tekintse [meg a Azure ad Domain Services áttelepítését a klasszikus virtuális hálózati modellből a Resource Managerbe][howto-migrate]című témakört.

<!-- LINKS - INTERNAL -->
[password-policy]: password-policy.md
[skus]: change-sku.md
[email-alerts]: notifications.md
[workbooks]: use-azure-monitor-workbooks.md
[azure-files]: ../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md
[hd-insights]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[wvd]: ../virtual-desktop/overview.md
[availability-zones]: ../availability-zones/az-overview.md
[howto-migrate]: migrate-from-classic-vnet.md
[attributes]: synchronization.md#attribute-synchronization-and-mapping-to-azure-ad-ds
[managed-disks]: ../virtual-machines/managed-disks-overview.md
