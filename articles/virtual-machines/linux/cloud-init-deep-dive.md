---
title: A Cloud-init ismertetése
description: Az Azure-beli virtuális gépek Cloud-init használatával történő üzembe helyezésének megismerése.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 94dd57310375febb4bc9a55efa704a5fbf4e80e8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559443"
---
# <a name="diving-deeper-into-cloud-init"></a>Mélyebb búvárkodás a Cloud-init használatával
Ha többet szeretne megtudni a [Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) szolgáltatásról, vagy mélyebb szinten hárítsa el a hibát, meg kell ismernie, hogyan működik. Ez a dokumentum kiemeli a fontos részeket, és ismerteti az Azure-specifikus elemeket.

Ha a Cloud-init egy általánosított rendszerkép részét képezi, és egy virtuális gép jön létre ebből a rendszerképből, a rendszer feldolgozza a konfigurációkat, és a kezdeti rendszerindítás során 5 fázisban futtatja őket. Ezek a szakaszok azt mutatják be, hogy a Cloud-init milyen ponton fogja alkalmazni a konfigurációkat. 


## <a name="understand-cloud-init-configuration"></a>Cloud-Init konfiguráció ismertetése
Ha egy virtuális gépet egy platformon futtat, a Cloud-init alkalmazásnak több konfigurációt kell alkalmaznia, mint a képfogyasztót, a `User data` (customData), amely támogatja a több formátumot, ezek dokumentálva vannak. [](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats) A további konfigurációkhoz parancsfájlokat (/var/lib/Cloud/Scripts) is hozzáadhat és futtathat, az alábbi témakör részletesebben tárgyalja.

Néhány konfiguráció már be van építve az Azure Marketplace-re, amely a Cloud-init használatával érkezik, például:

1. **Felhőalapú adatforrás** – a Cloud-init olyan kódot tartalmaz, amely képes kommunikálni a felhőalapú platformokkal, ezeket "adatforrásoknak" nevezzük. Ha egy virtuális gépet az [Azure](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure)-beli Cloud-init rendszerképből hoztak létre, a Cloud-init betölti az Azure-adatforrást, amely az Azure metaadat-végpontokkal fog működni, hogy megkapja a virtuális gép-specifikus konfigurációt.
2. **Futásidejű konfiguráció** (/Run/Cloud-init)
3. A **rendszerkép konfigurációja** (/etc/Cloud), például: `/etc/cloud/cloud.cfg` `/etc/cloud/cloud.cfg.d/*.cfg` . Egy példa arra, hogy ez hogyan használható az Azure-ban, a Cloud-init által használt Linux operációsrendszer-lemezképek esetében gyakori, hogy rendelkezzen egy Azure DataSource direktívával, amely közli a Cloud-init, hogy milyen adatforrást használ, így a Cloud-init Time:

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>Cloud-init rendszerindítási szakaszok (konfiguráció feldolgozása)

A Cloud-init használatával történő kiépítés esetén a rendszerindításnak 5 fázisa van, amely a konfigurációt és a naplókban látható.

1. [Generátor szakasza](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator): elindul a Cloud-init rendszergenerátor, és meghatározza, hogy a Cloud-init legyen a rendszerindítási célok között, és ha igen, lehetővé teszi a Cloud-init használatát. 

2. [Felhő-inicializálás helyi szakasza](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local): itt a Cloud-init megkeresi a helyi "Azure" adatforrást, amely engedélyezi a Cloud-init felületet az Azure-hoz, és egy hálózati konfigurációt alkalmaz, beleértve a tartalékot is.

3. [Cloud-init init Stage (hálózat)](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network): a hálózatkezelésnek online állapotban kell lennie, és a hálózati adapter és az útválasztási tábla információit kell létrehoznia. Ebben a szakaszban a/etc/Cloud/Cloud.cfg-ben felsorolt modulok lesznek `cloud_init_modules` futtatva. Az Azure-beli virtuális gép csatlakoztatva lesz, az ideiglenes lemez formázása megtörténik, a hostname beállítása pedig más feladatokkal együtt.

   Ezek a következők `cloud_init_modules` :
   
   ```bash
   - migrator
   - seed_random
   - bootcmd
   - write-files
   - growpart
   - resizefs
   - disk_setup
   - mounts
   - set_hostname
   - update_hostname
   - ssh
   ```
   
   Ezen szakasz után a Cloud-init jelzi az Azure platformot, amelyet a virtuális gép üzembe helyezése sikerült. Előfordulhat, hogy egyes modulok sikertelenek voltak, nem minden modul hibája kiépítési hibát eredményezhet.

4. [Cloud-init konfigurációs szakasz](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config): ebben a fázisban a/etc/Cloud/Cloud.cfg-ben `cloud_config_modules` definiált és felsorolt modulok lesznek futtatva.


5. A [Cloud-init végleges szakasza](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final): az utolsó szakaszban a/etc/Cloud/Cloud.cfg-ben felsorolt modulokat `cloud_final_modules` futtatja a rendszer. Itt a rendszerindítási folyamat futtatásához későn kell futtatni a modulokat, például a csomagok telepítését és a parancsfájlok futtatását. 

   -   Ebben a szakaszban parancsfájlokat futtathat, ha a következő könyvtárakba helyezi őket `/var/lib/cloud/scripts` :
   - `per-boot` -parancsfájlok ebben a könyvtárban, Futtatás minden újraindításkor
   - `per-instance` – a könyvtáron belüli parancsfájlok futtatása új példány első indításakor
   - `per-once` – a könyvtárban lévő parancsfájlok csak egyszer futnak

## <a name="next-steps"></a>Következő lépések

A [Cloud-init hibaelhárítása](cloud-init-troubleshooting.md).
