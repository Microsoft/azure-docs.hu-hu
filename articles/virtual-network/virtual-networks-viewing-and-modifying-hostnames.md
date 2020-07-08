---
title: Állomásnevek megtekintése és módosítása | Microsoft Docs
description: Azure-beli virtuális gépek, webes és feldolgozói szerepkörök állomásneveinak megtekintése és módosítása a névfeloldáshoz
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 3f8c35604af9780fb4299bbd7bfd87c3d93ac537
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84702839"
---
# <a name="viewing-and-modifying-hostnames"></a>Állomásnevek megtekintése és módosítása
Annak engedélyezéséhez, hogy a szerepkör-példányok állomásnévre legyenek hivatkozva, az egyes szerepkörökhöz tartozó szolgáltatás konfigurációs fájljában meg kell adnia az állomásnév értékét. Ezt úgy teheti meg, hogy hozzáadja a kívánt gazdagép nevét a **szerepkör** elem **vmName** attribútumához. A **vmName** attribútum értéke az egyes szerepkör-példányok állomásneve alapjául szolgál. Ha például a **vmName** *webrole* , és a szerepkör három példánya van, a példányok állomásneve a *webrole0*, a *webrole1 webes*és a *webrole2*lesz. A konfigurációs fájlban nem kell megadnia a virtuális gépek gazdagépének nevét, mert a virtuális gép állomásneve a virtuális gép neve alapján van feltöltve. Microsoft Azure szolgáltatás konfigurálásával kapcsolatos további információkért lásd: [Azure szolgáltatás konfigurációs sémája (. Cscfg fájl)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Állomásnevek megtekintése
A Cloud Service-ben a virtuális gépek és a szerepkör-példányok állomásneve a következő eszközökkel tekinthető meg.

### <a name="service-configuration-file"></a>Szolgáltatás konfigurációs fájlja
A szolgáltatás konfigurációs fájlját letöltheti egy központilag telepített szolgáltatáshoz a szolgáltatás **Konfigurálás** paneljéről a Azure Portal. Ezután megkeresheti a **vmName** attribútumot a **szerepkör neve** elemnél az állomásnév megtekintéséhez. Ne feledje, hogy ez az állomásnév az egyes szerepkör-példányok állomásneve alapjául szolgál. Ha például a **vmName** *webrole* , és a szerepkör három példánya van, a példányok állomásneve a *webrole0*, a *webrole1 webes*és a *webrole2*lesz.

### <a name="remote-desktop"></a>Távoli asztal
Miután engedélyezte a Távoli asztal (Windows), a Windows PowerShell távelérés (Windows) vagy az SSH (Linux és Windows) kapcsolatait a virtuális gépekkel vagy a szerepkör-példányokkal, különböző módokon tekintheti meg az állomásnévt aktív Távoli asztal kapcsolattal:

* Írja be a hostname parancsot a parancssorba vagy az SSH-terminálba.
* Írja be a parancssorba a következőt: ipconfig/all (csak Windows).
* Tekintse meg a számítógép nevét a rendszerbeállításokban (csak Windows rendszerben).

### <a name="azure-service-management-rest-api"></a>Azure Service Management REST API
A REST-ügyféltől kövesse az alábbi utasításokat:

1. Ellenőrizze, hogy van-e ügyféltanúsítvány a Azure Portalhoz való kapcsolódáshoz. Az ügyféltanúsítvány beszerzéséhez kövesse az [útmutató: közzétételi beállítások és előfizetési információk letöltése és importálása](https://msdn.microsoft.com/library/dn385850.aspx)című cikkben ismertetett lépéseket. 
2. Állítson be egy x-MS-Version nevű fejléc-bejegyzést 2013-11-01 értékkel.
3. Kérelem küldése a következő formátumban: https: \/ /Management.Core.Windows.net/ \<subscrition-id\> /Services/hostedservices/ \<service-name\> ? beágyazás-detail = True
4. Keresse meg a **hostname** elemet az egyes **RoleInstance** elemekhez.

> [!WARNING]
> A felhőalapú szolgáltatás belső tartományának utótagját a REST-hívási válaszban is megtekintheti a **InternalDnsSuffix** elem ellenőrzésével, vagy az ipconfig/all parancssorból való futtatásával egy távoli asztal-munkamenetben (Windows), vagy a Cat/etc/RESOLV.conf egy SSH-terminálról (Linux) való futtatásával.
> 
> 

## <a name="modifying-a-hostname"></a>Állomásnév módosítása
A módosított szolgáltatás-konfigurációs fájl feltöltésével vagy a számítógép Távoli asztal munkamenetből való átnevezésével módosíthatja a virtuális gépek vagy a szerepkör-példányok állomásnevét.

## <a name="next-steps"></a>További lépések
[Névfeloldás (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Azure-szolgáltatás konfigurációs sémája (. cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Azure Virtual Network konfigurációs séma](https://go.microsoft.com/fwlink/?LinkId=248093)

[DNS-beállítások megadása hálózati konfigurációs fájlok használatával](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

