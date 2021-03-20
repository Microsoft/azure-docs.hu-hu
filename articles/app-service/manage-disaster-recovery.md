---
title: Helyreállítás az egész régióra kiterjedő meghibásodás miatt
description: Ismerje meg, hogyan segítheti az Azure App Service az üzletmenet-folytonossági és a vész-helyreállítási (BCDR) képességek fenntartásában. Az Azure-ban az egész régióra kiterjedő meghibásodás miatt helyreállíthatja alkalmazását.
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1388dc11254324f74efcbaa55c97cac2ccd0c026
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87073742"
---
# <a name="move-an-app-service-app-to-another-region"></a>App Service alkalmazás áthelyezése másik régióba

Ez a cikk azt ismerteti, hogyan lehet az erőforrásokat egy másik Azure-régióban újra online App Service állapotba hozni egy olyan katasztrófa során, amely a teljes Azure-régióra hatással van. Ha egy katasztrófa a teljes Azure-régiót offline állapotba hozza, az adott régióban üzemeltetett összes App Service vész-helyreállítási módba kerül. A funkciók segítségével visszaállíthatja az alkalmazást egy másik régióba, vagy helyreállíthatja az érintett alkalmazás fájljait.

App Service erőforrások régió-specifikusak, és nem helyezhetők át régiók között. Vissza kell állítania az alkalmazást egy másik régióban lévő új alkalmazásba, majd létre kell hoznia az új alkalmazás tükrözési konfigurációit vagy erőforrásait.

## <a name="prerequisites"></a>Előfeltételek

- Nincsenek. A [pillanatképből való visszaállításhoz](app-service-web-restore-snapshots.md) általában **prémium** szint szükséges, de a vész-helyreállítási módban automatikusan engedélyezve van az érintett alkalmazás, függetlenül attól, hogy az érintett alkalmazás milyen szinten található.

## <a name="prepare"></a>Előkészítés

Azonosítsa az összes olyan App Service erőforrást, amelyet a érintett alkalmazás jelenleg használ. Például:

- App Service-alkalmazások
- [App Service-csomagok](overview-hosting-plans.md)
- [Üzembe helyezési pontok](deploy-staging-slots.md)
- [Az Azure-ban vásárolt egyéni tartományok](manage-custom-dns-buy-domain.md)
- [SSL-tanúsítványok](configure-ssl-certificate.md)
- [Azure Virtual Network integráció](web-sites-integrate-with-vnet.md)
- [Hibrid kapcsolatok](app-service-hybrid-connections.md).
- [Felügyelt identitások](overview-managed-identity.md)
- [Biztonsági mentési beállítások](manage-backup.md)

Bizonyos erőforrások, például az importált tanúsítványok vagy a hibrid kapcsolatok, más Azure-szolgáltatásokkal való integrációt tartalmaznak. Az erőforrások régiók közötti áthelyezésével kapcsolatos információkért tekintse meg a megfelelő szolgáltatások dokumentációját.

## <a name="restore-app-to-a-different-region"></a>Alkalmazás visszaállítása egy másik régióba

1. Hozzon létre egy új App Service alkalmazást egy *másik* Azure-régióban, mint az érintett alkalmazás. Ez a cél alkalmazás a vész-helyreállítási forgatókönyvben.

1. A [Azure Portal](https://portal.azure.com)navigáljon az érintett alkalmazás felügyeleti lapjára. Egy meghibásodott Azure-régióban az érintett alkalmazás figyelmeztető szöveget jelenít meg. Kattintson a figyelmeztetés szövegére.

    ![Képernyőkép az érintett alkalmazás oldaláról. Megjelenik egy figyelmeztető értesítés, amely leírja a helyzetet, és hivatkozást biztosít az alkalmazás visszaállításához.](media/manage-disaster-recovery/restore-start.png)

1. A **biztonsági mentés visszaállítása** lapon konfigurálja a visszaállítási műveletet az alábbi táblázat szerint. Ha elkészült, kattintson **az OK** gombra.

   | Beállítás | Érték | Leírás |
   |-|-|-|
   | **Pillanatkép (előzetes verzió)** | Válasszon ki egy pillanatképet. | A két legutóbbi pillanatkép elérhető. |
   | **Cél visszaállítása** | **Meglévő alkalmazás** | Kattintson a lenti megjegyzésre, és **kattintson ide a visszaállítási cél alkalmazás módosításához** , és válassza ki a kívánt alkalmazást. Katasztrófa esetén a pillanatképet csak egy másik Azure-régióban lévő alkalmazásra lehet visszaállítani. |
   | **Hely konfigurációjának visszaállítása** | **Igen** | |

    ![Képernyőkép a biztonsági másolat visszaállítása lapról. Egy adott pillanatkép, az előző táblázatban szereplő beállítások és az OK gomb kiemelve.](media/manage-disaster-recovery/restore-configure.png)

3. Konfigurálja a célalkalmazás [összes más](#prepare) elemét az érintett alkalmazás tükrözéséhez és a konfiguráció ellenőrzéséhez.

4. Ha készen áll arra, hogy az egyéni tartomány a célalkalmazás felé mutasson, adja meg újra [a tartománynevet](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

## <a name="recover-app-content-only"></a>Csak az alkalmazás tartalmának helyreállítása

Ha csak az érintett alkalmazásból kívánja helyreállítani a fájlokat a visszaállítás nélkül, kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)navigáljon az érintett alkalmazás felügyeleti lapjára, és kattintson a **közzétételi profil beolvasása** elemre.

    ![Képernyőkép az érintett alkalmazás oldaláról. Egy figyelmeztető értesítés látható, de nincs kiemelve. A közzétételi profil beolvasása eleme kiemelve.](media/manage-disaster-recovery/get-publish-profile.png)

1. Nyissa meg a letöltött fájlt, és keresse meg a nevét tartalmazó közzétételi profilt `ReadOnly - FTP` . Ez a vész-helyreállítási profil. Például:

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    Három attribútum értékének másolása: 
        
    - `publishUrl`: az FTP-állomásnév
    - `userName` és `userPWD` : az FTP hitelesítő adatai

1. Használja az FTP-ügyfelet, és kapcsolódjon az érintett alkalmazás FTP-gazdagépéhez az állomásnév és a hitelesítő adatok használatával.

1. Ha csatlakoztatva van, töltse le a teljes */site/wwwroot* mappát. A következő képernyőképen látható, hogyan tölthető le a [FileZilla](https://filezilla-project.org/)-ben.

    ![Képernyőkép egy FileZilla-fájl hierarchiáról. A WWWroot mappa ki van emelve, és a helyi menü látható. Ebben a menüben a letöltés kiemelve.](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>Következő lépések
[Azure-beli alkalmazás visszaállítása pillanatképből](app-service-web-restore-snapshots.md)
