---
title: 'Oktatóanyag: helyszíni adatáttelepítés az Azure Storage-ba a AzCopy használatával | Microsoft Docs'
description: Ebben az oktatóanyagban az AzCopyval fog adatokat migrálni vagy másolni blob-, tábla- és fájltartalmakból és -tartalmakba. Könnyedén migrálhatja adatait egy helyszíni tárolóból az Azure Storage-ba.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 682394329205d74859c1af0c0a68a37539da7872
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881028"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Oktatóanyag: helyszíni adatáttelepítés a felhőalapú tárolóba a AzCopy használatával

Az AzCopy egy olyan parancssori eszköz, amely lehetővé teszi, hogy egyszerű parancsokkal másoljon adatokat az Azure Blob Storage-ba, az Azure Files-ba és az Azure Table Storage-ba, illetve ezekből a tárolókból máshová. A parancsok úgy lettek kialakítva, hogy optimális teljesítményt nyújtsanak. Az AzCopyval az adatokat egy fájlrendszer és egy tárfiók, illetve több tárfiók között is másolhatja. Az AzCopyval helyi (helyszíni) adattárolóból tárfiókba másolhat adatokat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tárfiók létrehozása. 
> * Minden adat feltöltése az AzCopy segítségével.
> * Az adatok módosítása tesztelési célból.
> * Ütemezett vagy cron feladat létrehozása az új feltölthető fájlok azonosításához.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez töltse le a AzCopy legújabb verzióját. Lásd: Ismerkedés [a AzCopy szolgáltatással](storage-use-azcopy-v10.md).

Ha Windows rendszert használ, szüksége lesz a [Schtasks](/windows/win32/taskschd/schtasks) programra, mert az oktatóanyag azt használja a feladatok ütemezéséhez. A Linux-felhasználók e célra a crontab parancsot használják.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Tároló létrehozása

Az első lépés a tároló létrehozása, mert a blobokat mindig egy tárolóba kell feltölteni. A tárolók segítségével úgy történik a blobcsoportok rendszerezése, ahogy a fájljait rendezi mappákba a számítógépén.

Kövesse az alábbi lépéseket a tároló létrehozásához:

