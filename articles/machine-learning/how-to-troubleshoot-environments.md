---
title: Környezeti rendszerképek – problémamegoldás
titleSuffix: Azure Machine Learning
description: Útmutató a környezeti rendszerkép-buildek és-csomagok telepítésével kapcsolatos hibák elhárításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: ec0c7d64f2145cdaf594cb903c072984f4d376a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519129"
---
# <a name="troubleshoot-environment-image-builds"></a>Környezeti rendszerkép-buildek – problémamegoldás

Ismerje meg, hogy miként lehet elhárítani a Docker-környezet rendszerkép-buildekkel és-csomagokkal kapcsolatos problémákat.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).
* A [Azure Machine learning SDK](/python/api/overview/azure/ml/install).
* Az [Azure CLI](/cli/azure/install-azure-cli)-vel.
* A [Azure Machine learning CLI-bővítménye](reference-azure-machine-learning-cli.md).
* Helyi hibakereséshez rendelkeznie kell egy működő Docker-telepítéssel a helyi rendszeren.

## <a name="docker-image-build-failures"></a>Docker-rendszerkép-létrehozási hibák
 
A legtöbb rendszerkép-létrehozási hiba esetén a rendszerkép-létrehozási naplóban találhatja meg a kiváltó okot.
Keresse meg a rendszerkép-létrehozási naplót a Azure Machine Learning portálról (20 \_ rendszerkép- \_ Build \_log.txt) vagy a Azure Container Registry feladat futtatási naplóiból.
 
Általában egyszerűbb a hibák helyi reprodukálása. Tekintse át a hiba típusát, és próbálja ki a következők egyikét `setuptools` :

- Conda-függőség telepítése helyileg: `conda install suspicious-dependency==X.Y.Z` .
- Egy pip-függőség helyi telepítése: `pip install suspicious-dependency==X.Y.Z` .
- Próbálja meg megvalósulni a teljes környezetet: `conda create -f conda-specification.yml` .

> [!IMPORTANT]
> Győződjön meg arról, hogy a helyi számítási fürtön lévő platform és tolmács megfelel a távoli számítási fürtön lévőknek. 

### <a name="timeout"></a>Időtúllépés 
 
A következő hálózati problémák okozhatnak időtúllépési hibákat:

- Alacsony Internet-sávszélesség
- Kiszolgálói problémák
- Nagy függőségek, amelyek nem tölthetők le a megadott Conda vagy pip időtúllépési beállításokkal
 
Az alábbi példához hasonló üzenetek a problémát jelezik:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Ha hibaüzenetet kap, próbálkozzon az alábbi lehetséges megoldások egyikével:
 
- Próbáljon ki egy másik forrást, például a tükrözéseket, az Azure Blob Storage vagy más Python-hírcsatornákat a függőséghez.
- Conda vagy pip frissítése. Ha egyéni Docker-fájlt használ, frissítse az időtúllépési beállításokat.
- Egyes pip-verziók ismert problémákkal rendelkeznek. Vegye fontolóra a pip adott verziójának hozzáadását a környezeti függőségekhez.

### <a name="package-not-found"></a>A csomag nem található

A rendszerkép-létrehozási hibák leggyakoribb hibái a következők:

- Nem található a Conda csomag:

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- Nem található a megadott pip-csomag vagy-verzió:

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- Helytelen beágyazott pip-függőség:

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

