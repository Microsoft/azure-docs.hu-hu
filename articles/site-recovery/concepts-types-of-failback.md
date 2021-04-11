---
title: Feladat-visszavétel a vész-helyreállítás során a Azure Site Recovery | Microsoft Docs
description: Ez a cikk áttekintést nyújt a feladat-visszavétel különböző típusairól és azokról a kikötésekről, amelyeket a rendszer a Azure Site Recovery szolgáltatással való vész-helyreállítási folyamat során figyelembe vesz.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: 6f386b34222825b60748b4ca27dcc5c0c2487316
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580854"
---
# <a name="failback-of-vmware-vms-after-disaster-recovery-to-azure"></a>VMware virtuális gépek feladat-visszavétele az Azure-ba történő vészhelyreállítás után

Miután elvégezte a feladatátvételt az Azure-ba a vész-helyreállítási folyamat részeként, visszatérhet a helyszíni helyhez. Két különböző feladat-visszavételi típus lehetséges a Azure Site Recovery: 

- Feladat-visszavétel az eredeti helyre 
- Feladat-visszavétel másik helyre

Ha a feladatátvételt egy VMware virtuális gépen végzi, akkor a feladatátvételt a helyszíni virtuális gépekre is visszaállíthatja, ha még létezik. Ebben az esetben csak a módosítások replikálódnak vissza. Ez a forgatókönyv **eredeti helyre történő helyreállítás** néven ismert. Ha a helyszíni virtuális gép nem létezik, a forgatókönyv egy **másik helyre történő helyreállítás**.

> [!NOTE]
> Csak az eredeti vCenter és a konfigurációs kiszolgálóra lehet visszaadni. Nem telepíthet új konfigurációs kiszolgálót, és nem végezheti el a feladat-visszavételt. Emellett nem adhat hozzá új vCenter a meglévő konfigurációs kiszolgálóhoz és a feladat-visszavételhez az új vCenter.

## <a name="original-location-recovery-olr"></a>Eredeti helyre történő helyreállítás (OLR)
Ha úgy dönt, hogy visszaadja az eredeti virtuális gépet, a következő feltételeknek kell teljesülniük:

* Ha a virtuális gépet egy vCenter-kiszolgáló felügyeli, akkor a fő cél ESX-gazdagépének hozzá kell férnie a virtuális gép adattárához.
* Ha a virtuális gép ESX-gazdagépen található, de nem a vCenter kezeli, akkor a virtuális gép merevlemezének olyan adattárolóban kell lennie, amelyhez a fő cél gazdagépe hozzáférhet.
* Ha a virtuális gép ESX-gazdagépen található, és nem használja a vCenter-t, akkor az ismételt védelem előtt el kell végeznie a fő cél ESX-gazdagépének felderítését. Ez akkor is érvényes, ha a fizikai kiszolgálókat is felhasználja.
* Ha a lemezek már léteznek, és a helyszíni virtuális géphez vannak csatlakoztatva, visszatérhet a virtuális tárolóhelyek hálózatához (vSAN) vagy egy olyan lemezhez, amely a nyers eszköz-hozzárendelésen (RDM) alapul.

> [!IMPORTANT]
> Fontos, hogy engedélyezze a lemez. enableUUID = TRUE értéket, hogy a feladat-visszavétel során a Azure Site Recovery szolgáltatás azonosítani tudja a virtuális gép azon eredeti VMDK, amelybe a függőben lévő módosításokat írni fogja. Ha ez az érték nem TRUE (igaz) értékű, akkor a szolgáltatás a megfelelő helyszíni VMDK próbálja meg azonosítani a legjobb erőfeszítés alapján. Ha a megfelelő VMDK nem található, akkor a rendszer egy további lemezt hoz létre, és a rendszer az adatbevitelt kéri.

## <a name="alternate-location-recovery-alr"></a>Másik helyre történő helyreállítás (ALR)
Ha a helyszíni virtuális gép nem létezik a virtuális gép újbóli megóvása előtt, akkor a forgatókönyv más helyre történő helyreállításnak nevezzük. Az ismételt védelem munkafolyamat újra létrehozza a helyszíni virtuális gépet. Ez a teljes adatletöltést is eredményezi.

* Ha egy másik helyre végzi a feladatátvételt, a virtuális gép ugyanarra az ESX-gazdagépre lesz helyreállítva, amelyen a fő célkiszolgáló telepítve van. A lemez létrehozásához használt adattár a virtuális gép ismételt védetté kiválasztásakor megjelenő adattár lesz.
* Csak egy virtuálisgép-fájlrendszerre (VMFS) vagy egy vSAN adattárra lehet visszaadni. Ha rendelkezik RDM, az ismételt védelem és a feladat-visszavétel nem fog működni.
* Az ismételt védelem egy nagy kezdeti adatátvitelt is magában foglal, amelyet a módosítások követnek. Ez a folyamat azért van, mert a virtuális gép nem létezik a helyszínen. A teljes adatreplikációt vissza kell állítani. Ez az ismételt védelem több időt is igénybe vesz, mint az eredeti helyre történő helyreállítás.
* Nem lehet visszaadni a RDM-alapú lemezeket. VMFS-vagy vSAN-adattáron csak új virtuálisgép-lemezek (VMDK-EK) hozhatók létre.

> [!NOTE]
> Egy fizikai gép, amikor a feladatátvételt az Azure-ba küldi, csak VMware virtuális gép lehet. Ez ugyanazt a munkafolyamatot követi, mint a másik helyre történő helyreállítás. Győződjön meg arról, hogy legalább egy fő célkiszolgáló és a szükséges ESX/ESXi-gazdagépek felderítésére van szükség, amelyekre vissza kell állítania.

## <a name="next-steps"></a>Következő lépések

Kövesse a feladat- [visszavételi művelet](vmware-azure-failback.md)végrehajtásához szükséges lépéseket.

