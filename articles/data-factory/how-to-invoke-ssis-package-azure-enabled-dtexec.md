---
title: SQL Server Integration Services-(SSIS-) csomagok végrehajtása az Azure-kompatibilis dtexec segédprogrammal
description: Ismerje meg, hogyan hajthat végre SQL Server Integration Services (SSIS) csomagokat az Azure-kompatibilis dtexec segédprogrammal.
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 980e8e3c2f2c1ca1dc716df1e77caaa3fe3181aa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386169"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>SQL Server Integration Services csomagok futtatása az Azure-kompatibilis dtexec segédprogrammal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk az Azure-kompatibilis dtexec (AzureDTExec) parancssori segédprogramot ismerteti. SQL Server Integration Services (SSIS) csomagok futtatására használatos a Azure-SSIS Integration Runtime (IR) Azure Data Factory.

A hagyományos dtexec segédprogram SQL Server tartalmaz. További információ: [dtexec segédprogram](/sql/integration-services/packages/dtexec-utility). A SSIS-csomagok helyszíni futtatásához gyakran harmadik féltől származó vagy ütemező (például ActiveBatch és Control-M) is hivatkozik. 

A modern AzureDTExec segédprogram egy SQL Server Management Studio (SSMS) eszközt tartalmaz. Azt is megteheti, hogy harmadik féltől származó szervezők vagy ütemező SSIS-csomagokat futtatnak az Azure-ban. A szolgáltatás megkönnyíti a SSIS-csomagok felhőbe való áthelyezését és áttelepítését. Ha a Migrálás után továbbra is harmadik féltől származó szervezőket vagy ütemező szolgáltatásokat szeretne használni a napi műveletekben, akkor a dtexec helyett AzureDTExec hivatkozhatnak.

A AzureDTExec Data Factory folyamatokban hajtja végre a csomagokat végrehajtási SSIS-csomagként. További információ: SSIS- [csomagok futtatása Azure Data Factory tevékenységként](./how-to-invoke-ssis-package-ssis-activity.md). 

A AzureDTExec konfigurálható a SSMS-on keresztül egy olyan Azure Active Directory (Azure AD) alkalmazás használatára, amely folyamatokat hoz létre az adatelőállítóban. Azt is beállíthatja, hogy hozzáférhessen a fájlrendszerekhez, a fájlmegosztáshoz vagy Azure Fileshoz, ahol a csomagokat tárolja. A Meghívási beállításokhoz megadott értékek alapján a AzureDTExec egyedi Data Factory folyamatot hoz létre, és futtatja a SSIS-csomag végrehajtásával kapcsolatos tevékenységet. A AzureDTExec meghívása ugyanazokkal az értékekkel, amikor a beállításai megegyeznek a meglévő folyamattal.