1. A főoldalon kattintson a **Tárfiókok** gombra, majd válassza ki a létrehozott tárfiókot.
2. A **Szolgáltatások** területen kattintson a **Blobok** elemre, majd válassza a **Tároló** lehetőséget.

   ![A tárolók létrehozását bemutató képernyőkép](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
A tárolók nevének betűvel vagy számmal kell kezdődnie. A név csak betűkből, számokból és kötőjel (-) karakterből állhat. A blobok és tárolók elnevezésével kapcsolatos szabályok teljes listáját lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

## <a name="download-azcopy"></a>Az AzCopy letöltése

Töltse le az AzCopy v10 végrehajtható fájlt.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Helyezze a AzCopy-fájlt bárhol a számítógépre. Adja hozzá a fájl helyét a rendszerútvonal-változóhoz, hogy ezt a végrehajtható fájlt a számítógép bármelyik mappájából megtekintheti.

## <a name="authenticate-with-azure-ad"></a>Hitelesítés az Azure AD-vel

Először rendelje hozzá a [Storage blob adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) szerepkört az identitásához. Lásd: [a Azure Portal használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-információk eléréséhez](./storage-auth-aad-rbac-portal.md).

Ezután nyisson meg egy parancssort, írja be a következő parancsot, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login
```

Ez a parancs egy hitelesítő kódot és egy webhely URL-címét adja vissza. Nyissa meg a webhelyet, adja meg a kódot, majd válassza a **tovább** gombot.

![A bejelentkezési kérést ábrázoló képernyőfelvétel](media/storage-use-azcopy-v10/azcopy-login.png)

Ekkor megjelenik egy bejelentkezési ablak. Ebben az ablakban jelentkezzen be az Azure-fiókjába az Azure-fiókja hitelesítő adataival. Miután sikeresen bejelentkezett, lezárhatja a böngészőablakot, és megkezdheti a AzCopy használatát.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Egy mappa tartalmának feltöltése a Blob Storage-ba

Az AzCopyval feltöltheti egy mappa összes fájlját a Blob Storage-ba [Windows](./storage-use-azcopy-v10.md) vagy [Linux](./storage-use-azcopy-v10.md) rendszeren. Egy mappa összes blobjának feltöltéséhez írja be a következő AzCopy-parancsot:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Cserélje le a `<local-folder-path>` helyőrzőt a fájlokat tartalmazó mappa elérési útjára (például: `C:\myFolder` vagy `/mnt/myFolder` ).

* Cserélje le a `<storage-account-name>` helyőrzőt a Storage-fiók nevére.

* Cserélje le a `<container-name>` helyőrzőt a létrehozott tároló nevére.

Ha a megadott könyvtár tartalmát rekurzív módon szeretné feltölteni a blob Storage-ba, adja meg a `--recursive` beállítást. Ha ezzel a kapcsolóval futtatja az AzCopy-t, a rendszer az összes almappát és azok fájljait is feltölti.

## <a name="upload-modified-files-to-blob-storage"></a>Módosított fájlok feltöltése a Blob Storage-ba

Az AzCopyval a legutóbbi módosításuk ideje alapján is feltölthet fájlokat. 

Ha szeretné kipróbálni ezt a funkciót, tesztelési céllal módosítson vagy hozzon létre fájlokat a forráskönyvtárban. Ezután használja a AzCopy `sync` parancsot.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Cserélje le a `<local-folder-path>` helyőrzőt a fájlokat tartalmazó mappa elérési útjára (például: `C:\myFolder` vagy `/mnt/myFolder` ).

* Cserélje le a `<storage-account-name>` helyőrzőt a Storage-fiók nevére.

* Cserélje le a `<container-name>` helyőrzőt a létrehozott tároló nevére.

További információ a `sync` parancsról: [fájlok szinkronizálása](./storage-use-azcopy-v10.md#transfer-data).

## <a name="create-a-scheduled-task"></a>Ütemezett feladat létrehozása

Létrehozhat olyan ütemezett vagy cron feladatot, amely egy AzCopy parancssori szkriptet futtat. A szkript adott időközönként azonosítja és feltölti az új helyszíni adatokat a felhőtárolóba.

Másolja az AzCopy-parancsot egy szövegszerkesztőbe. Frissítse az AzCopy-parancs paraméterértékeit a megfelelő értékekre. Mentse a fájlt `script.sh` (Linux) vagy `script.bat` (Windows) néven az AzCopyhoz. 

Ezek a példák feltételezik, hogy a mappa neve `myFolder` , a Storage-fiók neve, `mystorageaccount` a tároló neve pedig `mycontainer` .

> [!NOTE]
> A Linux-példa egy SAS-tokent fűz hozzá. Meg kell adnia egyet a parancsban. Az AzCopy v10 jelenlegi verziója nem támogatja az Azure AD-hitelesítést a cron-feladatokban.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true
```

# <a name="windows"></a>[Windows](#tab/windows)

```bash
azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true
```

---

Ebben az oktatóanyagban a [Schtasks](/windows/win32/taskschd/schtasks) eszközt használjuk egy ütemezett feladat létrehozásához Windows rendszeren, és a [Crontab](http://crontab.org/) parancsot egy cron feladat létrehozásához Linux rendszeren.

 A **Schtasks** lehetővé teszi a rendszergazdák számára ütemezett feladatok létrehozását, törlését, lekérdezését, módosítását, futtatását és befejezését helyi vagy távoli számítógépeken. A **Cron** lehetővé teszi a Linux- és Unix-felhasználók számára parancsok vagy szkriptek futtatását adott dátumon és időpontban [cron-kifejezések](https://en.wikipedia.org/wiki/Cron#CRON_expression) használatával.

# <a name="linux"></a>[Linux](#tab/linux)

Cron feladat létrehozásához Linux rendszeren írja be az alábbi parancsot egy terminálon:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Ha a parancsban megadja az `*/5 * * * *` cron-kifejezést, az azt jelenti, hogy a `script.sh` héjszkriptnek öt percenként kell futnia. A szkriptet ütemezheti úgy, hogy adott időben fusson naponta, havonta vagy évente. Ha többet szeretne megtudni a feladatok végrehajtási dátumának és idejének megadásáról, tekintse át a [cron-kifejezéseket](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windows"></a>[Windows](#tab/windows)

Ha ütemezett feladatot szeretne létrehozni Windows rendszeren, írja be a következő parancsot a parancssorba vagy a PowerShellbe:

Ez a példa feltételezi, hogy a parancsfájl a számítógép legfelső szintű meghajtóján található, de a szkript bárhol lehet.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

A parancs a következő paramétereket használja:
- Az `/SC` paramétert a percenkénti ütemezés megadásához.
- Az `/MO` paramétert öt perces intervallumok meghatározásához.
- A `/TN` paramétert a feladat nevének megadásához.
- A `/TR` paramétert a `script.bat` fájl elérési útjának megadásához.

További információk az ütemezett feladatok létrehozásáról Windows rendszeren: [Schtasks](/previous-versions/orphan-topics/ws.10/cc772785(v=ws.10)#BKMK_minutes).

---

Ha ellenőrizni szeretné, hogy az ütemezett/cron feladat megfelelően fut-e, hozzon létre új fájlokat a `myFolder` könyvtárban. Várjon öt percet, és győződjön meg arról, hogy az új fájlok feltöltődtek a tárfiókjába. Navigáljon a naplókönyvtárhoz, és tekintse meg az ütemezett vagy a cron feladat kimeneti naplóit.

## <a name="next-steps"></a>Következő lépések

A helyszíni adatok Azure Storage-ba, vagy a Storage-adatok helyszíni tárolóba történő áthelyezésével kapcsolatos további információkért kövesse az alábbi hivatkozást:

* [Adatok áthelyezése az Azure Storage-ba és az-ból](./storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

A AzCopy kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

* [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](./storage-use-azcopy-v10.md#transfer-data)

* [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)

* [Adatok átvitele a AzCopy és az Amazon S3 gyűjtővel](storage-use-azcopy-s3.md)
 
* [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)