Győződjön meg arról, hogy a csomag létezik a megadott forrásokon. Pip- [Keresés](https://pip.pypa.io/en/stable/reference/pip_search/) használata a pip-függőségek ellenőrzéséhez:

- `pip search azureml-core`

Conda-függőségek esetén használja a [Conda Search kifejezést](https://docs.conda.io/projects/conda/en/latest/commands/search.html):

- `conda search conda-forge::numpy`

További lehetőségek:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>A telepítő megjegyzései

Győződjön meg arról, hogy a szükséges terjesztés létezik a megadott platformhoz és a Python-értelmező verziójához.

A pip-függőségek eléréséhez nyissa meg a t, és ellenőrizze, hogy elérhető-e `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` a szükséges verzió. A gombra kattintva https://pypi.org/project/azureml-core/1.11.0/#files megtekintheti a példát.

Conda-függőségek esetén a csomag a csatorna adattárában található.
Az anaconda, Inc. által karbantartott csatornákon keresse meg az [anaconda-csomagok lapot](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Pip-csomag frissítése

Egy pip csomag telepítése vagy frissítése során előfordulhat, hogy a feloldónak frissítenie kell egy már telepített csomagot az új követelmények kielégítése érdekében.
Az Eltávolítás a pip verziójával vagy a függőség telepítésének módjával kapcsolatos különféle okok miatt sikertelen lehet.
A leggyakoribb forgatókönyv az, hogy a Conda által telepített függőséget nem lehetett eltávolítani a pip használatával.
Ebben a forgatókönyvben érdemes lehet eltávolítani a függőséget a használatával `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Telepítési problémák

Bizonyos telepítési verziók olyan problémákkal rendelkeznek a csomag-feloldásokban, amelyek létrehozási hibát okozhatnak.

Ha egyéni alaprendszerképet vagy Docker használ, javasoljuk, hogy a Conda 4.5.4 vagy újabb verzióját használja.

A pip-függőségek telepítéséhez pip-csomag szükséges. Ha nincs megadva verzió a környezetben, a rendszer a legújabb verziót fogja használni.
Javasoljuk, hogy a pip ismert verzióját használja az átmeneti problémák elkerüléséhez, illetve az eszköz legújabb verziójának megváltoztatásához.

Ha a következő üzenet jelenik meg, érdemes megfontolnia a pip-verzió rögzítését a környezetben:

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

Pip-alfolyamati hiba:
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

Ha feloldhatatlan ütközik a függőségek között, a pip telepítése végtelen hurokba helyezhető. Ha helyileg dolgozik, a 20,3-es verzióra a pip verzióját <. YAML-fájlból létrehozott Conda-környezetekben ez a probléma csak akkor jelenik meg, ha a Conda-Forge a legmagasabb prioritású csatorna. A probléma enyhítése érdekében explicit módon adja meg a pip < 20,3 (! = 20,3 vagy = 20.2.4 PIN-kódot más verzióra) a Conda-specifikáció fájljában.

## <a name="service-side-failures"></a>Szolgáltatási oldali hibák

Tekintse meg az alábbi forgatókönyveket a lehetséges szolgáltatási oldali hibák elhárításához.

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>Nem lehet lekérni egy rendszerképet egy tároló-beállításjegyzékből, vagy nem oldható fel a tároló beállításjegyzékének címe.

Lehetséges problémák:
- Előfordulhat, hogy a tároló-beállításjegyzék elérési útjának neve nem oldható fel megfelelően. Ellenőrizze, hogy a képnevek dupla perjelet használnak-e, és a vágás iránya a Linuxon és a Windows-gazdagépeken helyes-e.
- Ha egy virtuális hálózat mögötti tároló-beállításjegyzék egy nem [támogatott régióban](../private-link/private-link-overview.md#availability)található privát végpontot használ, konfigurálja a tároló-beállításjegyzéket a portálon a szolgáltatás végpontjának (nyilvános hozzáférés) használatával, és próbálkozzon újra.
- Miután elhelyezte a tároló-beállításjegyzéket egy virtuális hálózat mögött, futtassa a [Azure Resource Manager sablont](./how-to-network-security-overview.md) , hogy a munkaterület képes legyen kommunikálni a tároló beállításjegyzék-példánnyal.

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>Egy munkaterület-tároló beállításjegyzékének 401-as hibát kap

A [ws.sync_keys ()](/python/api/azureml-core/azureml.core.workspace.workspace#sync-keys--)használatával újraszinkronizálhatja a tároló kulcsait.

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>A környezet továbbra is "várakozás más Conda-műveletek befejezésére..." hiba

Ha a rendszerkép létrehozása folyamatban van, a Conda az SDK-ügyfél zárolja. Ha a folyamat összeomlott vagy a felhasználó helytelenül törölte, a Conda zárolt állapotban marad. A probléma megoldásához manuálisan törölje a zárolási fájlt. 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>Az egyéni Docker-rendszerkép nem szerepel a beállításjegyzékben

Ellenőrizze, hogy a [helyes címke](./how-to-use-environments.md#create-an-environment) van-e használatban, és hogy a `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` letiltja a Conda, és a felhasználó telepített csomagjait használja.

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>A következő gyakori virtuális hálózati problémák valamelyikét kapja

- Győződjön meg arról, hogy a Storage-fiók, a számítási fürt és a tároló-beállításjegyzék mind a virtuális hálózat ugyanazon alhálózatán található.
- Ha a tároló-beállításjegyzék egy virtuális hálózat mögött van, nem lehet közvetlenül felhasználni lemezképek létrehozásához. Lemezképek létrehozásához a számítási fürtöt kell használnia.
- Előfordulhat, hogy a tárterületet a virtuális hálózat mögé kell helyezni, ha:
    - Használjon következtetést vagy saját kereket.
    - Lásd: 403 (nem hitelesítő) szolgáltatási hiba.
    - Nem lehet lekérni a képadatokat Azure Container Registryról.

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>A rendszerkép létrehozása meghiúsul, ha a hálózati védett tárolóhoz próbál hozzáférni.

- Azure Container Registry feladatok nem működnek a virtuális hálózatok mögött. Ha a felhasználó egy virtuális hálózat mögött van a tároló beállításjegyzéke, a számítási fürtöt kell használnia egy rendszerkép létrehozásához.
- A tárolónak egy virtuális hálózat mögött kell lennie ahhoz, hogy lekérje a függőségeket.

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>Nem futtathat kísérleteket, ha a tárterületen engedélyezve van a hálózati biztonság

Ha alapértelmezett Docker-rendszerképeket használ, és a felhasználó által felügyelt függőségeket is engedélyezi, használja a MicrosoftContainerRegistry és a AzureFrontDoor. FirstParty [szolgáltatás címkéit](./how-to-network-security-overview.md) a engedélyezési Azure Container Registry és annak függőségeire.

 További információt a [virtuális hálózatok engedélyezése](./how-to-network-security-overview.md)című témakörben talál.

### <a name="you-need-to-create-an-icm"></a>Létre kell hoznia egy ICM-t

Ha egy ICM-et hoz létre/rendel a Metaadattár-hez, a CSS támogatási jegyét is megtudhatja, hogy jobban megértse a problémát.

## <a name="next-steps"></a>Következő lépések

- [Gépi tanulási modell betanítása a virágok kategorizálására](how-to-train-scikit-learn.md)
- [Gépi tanulási modell betanítása egyéni Docker-rendszerkép használatával](how-to-train-with-custom-image.md)