## <a name="prerequisites"></a>Előfeltételek
A AzureDTExec használatához töltse le és telepítse a SSMS legújabb verzióját, amely 18,3-es vagy újabb verziójú. Töltse le a [webhelyről](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="configure-the-azuredtexec-utility"></a>A AzureDTExec segédprogram konfigurálása
A SSMS telepítése a helyi gépen a AzureDTExec-t is telepíti. A beállítások konfigurálásához indítsa el a SSMS a **Futtatás rendszergazdaként** beállítással. Ezután válassza   >  **az eszközök migrálása az Azure-ba** beállítást  >  **Azure-kompatibilis DTExec**.

![Az Azure-kompatibilis dtexec menü konfigurálása](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Ez a művelet egy olyan **AzureDTExecConfig** ablakot nyit meg, amelyet rendszergazdai jogosultságokkal kell megnyitnia ahhoz, hogy írni lehessen a *AzureDTExec. Settings* fájlba. Ha még nem futtatta a SSMS rendszergazdaként, megnyílik a felhasználói fiókok felügyelete (UAC) ablak. Adja meg a rendszergazdai jelszavát a jogosultságok emeléséhez.

![Az Azure-t támogató dtexec beállításainak konfigurálása](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

A **AzureDTExecConfig** ablakban adja meg a konfigurációs beállításokat a következő módon:

- **ApplicationId**: adja meg annak az Azure ad-alkalmazásnak az egyedi azonosítóját, amelyet a megfelelő engedélyekkel hoz létre a folyamatoknak az adat-előállítóban történő létrehozásához. További információ: [Azure ad-alkalmazás és egyszerű szolgáltatás létrehozása Azure Portal használatával](../active-directory/develop/howto-create-service-principal-portal.md).
- **AuthenticationKey**: adja meg az Azure ad-alkalmazáshoz tartozó hitelesítési kulcsot.
- **TenantId**: adja meg az Azure ad-bérlő egyedi azonosítóját, amely alatt az Azure ad-alkalmazás létrejött.
- **DataFactory**: adja meg annak az adatelőállítónak a nevét, amelyben a SSIS-csomag végrehajtásával kapcsolatos egyedi folyamatok jönnek létre a AzureDTExec meghívásakor megadott beállítások alapján.
- **IRName**: adja meg az adatgyárban található Azure-SSIS IR nevét, amelyen az univerzális elnevezési KONVENCIÓ (UNC) elérési útján megadott csomagok a AzureDTExec meghívásakor futni fognak.
- **PipelineNameHashStrLen**: adja meg a AzureDTExec meghívásakor megadott beállításokból generált kivonatoló karakterláncok hosszát. A karakterláncok egyedi neveket alkotnak a csomagokat futtató Data Factory-folyamatok számára a Azure-SSIS IR. Általában 32 karakter hosszúnak kell lennie.
- **ResourceGroup**: adja meg annak az Azure-erőforráscsoportnak a nevét, amelyben az adatelőállítót létrehozták.
- **SubscriptionId**: adja meg annak az Azure-előfizetésnek az egyedi azonosítóját, amelyben az adat-előállítót létrehozták.
- **LogAccessDomain**: adja meg azt a tartományi hitelesítő adatot, amely a log mappához való hozzáféréshez szükséges a naplófájlok írásakor, ami kötelező, ha a **LogPath** meg van adva, és a **naplózási szint** nem **null értékű**.
- **LogAccessPassword**: írja be a jelszó hitelesítő adatait a log mappa UNC elérési útján való eléréséhez a naplófájlok írásakor, ami akkor szükséges, ha a **LogPath** meg van adva, és a **naplózási szint** nem **null értékű**.
- **LogAccessUserName**: adja meg azt a felhasználónevet, amely a log mappához való hozzáféréshez szükséges a naplófájlok írásakor, ha a **LogPath** meg van adva, és a **naplózási szint** nem **null értékű**.
- **Naplózási szint**: adja meg a naplózás kiválasztott hatókörét a csomagok végrehajtásának előre definiált **Null**, **alapszintű**, **részletes** vagy **teljesítmény** beállításai közül a Azure-SSIS IR.
- **LogPath**: adja meg a napló mappa UNC elérési útját, amelybe a rendszer a csomag végrehajtásának naplófájljait írja a Azure-SSIS IR.
- **PackageAccessDomain**: adja meg a tartomány hitelesítő adatait a AzureDTExec meghívásakor megadott UNC elérési úton lévő csomagok eléréséhez.
- **PackageAccessPassword**: adja meg a jelszó hitelesítő adatait a AzureDTExec meghívásakor megadott UNC elérési útban lévő csomagok eléréséhez.
- **PackageAccessUserName**: adja meg a username hitelesítő adatait, amely a AzureDTExec meghívásakor megadott UNC-elérési úton éri el a csomagokat.

Ha a csomagokat és a naplófájlokat a helyszínen lévő fájlrendszerekben vagy fájlmegosztásban szeretné tárolni, csatlakoztassa a Azure-SSIS IRt a helyszíni hálózathoz csatlakoztatott virtuális hálózathoz, hogy az képes legyen beolvasni a csomagokat, és megírni a naplófájlokat. További információ: [Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz](./join-azure-ssis-integration-runtime-virtual-network.md).

Ha el szeretné kerülni, hogy a *AzureDTExec. Settings* fájlba írt bizalmas értékek egyszerű szövegként legyenek megjelenítve, Base64 kódolású karakterláncba kódoljuk őket. A AzureDTExec meghívásakor a rendszer az összes Base64 kódolású karakterláncot Visszakódolja az eredeti értékükre. Az *AzureDTExec. Settings* fájlt továbbra is biztonságossá teheti az ahhoz hozzáférő fiókok korlátozásával.

## <a name="invoke-the-azuredtexec-utility"></a>A AzureDTExec segédprogram meghívása
A AzureDTExec meghívhatja a parancssori parancssorból, és megadhatja a megfelelő értékeket a használati esethez tartozó konkrét beállításokhoz.

A segédprogram a következő helyen van telepítve: `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn` . Az elérési utat hozzáadhatja az "ELÉRÉSi út" környezeti változóhoz, amelyet a rendszer bárhonnan meghívhat.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

A AzureDTExec meghívása hasonló beállításokat kínál a dtexec meghívásához. További információ: [Dtexec segédprogram](/sql/integration-services/packages/dtexec-utility). A jelenleg támogatott lehetőségek a következők:

- **/F [Ile]**: a fájlrendszerben, a fájlmegosztást vagy a Azure Filesban tárolt csomagot tölt be. A beállítás értékeként megadhatja a csomagfájl UNC elérési útját a fájlrendszerben, a fájlmegosztást vagy a Azure Files a. dtsx kiterjesztésű fájllal. Ha a megadott UNC elérési út szóközt tartalmaz, tegye idézőjelek közé a teljes elérési utat.
- **/Conf [igFile]**: meghatározza az értékek kinyerésére szolgáló konfigurációs fájlt. Ezzel a beállítással megadhatja a csomag futásidejű konfigurációját, amely eltér a tervezési időpontban megadott értéktől. Egy XML-konfigurációs fájlban különböző beállításokat tárolhat, majd betöltheti őket a csomag végrehajtása előtt. További információ: SSIS- [csomag beállításai](/sql/integration-services/packages/package-configurations). A beállítás értékének megadásához használja az UNC elérési utat a fájlrendszerben, a fájlmegosztást vagy Azure Files a saját dtsConfig-bővítménnyel. Ha a megadott UNC elérési út szóközt tartalmaz, tegye idézőjelek közé a teljes elérési utat.
- **/Conn [csolat]**: a csomag meglévő kapcsolataihoz tartozó kapcsolatok karakterláncait határozza meg. Ezzel a beállítással beállíthatja, hogy a csomagban lévő, a tervezési időpontban megadott beállításoktól eltérő futásidejű kapcsolatok karakterláncai meglegyenek a csomag meglévő kapcsolataihoz. A beállítás értékét a következőképpen adhatja meg: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]` .
- **Készlet**: felülbírálja a paraméter, változó, tulajdonság, tároló, naplózási szolgáltató, foreach enumerálás vagy a csomagbeli kapcsolatok konfigurációját. Ez a beállítás többször is megadható. A beállítás értékét a következőképpen adhatja meg: `property_path;value` . Például `\package.variables[counter].Value;1` felülbírálja a `counter` változó értékét 1-ként. A **csomag konfigurációja** varázslóval megkeresheti, másolhatja és beillesztheti a csomagban lévő azon elemek értékét, `property_path` amelyek értékeit felül szeretné bírálni. További információ: [csomag konfigurálása varázsló](/sql/integration-services/packages/legacy-package-deployment-ssis).
- **/De [Crypt]**: a **EncryptAllWithPassword** / **EncryptSensitiveWithPassword** védelmi szintjével konfigurált csomag visszafejtési jelszavának beállítása.

> [!NOTE]
> A AzureDTExec új értékekkel való meghívásával új folyamat jön létre, kivéve a **/de [csfájl]** kapcsolót.

## <a name="next-steps"></a>Következő lépések

Miután a rendszer a AzureDTExec meghívásakor létrehozta és futtatja a SSIS-csomag végrehajtása tevékenységgel rendelkező egyedi folyamatokat, azok a Data Factory-portálon figyelhetők. Data Factory eseményindítókat is hozzárendelhet, ha Data Factory használatával szeretne összehangolni/ütemezni. További információ: SSIS- [csomagok futtatása Data Factory tevékenységként](./how-to-invoke-ssis-package-ssis-activity.md).

> [!WARNING]
> A létrehozott folyamatot a rendszer csak a AzureDTExec fogja használni. A Tulajdonságok vagy paraméterek a jövőben változhatnak, ezért ne módosítsa vagy ne használja fel azokat más célra. Előfordulhat, hogy a módosítások megszakítják a AzureDTExec. Ha ez történik, törölje a folyamatot. A AzureDTExec új folyamatot hoz létre a következő meghívásakor.