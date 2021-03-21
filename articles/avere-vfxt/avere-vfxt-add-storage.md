---
title: Avere vFXT-tároló konfigurálása – Azure
description: Ismerje meg, hogyan adhat hozzá háttérrendszer-tárolót egy fürthöz az Azure-beli avere-vFXT. Ha létrehozott egy Azure BLOB-tárolót a fürttel, az készen áll a használatra.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: c17d3c7cd2cf6fe5bca725cf94344b2d2cb50bf2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001524"
---
# <a name="configure-storage"></a>A tárolás konfigurálása

Ez a lépés a vFXT-fürt háttér-tárolási rendszerét állítja be.

> [!TIP]
> Ha létrehozott egy új Azure BLOB-tárolót a avere vFXT-fürttel együtt, a tároló már konfigurálva van, és használatra kész.

Kövesse ezeket az utasításokat, ha nem hozott létre új BLOB-tárolót a fürttel, vagy ha további hardver-vagy felhőalapú tárolási rendszert szeretne hozzáadni.

Két fő feladat létezik:

1. [Hozzon létre egy Core Filer](#create-a-core-filer)-t, amely összekapcsolja a vFXT-fürtöt egy meglévő tárolási rendszerrel vagy egy Azure Storage-fiók tárolóval.

1. [Hozzon létre egy névtér-elágazást](#create-a-junction), amely meghatározza az ügyfelek által csatlakoztatni kívánt elérési utat.

Ezek a lépések a avere Vezérlőpultot használják. A [vFXT-fürt](avere-vfxt-cluster-gui.md) használatának megismeréséhez olvassa el a következő témakört:.

## <a name="create-a-core-filer"></a>Alapszintű Filer létrehozása

A "Core Filer" egy vFXT kifejezés a háttérrendszer tárolására. A tárterület lehet egy hardveres NAS-berendezés, például a NetApp vagy a Isilon, vagy lehet egy Felhőbeli objektum tárolója. Az alapszintű Filer szolgáltatással kapcsolatos további információk a [avere-fürt beállítási útmutatójában](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers)találhatók.

Egy alapvető Filer hozzáadásához válasszon a következő két fő típus közül:

* [NAS Core Filer](#nas-core-filer) – a NAS Core Filer hozzáadását ismerteti
* [Azure Storage Cloud Core Filer](#azure-blob-storage-cloud-core-filer) – az Azure Blob Storage-tárolók Cloud Core Filer-ként való hozzáadását ismerteti

### <a name="nas-core-filer"></a>NAS Core Filer

A NAS Core Filer lehet egy helyszíni NetApp vagy Isilon készülék, vagy egy Felhőbeli NAS-végpont. A tárolási rendszernek megbízható, nagy sebességű kapcsolattal kell rendelkeznie a avere vFXT-fürthöz – például egy 1GBps ExpressRoute-kapcsolat (nem VPN) –, és meg kell adnia a fürt számára a használt NAS-exportáláshoz szükséges hozzáférést.

A következő lépésekkel adhat hozzá egy NAS Core Filer-t:

1. A avere vezérlőpultján kattintson a felül található **Beállítások** fülre.

1. Kattintson az **alapszintű Filer**  >  **Manage Core filers** elemre a bal oldalon.

1. Kattintson a **Létrehozás** lehetőségre.

   ![Képernyőkép az új alapszintű Filer hozzáadása oldalról, amely kurzort mutat a létrehozás gombon](media/avere-vfxt-add-core-filer-start.png)

1. Adja meg a szükséges információkat a varázslóban:

   * Nevezze el a Core Filer nevet.
   * Ha elérhető, adjon meg egy teljes tartománynevet (FQDN). Ellenkező esetben adjon meg egy IP-címet vagy állomásnevet, amely az alapszintű Filer megoldásra lett feloldva.
   * Válassza ki a Filer osztályt a listából. Ha nem biztos, válassza az **egyéb** lehetőséget.

     ![Képernyőkép az új alapszintű Filer hozzáadása oldal alapszintű Filer-nevével és teljes tartománynevével](media/avere-vfxt-add-core-filer.png)
  
   * Kattintson a **tovább** gombra, és válassza ki a gyorsítótárazási házirendet.
   * Kattintson a **Filer hozzáadása** elemre.
   * További részletekért tekintse meg az [új NAS Core Filer hozzáadását](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) a avere-fürt beállítási útmutatójában.

Ezután folytassa a [csomópont létrehozásával](#create-a-junction).  

### <a name="azure-blob-storage-cloud-core-filer"></a>Azure Blob Storage Cloud Core Filer

Ha az Azure Blob Storage-t a vFXT-fürt háttér-tárolójának használatára szeretné használni, akkor egy üres tárolóra van szükség, amely alapszintű Filer-ként adható hozzá.

A blob Storage fürthöz való hozzáadásához a következő feladatok szükségesek:

* Hozzon létre egy Storage-fiókot (1. lépés)
* Üres blob-tároló létrehozása (2-3-es lépések)
* Adja hozzá a Storage-elérési kulcsot Felhőbeli hitelesítő adatként a vFXT-fürthöz (4-6. lépés)
* Adja hozzá a BLOB-tárolót a vFXT-fürt alapvető Filer-ként (7-9-es lépések)
* Hozzon létre egy olyan névtér-elágazást, amelyet az ügyfelek a Core Filer csatlakoztatásához használnak ([elágazás létrehozása](#create-a-junction)a hardveres és a felhőalapú tároláshoz is)

> [!TIP]
> Ha létrehoz egy új BLOB-tárolót az Azure-fürthöz tartozó avere-vFXT létrehozásakor, a központi telepítési sablon automatikusan alapszintű Filer-ként konfigurálja a tárolót. (Ez akkor is igaz, ha a létrehozási parancsfájlt használja, amely kérésre elérhető.) Ezt követően nem kell konfigurálnia az alapvető Filer-t.
>
> A fürt létrehozási eszköze a következő konfigurációs feladatokat végzi el:
>
> * Létrehoz egy új BLOB-tárolót a megadott Storage-fiókban.
> * A tárolót alapszintű Filer-ként definiálja
> * Névtér-elágazást hoz létre a tárolóhoz.
> * Létrehoz egy Storage szolgáltatásbeli végpontot a fürt virtuális hálózatán belül

Ha a fürt létrehozása után szeretne BLOB-tárolót hozzáadni, kövesse az alábbi lépéseket.

1. Hozzon létre egy általános célú v2 Storage-fiókot a következő beállításokkal:

   * **Előfizetés** – ugyanaz, mint a vFXT-fürt
   * **Erőforráscsoport** – ugyanaz, mint a vFXT (nem kötelező)
   * **Hely** – ugyanaz, mint a vFXT-fürt
   * **Performance** -standard (a Premium Storage nem támogatott)
   * **Fiók típusa** – általános célú v2 (StorageV2)
   * **Replikáció** – helyileg redundáns tárolás (LRS)
   * **Hozzáférési szint** – gyors
   * **Biztonságos átvitel szükséges** – tiltsa le ezt a beállítást (nem alapértelmezett érték)
   * **Virtuális hálózatok** – nem kötelező

   Használhatja a Azure Portal, vagy kattintson az alábbi "üzembe helyezés az Azure-ban" gombra.

   [![a Storage-fiók létrehozásához szükséges gomb](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. A fiók létrehozása után keresse meg a Storage-fiók lapot.

   ![Új Storage-fiók Azure Portal](media/avere-vfxt-new-storage-acct.png)

1. Hozzon létre egy új BLOB-tárolót: kattintson a **tárolók** elemre az Áttekintés lapon, majd kattintson a **+ tároló** elemre. Használja a tároló nevét, és győződjön meg arról, hogy a hozzáférés **privát** értékre van állítva.

   ![Storage-Blobok oldal a + Container gombbal, és egy előugró oldalon létrehozott új tároló](media/avere-vfxt-new-blob.png)

1. Az Azure Storage-fiók kulcsának beszerzéséhez kattintson a **Beállítások** területen a **hozzáférési kulcsok** elemre. Másolja a megadott kulcsok egyikét.

   ![Azure Portal grafikus felhasználói felület a kulcs másolásához](media/avere-vfxt-copy-storage-key.png)

1. Nyissa meg a fürt avere Vezérlőpultját. Kattintson a **Beállítások** elemre, majd nyissa meg a **fürt**  >  **Felhőbeli hitelesítő adatait** a bal oldali navigációs ablaktáblán. A Felhőbeli hitelesítő adatok lapon kattintson a **hitelesítő adatok hozzáadása** lehetőségre.

   ![Kattintson a hitelesítő adatok hozzáadása gombra a Felhőbeli hitelesítő adatok konfigurálása lapon](media/avere-vfxt-new-credential-button.png)

1. Adja meg a következő információkat a Cloud Core Filer hitelesítő adatainak létrehozásához:

   | Mező | Érték |
   | --- | --- |
   | Hitelesítő adat neve | bármilyen leíró név |
   | Szolgáltatás típusa | (Azure Storage-hozzáférési kulcs kiválasztása) |
   | Bérlő | tárfiók neve |
   | Előfizetés | subscription ID |
   | Tároló-hozzáférési kulcs | Azure Storage-fiók kulcsa (az előző lépésben másolva) |

   Kattintson a **Submit (Küldés**) gombra.

   ![A Felhőbeli hitelesítő adatok űrlapja befejeződött a avere Vezérlőpultján](media/avere-vfxt-new-credential-submit.png)

1. Ezután hozza létre a Core Filer-t. A avere Vezérlőpultjának bal oldalán kattintson az **alapszintű Filer**  >   **Manage Core filers** elemre.

1. Kattintson a **Létrehozás** gombra a **Core filers beállítások kezelése** lapon.

1. Töltse ki a varázslót:

   * Válasszon ki egy **felhő** típusú Filer-típust.
   * Nevezze el az új Core Filer nevet, és kattintson a **tovább** gombra.
   * Fogadja el az alapértelmezett gyorsítótár-házirendet, és folytassa a harmadik oldallal.
   * A **szolgáltatás típusa** területen válassza az **Azure Storage** lehetőséget.
   * Válassza ki a korábban létrehozott hitelesítő adatokat.
   * A **gyűjtő tartalmának** **üresre** állítása
   * **Tanúsítvány ellenőrzésének** módosítása **Letiltva**
   * A **tömörítési mód** módosítása a **none** értékre
   * Kattintson a **Tovább** gombra.
   * A negyedik lapon adja meg a tároló nevét a **gyűjtő neve** *storage_account_name* / *container_nameként*.
   * Szükség esetén a **titkosítás típusát** állítsa **nincs** értékre.  Az Azure Storage alapértelmezés szerint titkosítva van.
   * Kattintson a **Filer hozzáadása** elemre.

   További részletekért olvassa el az [új Cloud Core Filer hozzáadását](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) a avere-fürt konfigurációs útmutatójában.

A lap frissül, vagy frissítheti a lapot az új Core Filer megjelenítéséhez.

Ezután [létre kell hoznia egy csomópontot](#create-a-junction).

## <a name="create-a-junction"></a>Elágazás létrehozása

A Junction az ügyfelek számára létrehozott elérési út. Az ügyfelek csatlakoztatják az elérési utat, és megérkeznek a kiválasztott célhelyre.

Létrehozhatja például a `/vfxt/files` NetApp Core Filer `/vol0/data` export és az `/project/resources` alkönyvtár leképezését.

A csatlakozási pontokról további információt a [avere-fürt konfigurációs útmutatójának névtér szakaszában](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html)talál.

Kövesse az alábbi lépéseket a avere Vezérlőpult felületén:

* Kattintson   >  a bal felső sarokban található VServer **névtér** elemre.
* Adja meg a névtér elérési útját a (z)/(továbbítás perjel) kezdettel, például: ``/vfxt/data`` .
* Válassza ki az alapvető Filer-t.
* Válassza ki az alapszintű Filer-exportálást.
* Kattintson a **Tovább** gombra.

  ![Képernyőkép az "új elágazás hozzáadása" oldalról a Junction, a Core Filer és az export mezőkkel kiegészítve](media/avere-vfxt-add-junction.png)

Az összekapcsolás néhány másodperc múlva megjelenik. Szükség szerint hozzon létre további csatlakozási pontokat.

Az elágazás létrehozása után az ügyfelek a névtér elérési útját használják a fájloknak a tárolási rendszerből való eléréséhez.

## <a name="next-steps"></a>Következő lépések

* [Az Avere vFXT-fürt csatlakoztatása](avere-vfxt-mount-clients.md)
* Az adatáthelyezés hatékony módjai az [új blob-tárolóba](avere-vfxt-data-ingest.md)
