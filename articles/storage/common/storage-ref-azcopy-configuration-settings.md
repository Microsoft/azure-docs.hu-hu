---
title: AzCopy v10 konfigurációs beállítás (Azure Storage) | Microsoft Docs
description: Ez a cikk az AzCopy 10-es konfigurációbeállításokkal kapcsolatos referenciainformációkat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4fcbcf145dc417d2a7f78913e70429c3929cd902
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509075"
---
# <a name="azcopy-v10-configuration-settings-azure-storage"></a>AzCopy 10-es konfigurációs beállításai (Azure Storage)

Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között. Ez a cikk azon környezeti változók listáját tartalmazza, amelyek az AzCopy 10-esének konfigurálásához használhatók.

> [!NOTE]
> Ha olyan tartalmat keres, amely segít az AzCopy első lépésekben, lásd: [Az AzCopy első](storage-use-azcopy-v10.md)lépések.

## <a name="azcopy-v10-environment-variables"></a>AzCopy 10-es környezeti változói

Az alábbi táblázat ismerteti az egyes környezeti változókat, és hivatkozásokat biztosít a változó használatát segítő tartalmakra.

| Környezeti változó | Leírás |
|--|--|
| AWS_ACCESS_KEY_ID | Amazon Web Services hozzáférési kulcs. Kulcsot biztosít a Amazon Web Services. [Adatok másolása az Amazon S3-ból az Azure Storage-ba az AzCopy használatával](storage-use-azcopy-s3.md) |
| AWS_SECRET_ACCESS_KEY | Amazon Web Services titkos hozzáférési kulcs Egy titkos kulcsot biztosít az Amazon Web Services. [Adatok másolása az Amazon S3-ból az Azure Storage-ba az AzCopy használatával](storage-use-azcopy-s3.md) |
| AZCOPY_ACTIVE_DIRECTORY_ENDPOINT | A Azure Active Directory végpontot használja. Ez a változó csak automatikus bejelentkezéshez használatos. A bejelentkezési parancshoz használja a parancssori jelölőt. |
| AZCOPY_AUTO_LOGIN_TYPE | Állítsa ezt a változót `DEVICE` , `MSI` vagy `SPN` értékre. Ez a változó lehetővé teszi az engedélyt az parancs használata `azcopy login` nélkül. Ez a mechanizmus olyan esetekben hasznos, amikor az operációs rendszer nem rendelkezik titkos tárolóval, például linuxos *kulcstartóval.* Lásd: Authorize without a secret store (Engedély [titkos tároló nélkül).](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_BUFFER_GB | Adja meg a rendszermemória maximális mennyiségét, amit az AzCopy a fájlok letöltésekor és feltöltésekor használni szeretne. Ezt az értéket gigabájtban (GB) kell kifejezni. Lásd: [Memóriahasználat optimalizálása](storage-use-azcopy-optimize.md#optimize-memory-use) |
| AZCOPY_CACHE_PROXY_LOOKUP | Alapértelmezés szerint a Windows rendszeren az AzCopy az állomásnév szintjén gyorsítótárazza a proxykiszolgálók keresését (nem veszi figyelembe az URL-útvonalat). A gyorsítótár letiltásához állítsa a értéket a "true" (igaz) érték helyett bármely más értékre. |
| AZCOPY_CONCURRENCY_VALUE | Megadja az egyidejű kérések számát. Ezzel a változóval növelheti az átviteli sebességet. Ha a számítógép kevesebb mint 5 PROCESSZORt használ, akkor ennek a változónak az értéke `32` . Egyéb esetben az alapértelmezett érték a CPU-k száma szorozva 16-tal. Ennek a változónak a maximális alapértelmezett értéke , de manuálisan is beállíthatja ezt az értéket `3000` magasabbra vagy alacsonyabbra. Lásd: [Egyidejűség növelése](storage-use-azcopy-optimize.md#increase-concurrency) |
| AZCOPY_CONCURRENT_FILES | Felülbírálja az egy időben folyamatban lévő fájlok (hozzávetőleges) számát azáltal, hogy szabályozza, hogy egyszerre hány fájlhoz kezdeményezzen átvitelt. |
| AZCOPY_CONCURRENT_SCAN | A vizsgálat során használt párhuzamosság (maximális) mértékét szabályozza. Csak a párhuzamosított enumerátorokat érinti, Azure Files a blobokat és a helyi fájlrendszereket. |
| AZCOPY_DEFAULT_SERVICE_API_VERSION | Felülbírálja a szolgáltatás API-verzióját, hogy az AzCopy be tudja fogadni az egyéni környezeteket, például Azure Stack. |
| AZCOPY_DISABLE_HIERARCHICAL_SCAN | Csak akkor érvényes, ha az Azure Blobs a forrás. Az egyidejű vizsgálat gyorsabb, de a hierarchikus listázási API-t alkalmazza, ami több I/S/költséggel jár. A "true" (igaz) érték megadásával a teljesítmény visszaeshet, azonban költségeket takaríthat meg. |
| AZCOPY_JOB_PLAN_LOCATION | Felülbírálja a feladatterv fájljait (amelyek a folyamat nyomon követéséhez és az áttöltéshez használatosak) a lemez feltöltésének elkerülése érdekében. |
| AZCOPY_LOG_LOCATION | Felülbírálja a naplófájlok tárolására való helyeket, hogy elkerülje a lemez feltöltését. |
| AZCOPY_MSI_CLIENT_ID | Felhasználó által hozzárendelt felügyelt identitás ügyfél-azonosítója. Akkor `AZCOPY_AUTO_LOGIN_TYPE` használja, ha a beállítása `MSI` . Lásd: [Authorize without a secret store (Engedély titkos tároló nélkül)](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_OBJECT_ID | A felhasználó által hozzárendelt felügyelt identitás objektumazonosítója. Akkor `AZCOPY_AUTO_LOGIN_TYPE` használja, ha a beállítása `MSI` . Lásd: [Authorize without a secret store (Engedély titkos tároló nélkül)](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_RESOURCE_STRING | A felhasználó által hozzárendelt felügyelt identitás erőforrás-azonosítója. Lásd: [Authorize without a secret store (Engedély titkos tároló nélkül)](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_PACE_PAGE_BLOBS | A lapblobok átviteli sebességét automatikusan módosítani kell a szolgáltatási korlátoknak megfelelően? Alapértelmezett érték: true (igaz). A letiltásához állítsa "false" (hamis) értékre |
| AZCOPY_PARALLEL_STAT_FILES | Az AzCopy a párhuzamos "szálakon" keres fájltulajdonságokat a helyi fájlrendszer vizsgálatakor.  A szálakat a függvény által meghatározott készletből AZCOPY_CONCURRENT_SCAN.  Ha ezt true (igaz) értékre váltja, az javíthatja a vizsgálat teljesítményét Linux rendszeren.  Windows rendszeren nem szükséges vagy ajánlott. |
| AZCOPY_SHOW_PERF_STATES | Ha a beállítás bármire van állítva, a képernyőn megjelenő kimenet az adattömbök számát fogja tartalmazni állapot szerint |
| AZCOPY_SPA_APPLICATION_ID | A szolgáltatásnév alkalmazásregisztrációjának alkalmazásazonosítója. Akkor `AZCOPY_AUTO_LOGIN_TYPE` használja, ha a beállítása `SPN` . Lásd: [Authorize without a secret store (Engedély titkos tároló nélkül)](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PASSWORD | A tanúsítvány jelszava. Akkor `AZCOPY_AUTO_LOGIN_TYPE` használja, ha a beállítása `SPN` . Lásd: [Authorize without a secret store (Engedély titkos tároló nélkül)](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PATH | A tanúsítványfájl relatív vagy teljes elérési útja. Akkor `AZCOPY_AUTO_LOGIN_TYPE` használja, ha a beállítása `SPN` . Lásd: [Authorize without a secret store (Engedély titkos tároló nélkül)](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CLIENT_SECRET | Az ügyfél titkos ügyféltitkja. Akkor `AZCOPY_AUTO_LOGIN_TYPE` használja, ha a beállítása `SPN` . Lásd: [Authorize without a secret store (Engedély titkos tároló nélkül)](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_TENANT_ID | Az Azure Active Directory OAuth-eszköz interaktív bejelentkezéséhez használt bérlőazonosító. Ez a változó csak automatikus bejelentkezéshez használatos. A bejelentkezési parancshoz használja a parancssori jelölőt. |
| AZCOPY_TUNE_TO_CPU | Állítsa false (hamis) értékre, hogy az AzCopy ne veszi figyelembe a CPU-használatot az egyidejűségi szint automatikus kiejtésekor (például a benchmark parancsban). |
| AZCOPY_USER_AGENT_PREFIX | Adjon hozzá egy előtagot az alapértelmezett AzCopy felhasználói ügynökhöz, amely telemetriai célokra szolgál. A rendszer automatikusan beszúr egy szóközt. |
| GOOGLE_APPLICATION_CREDENTIALS | A szolgáltatásfiók kulcsfájlja abszolút elérési útja Egy kulcsot biztosít a Google Cloud Storage-ban való jogosultsághoz. [Adatok másolása a Google Cloud Storage-ból az Azure Storage-ba az AzCopy (előzetes verzió) használatával](storage-ref-azcopy-configuration-settings.md) |
| HTTPS_PROXY | Konfigurálja az AzCopy proxybeállítását. Állítsa be ezt a változót a proxy IP-címére és a proxyport számára. Például: `xx.xxx.xx.xxx:xx`. Ha Windows rendszeren futtatja az AzCopyt, az AzCopy automatikusan észleli a proxybeállításokat, így Windows rendszeren nem kell használnia ezt a beállítást. Ha ezen beállítás használata mellett dönt Windows rendszeren, az felülírja az automatikus észlelést. Lásd: [Proxybeállítások konfigurálása](#configure-proxy-settings) |


## <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása

Az AzCopy proxybeállításának konfigurálásához állítsa be a `HTTPS_PROXY` környezeti változót. Ha Windows rendszeren futtatja az AzCopyt, az AzCopy automatikusan észleli a proxybeállításokat, így Windows rendszeren nem kell használnia ezt a beállítást. Ha ezen beállítás használata mellett dönt Windows rendszeren, az felülírja az automatikus észlelést.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | A parancssorban használja a következőt: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> A PowerShellben használja a következőt: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

Az AzCopy jelenleg nem támogatja az NTLM- vagy Kerberos-hitelesítést igénylő proxykat.

### <a name="bypassing-a-proxy"></a>Proxy megkerülése

Ha Windows rendszeren futtatja az AzCopyt, és azt szeretné, hogy egyáltalán ne használjon _proxyt_ (a beállítások automatikus észlelése helyett), használja ezeket a parancsokat. Ezekkel a beállításokkal az AzCopy nem keres proxyt, és nem kísérel meg proxyt használni.

| Operációs rendszer | Környezet | Parancsok  |
|--------|-----------|----------|
| **Windows** | Parancssor (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

Más operációs rendszereken egyszerűen hagyja be HTTPS_PROXY a változót, ha nem szeretne proxyt használni.

## <a name="see-also"></a>Lásd még

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Az AzCopy 10-es és Azure Storage-beli teljesítményének optimalizálása](storage-use-azcopy-optimize.md)
- [Az AzCopy 10-es hibák elhárítása az Azure Storage-ban naplófájlok használatával](storage-use-azcopy-configure.md)
