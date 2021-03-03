---
title: Red Hat számítási feladatok az Azure-on – áttekintés | Microsoft Docs
description: Ismerje meg az Azure-beli Red Hat-termékek ajánlatait.
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: bd8f2e5b825f88d1b0e04ed231b85296ac47e998
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676047"
---
# <a name="red-hat-workloads-on-azure"></a>Red Hat számítási feladatok az Azure-ban

A Red Hat számítási feladatait az Azure különféle kínálatai támogatják. A Red Hat Enterprise Linux-(RHEL-) lemezképek a RHEL-munkaterhelések középpontjában állnak, mint a Red Hat Update Infrastructure (RHUI).

## <a name="red-hat-enterprise-linux-images"></a>Rendszerképek Red Hat Enterprise Linux

Az Azure széles körben kínál RHEL-lemezképeket az Azure-ban. Ezeket a lemezképeket két különböző licencelési modell teszi elérhetővé: utólagos elszámolású és saját előfizetéssel (BYOS). Az Azure-ban új RHEL-lemezképek jelennek meg, amikor a rendszer az új RHEL-verziók kiadását és frissítését a teljes életciklusa során, szükség szerint

### <a name="pay-as-you-go-images"></a>Utólagos elszámolású képek

Az Azure számos RHEL-előfizetéssel elvégezhető képet biztosít. Ezek a lemezképek megfelelően jogosultak a RHEL, és a frissítések forrásaként vannak csatolva (Red Hat frissítési infrastruktúra). Ezek a lemezképek prémium díjat számítanak fel a RHEL jogosultságok és frissítések esetében. Az utólagos elszámolású RHEL a következők:

* Szabványos RHEL.
* RHEL az SAP-hoz.
* RHEL az SAP számára magas rendelkezésre állású és frissítési szolgáltatásokkal.

Az utólagos elszámolású képeket érdemes lehet használni, ha nem szeretné, hogy a megfelelő számú előfizetésre ne kelljen külön fizetni.

### <a name="red-hat-gold-images"></a>Red Hat Gold-képek

Az Azure Red Hat Gold images ( `rhel-byos` )-t is kínál. Ezek a képek hasznosak lehetnek a már meglévő Red Hat-előfizetésekkel rendelkező ügyfelek számára, és ezeket az Azure-ban szeretné használni. Az Azure-ban való használat előtt engedélyeznie kell a Red Hat Cloud Access meglévő Red Hat-előfizetéseit. A rendszerképekhez való hozzáférés automatikusan megtörténik, ha a Red Hat-előfizetések engedélyezve vannak a Felhőbeli hozzáféréshez, és megfelelnek a jogosultsági követelményeknek. Ezeknek a lemezképeknek a használatával az ügyfél elkerülheti a kettős számlázást, amely az utólagos elszámolású képek használatából merülhet fel.
* Megtudhatja, hogyan [engedélyezheti a Red Hat-előfizetéseket a Felhőbeli hozzáféréshez az Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/enabling-and-maintaining-subs_cloud-access)-ban.
* Megtudhatja, hogyan [keresheti meg a Red Hat Gold images-képeket a Azure Portalban, az Azure CLI-ben vagy a PowerShell-parancsmagban](./byos.md).

> [!NOTE]
> A kettős számlázás akkor merül fel, amikor egy felhasználó kétszer fizet a RHEL-előfizetések esetében. Ez a forgatókönyv általában akkor fordul elő, ha egy ügyfél Red Hat Subscription-Managert használ a RHEL utólagos elszámolású virtuális gépen való jogosultsághoz. Ha például egy Subscription-Managert használó ügyfél az SAP-csomagokra vonatkozó jogosultságot csatol egy RHEL utólagos elszámolású rendszerképhez, akkor közvetve nem számítunk fel díjat, mert kétszer fizetnek a RHEL. Az utólagos elszámolású prémium és az SAP-előfizetésen keresztül egyszer kell fizetniük. Ez a forgatókönyv nem BYOS meg a rendszerkép felhasználóit.

### <a name="generation-2-images"></a>2. generációs rendszerképek

A 2. generációs virtuális gépek (VM-EK) újabb funkciókat biztosítanak az 1. generációs virtuális gépekhez képest. További információ: [2. generációs dokumentáció](../../generation-2.md). A RHEL-rendszerkép szempontjából a legfontosabb különbség az, hogy a 2. generációs virtuális gépek a BIOS belső vezérlőprogram-felülete helyett UEFI-t használnak. A fő rendszerindító rekord (MBR) helyett GUID partíciós táblát (GPT) is használnak a rendszerindítási időben. A GPT használata többek között a 2 TB-nál nagyobb operációsrendszer-lemezeket is lehetővé teszi. Emellett a [Mv2 sorozatú virtuális gépek](../../mv2-series.md) csak a 2. generációs lemezképeken futnak.

A 2. generációs RHEL-lemezképek az Azure piactéren érhetők el. Az Azure CLI használatakor megjelenő lemezképek listájában keresse meg a "Gen2" kifejezést a lemezkép SKU-ban. A 2. generációs virtuális gépek üzembe helyezéséhez nyissa meg a virtuális gép üzembe helyezési folyamatának **speciális** lapját.

## <a name="red-hat-update-infrastructure"></a>A Red Hat frissítési infrastruktúrája

Az Azure csak az utólagos elszámolású RHEL virtuális gépekhez nyújt Red Hat frissítési infrastruktúrát. A RHUI hatékonyan tükrözi a Red Hat CDNs, de csak az Azure Pay-as-you-go RHEL virtuális gépek számára érhető el. A megfelelő csomagokhoz férhet hozzá, attól függően, hogy melyik RHEL-lemezképet telepítette. Az SAP-rendszerkép RHEL például az alap RHEL-csomagok mellett az SAP-csomagokat is elérheti.

### <a name="rhui-update-behavior"></a>RHUI-frissítési viselkedés

A RHUI-frissítéshez kapcsolódó RHEL-lemezképek alapértelmezés szerint a RHEL legújabb másodlagos verziójára csatlakoznak, amikor a `yum update` fut. Ez azt jelenti, hogy egy RHEL 7,4 virtuális gép frissíthető a RHEL 7,7-re, ha egy `yum update` művelet fut rajta. Ez a viselkedés a RHUI kialakításán alapul. A frissítési viselkedés enyhítése érdekében váltson át a normál RHEL-adattárakból a [kiterjesztett frissítési támogatási tárházra](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Következő lépések

* További információ az [Azure-beli RHEL-lemezképekről](./redhat-images.md).
* További információ a [Red Hat frissítési infrastruktúráról](./redhat-rhui.md).
* További információ a [Red Hat Gold ( `rhel-byos` ) ajánlatról](./byos.md).