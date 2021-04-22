---
title: A Microsoft Azure Data Box Heavy gyorsútmutatója | Microsoft Docs
description: Ebből a gyorsútmutatóból megtudhatja, hogyan helyezheti üzembe az Azure Data Box Heavyt az Azure Portal használatával, beleértve a bekábelezést, a konfigurálást és az adatok másolását és feltöltését az Azure-ba.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 11/04/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 8c418f7cbeb56b94b7a85b12e833301b979bff32
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871549"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Gyorsútmutató: Az Azure Data Box Heavy üzembe helyezése az Azure Portal használatával

Ez a gyorsútmutató az Azure Data Box Heavy üzembe helyezését ismerteti az Azure Portal használatával. A lépések bemutatják, hogyan csatlakoztathatja a kábelekkel, és hogyan konfigurálhatja a Data Box Heavyt, majd másolhatja rá az adatokat az Azure-ba való feltöltésükhöz. A rövid útmutató az Azure Portalon és az eszköz helyi webes felületén hajtható végre.

Részletes üzembehelyezési és nyomkövetési utasítások: [Oktatóanyag: Azure Data Box megrendelését ismertető oktatóanyagot](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Előfeltételek

Az eszköz üzembe helyezése előtt teljesítse az üzembehelyezési hely, a Data Box szolgáltatás és az eszköz alábbi konfigurációs követelményeit.

### <a name="for-installation-site"></a>Az üzembehelyezési hely tekintetében:

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Az eszköz ténylegesen átfér az adott esetben használt bejáratokon. Az eszköz méretei: szélesség: 66 cm hossz: 122 cm magasság: 71 cm.
- Ha az üzembe helyezés nem a földszinten történik, lifttel vagy rámpával férhet hozzá az eszközhöz.
- Az eszközt két személynek kell kezelnie. Az eszköz tömege körülbelül 226,8 kg. Az eszköz kerekekkel van ellátva.
- Az adatközpontban található olyan vízszintes hely, amelynek közelében van elérhető hálózati csatlakozási lehetőség, és ahol elfér az ilyen helyigényű eszköz.

### <a name="for-service"></a>A szolgáltatás esetén

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Az eszköz esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Áttekintette [a Data Box Heavy biztonsági útmutatásait](data-box-safety.md).
- Rendelkezik egy, az adatközponti hálózatra csatlakoztatott gazdagéppel. A Data Box Heavy erről a számítógépről másolja majd az adatokat. A gazdagépen [támogatott operációs rendszernek](data-box-heavy-system-requirements.md) kell futnia.
- Rendelkezik RJ-45 kábellel ellátott laptoppal a helyi felhasználó felülethez való csatlakozáshoz és az eszköz konfigurálásához. A laptop használatával konfigurálhatja az eszköz csomópontjait.
- Az adatközpont nagy sebességű hálózathoz kapcsolódik, és rendelkezik legalább 10 GbE sebességű kapcsolattal.
- Eszközcsomópontonként egy-egy 40 Gb/s-os vagy 10 Gb/s-os kábelre van szükség. A Mellanox MCX314A-BCCT hálózati adapterrel kompatibilis kábeleket válasszon:
    - A 40 Gb/s-os kábel esetében a kábel eszközoldali végének QSFP+ rendszerűnek kell lennie.
    - A 10 Gb/s-os kábel esetén szükség van egy olyan SFP+ kábelre, amely az egyik végén egy 10 Gb/s-os kapcsolóhoz, az eszközoldali végén pedig egy QSFP+–SFP+ adapterhez (vagy a QSA-adapterhez) csatlakozik.
- A tápkábelek az eszköz hátulján lévő tálcán találhatók.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="order"></a>Rendelés

### <a name="portal"></a>[Portál](#tab/azure-portal)

Ez a lépés nagyjából 5 percet vesz igénybe.

1. Hozzon létre egy új Azure Data Box-erőforrást az Azure Portalon.
2. Válasszon ki egy meglévő előfizetést a szolgáltatáshoz, és az átvitel típusánál válassza az **Importálás** lehetőséget. Adja meg a **Forrásország** mezőben azt a helyet, ahol az adatok jelenleg találhatók, az **Azure-beli célrégió** mezőben pedig az adatátvitel célját.
3. Válassza a **Data Box Heavy** lehetőséget. A maximális hasznos kapacitás 770 TB, nagyobb mennyiségű adat esetén több rendelést is létrehozhat.
4. Adja meg a rendelés részleteit és a szállítási adatokat. Ha a szolgáltatás elérhető az Ön régiójában, adja meg az értesítési e-mail-címeket, tekintse át az összefoglalót, és hozza létre a rendelést.

A rendelés létrehozását követően megtörténik az eszköz a szállításra való előkészítése.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ezekkel az Azure CLI-parancsokkal Data Box Heavy-feladatokat hozhat létre.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. Futtassa az [az group create](/cli/azure/group#az_group_create) parancsot egy erőforráscsoport létrehozásához, vagy használjon egy meglévő erőforráscsoportot:

   ```azurecli
   az group create --name databox-rg --location westus 
   ```

1. Futtassa az [az storage account create](/cli/azure/storage/account#az_storage_account_create) parancsot egy tárfiók létrehozásához, vagy használjon egy meglévő tárfiókot:

   ```azurecli
   az storage account create --resource-group databox-rg --name databoxtestsa
   ```

1. Futtassa az [az databox job create](/cli/azure/databox/job#az_databox_job_create) parancsot egy **--sku** értékű `DataBoxHeavy` paraméterrel rendelkező Data Box-feladat létrehozásához:

   ```azurecli
   az databox job create --resource-group databox-rg --name databoxheavy-job \
       --location westus --sku DataBoxHeavy --contact-name "Jim Gan" --phone 4085555555 \
       --city Sunnyvale --email-list JimGan@contoso.com --street-address1 "1020 Enterprise Way" \
       --postal-code 94089 --country US --state-or-province CA --storage-account databoxtestsa \
       --staging-storage-account databoxtestsa --resource-group-for-managed-disk rg-for-md
   ```

   > [!NOTE]
   > Győződjön meg arról, hogy az előfizetés támogatja a Data Box Heavyt.

1. Futtassa az [az databox job update](/cli/azure/databox/job#az_databox_job_update) parancsot egy feladat frissítéséhez, mint ebben a példában, amelyben módosítja a kapcsolattartó nevét és e-mail-címét:

   ```azurecli
   az databox job update -g databox-rg --name databox-job --contact-name "Robert Anic" --email-list RobertAnic@contoso.com
   ```

   Futtassa az [az databox job show](/cli/azure/databox/job#az_databox_job_show) parancsot a feladat adatainak lekéréséhez:

   ```azurecli
   az databox job show --resource-group databox-rg --name databox-job
   ```

   Futtassa az [az databox job list]( /cli/azure/databox/job#az_databox_job_list) parancsot egy erőforráscsoport összes Data Box-feladatának megtekintéséhez:

   ```azurecli
   az databox job list --resource-group databox-rg
   ```

   Futtassa az [az databox job cancel](/cli/azure/databox/job#az_databox_job_cancel) parancsot egy feladat megszakításához:

   ```azurecli
   az databox job cancel –resource-group databox-rg --name databox-job --reason "Cancel job."
   ```

   Futtassa az [az databox job delete](/cli/azure/databox/job#az_databox_job_delete) parancsot egy feladat törléséhez:

   ```azurecli
   az databox job delete –resource-group databox-rg --name databox-job
   ```

1. Futtassa az [az databox job list-credentials]( /cli/azure/databox/job#az_databox_job_list_credentials) parancsot a Data Box-feladat hitelesítő adatainak listázásához:

   ```azurecli
   az databox job list-credentials --resource-group "databox-rg" --name "databoxdisk-job"
   ```

A rendelés létrehozását követően megtörténik az eszköz a szállításra való előkészítése.

---

::: zone-end

::: zone target = "chromeless"

## <a name="cable-and-connect-to-your-device"></a>Az eszköz bekábelezése és csatlakoztatása

Miután áttekintette az előfeltételeket, elvégezheti az eszköz bekábelezését és csatlakoztatását.

::: zone-end

## <a name="cable-for-power"></a>Az eszköz tápkábeleinek bekötése

Ez a lépés nagyjából 5 percet vesz igénybe.

A Data Box Heavy megérkeztét követően hajtsa végre az alábbi lépéseket az eszköz tápkábeleinek bekötéséhez és az eszköz bekapcsolásához.

1. Amennyiben az eszközön sérülés vagy illetéktelen hozzáférés nyomai észlelhetők, ne folytassa az üzembe helyezést. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, és küldessen egy csereeszközt.
2. Vigye az eszközt az üzembehelyezési helyére, és rögzítse a hátsó kerekeket.
3. Csatlakoztassa mind a négy tápkábelt az eszköz hátulján található tápegységekhez.
4. Az eszközcsomópontok bekapcsolásához nyomja meg a bekapcsológombokat az eszköz előlapján.

## <a name="cable-first-node-for-network"></a>Az első csomópont bekötése a hálózatba

Ez a lépés nagyjából 10–15 percet vesz igénybe.

1. Az RJ-45 CAT 6 hálózati kábellel csatlakoztassa a gazdagépet az eszköz felügyeleti portjához (MGMT).
2. A Twinax QSFP+ rézkábellel csatlakoztassa legalább az egyik 40 Gb/s (előnyben részesítve az 1 Gb/s-ossal szemben) hálózati adaptert, a DATA 1 vagy a DATA 2 adaptert az adatforgalom számára. Ha 10 Gb/s-os kapcsolót használ, használjon Twinax SFP+ kábelt QSFP+–SFP+ adapterrel (QSA-adapterrel) a 40 Gb/s-os hálózati adapter csatlakoztatásához az adatforgalom számára.
3. Csatlakoztassa a kábeleket az eszközhöz az ábra szerint.  

    ![Data Box Heavy kábelekkel](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Az első csomópont konfigurálása

Ez a lépés kb. 5–7 percet vesz igénybe.

1. Az eszköz jelszavát az [Azure Portal](https://portal.azure.com)**Általános > Eszköz adatai** lapján találja. Az eszköz mindkét csomópontjához ugyanazt a jelszót használja.
2. A Data Box Heavyhez csatlakoztatott számítógépen rendelje a 192.168.100.5 statikus IP-címet és a 255.255.255.0 alhálózatot az Ethernet-adapterhez. Nyissa meg az eszköz helyi webes felületét a következő helyen: `https://192.168.100.10`. A csatlakozás az eszköz bekapcsolását követően 5 percet is igénybe vehet.
3. Jelentkezzen be az Azure Portalról beszerzett jelszóval. Egy hibaüzenet jelenik meg, miszerint a webhely biztonsági tanúsítványa hibás. A böngészőspecifikus utasításokat követve lépjen a weblapra.
4. Alapértelmezés szerint az adapterek hálózati beállításai (az MGMT kivételével) DHCP-ként vannak konfigurálva. Szükség esetén statikusként is konfigurálhatja az adaptereket, és megadhat egy IP-címet.

## <a name="cable-and-configure-the-second-node"></a>A második csomópont bekábelezése és konfigurálása

Ez a lépés nagyjából 15–20 percet vesz igénybe.

Az eszköz második csomópontjának bekábelezéséhez és konfigurálásához kövesse az első csomópont esetében használt lépéseket.  


::: zone target = "docs"

## <a name="copy-data"></a>Adatok másolása

A művelet végrehajtásának időtartama az adatok méretétől és az adatok másolásához használt hálózat sebességétől függ.
 
1. Másolja át az adatokat mindkét eszközcsomópontra a két 40 Gb/s sebességű adapter egyidejű használatával.

    - Windows rendszerű gazdagép használata esetén használjon SMB-kompatibilis fájlmásoló eszközt, amilyen például a [Robocopy](/previous-versions/technet-magazine/ee851678(v=msdn.10)).
    - NFS-gazdagép esetén a `cp` parancs vagy a `rsync` használatával másolhatja az adatokat.
2. Az eszközön található megosztásokhoz a következő útvonalon csatlakozhat: `\\<IP address of your device>\ShareName`. A megosztás eléréséhez szükséges hitelesítő adatokat a Data Box Heavy helyi webes felhasználói felületének **Csatlakozás és másolás** lapján tekintheti meg.
3. Gondoskodjon róla, hogy a megosztások és a mappák neve, valamint az adatok megfeleljenek [az Azure Storage és a Data Box Heavy szolgáltatás korlátaival](data-box-heavy-limits.md) foglalkozó cikkben foglaltaknak.

## <a name="prepare-to-ship"></a>A szállítás előkészítése

A művelet végrehajtásának időtartama az adatok mennyiségétől függ.

1. Az adatmásolás hibák nélküli befejeződése után nyissa meg a helyi webes felhasználói felület **Előkészítés szállításra** lapját, és kezdje meg az előkészületeket a szállításra.
2. Ha az **Előkészítés szállításra** sikeresen befejeződött mindkét csomóponton, kapcsolja ki az eszközt a helyi webes felhasználói felületen.

## <a name="ship-to-azure"></a>Elküldés az Azure-nak

A művelet befejezése nagyjából 15–20 percet vesz igénybe.

1. Válassza le a kábeleket, és helyezze vissza azokat az eszköz hátulján található tálcára.
2. Egyeztessen egy csomagfelvételi időpontot helyi szolgáltatójával.
3. Lépjen kapcsolatba a [Data Box üzemeltetési csapatával](mailto:DataBoxOps@microsoft.com) a csomagfelvétellel kapcsolatos információk megadása és a visszaküldési fuvarlevélcímke beszerzése érdekében.
4. A visszaküldési fuvarlevélcímkének láthatónak kell lennie az eszköz elülső átlátszó paneljén.

## <a name="verify-data"></a>Az adatok ellenőrzése

A művelet végrehajtásának időtartama az adatok mennyiségétől függ.

1. A Data Box Heavy eszköz az Azure-adatközpont hálózatához való csatlakoztatásakor az adatok az Azure-ba való feltöltése automatikus.
2. A Data Box szolgáltatás az Azure Portalon értesíti, ha az adatok másolása befejeződött.

    1. Ellenőrizze a hibákat a hibanaplókban, és tegye meg a szükséges intézkedéseket.
    2. Ellenőrizze, hogy az adatok jelen vannak-e a tárfiók(ok)ban, mielőtt törölné azokat a forrásról.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a lépés 2–3 percet vehet igénybe.

- A Data Box Heavy-rendelést a feldolgozása előtt bármikor visszavonhatja az Azure Portalon. A rendelést a teljesítése után már nem lehet visszavonni. A rendelés halad a maga útján, amíg el nem éri a teljesített állapotot. A rendelés visszavonásához lépjen az **Áttekintés** oldalra, és kattintson a **Megszakítás** parancsra a parancssávon.

- A rendelés akkor törölhető, ha a **Teljesítve** vagy a **Megszakítva** állapot jelenik meg az Azure Portalon. A rendelés törléséhez lépjen az **Áttekintés** oldalra, és kattintson a **Törlés** parancsra a parancssávon.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Data Box Heavyt helyezett üzembe az adatok az Azure-ba való importálásához. Folytassa a következő útmutatóval, ha többet szeretne megtudni az Azure Data Box Heavy kezeléséről:

> [!div class="nextstepaction"]
> [A Data Box Heavy az Azure Portal használatával történő kezelése](data-box-portal-admin.md)

::: zone-end
