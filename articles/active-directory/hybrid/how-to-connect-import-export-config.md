---
title: Azure AD Connect konfigurációs beállításainak importálása és exportálása
description: Ez a cikk a Felhőbeli üzembe helyezéssel kapcsolatos gyakori kérdéseket ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d67460c654c854c5a855560dde1d67732fa818c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681955"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings"></a>Azure AD Connect konfigurációs beállításainak importálása és exportálása 

Az Azure Active Directory (Azure AD) összekapcsolási központi telepítések az egyetlen erdős Expressz módú telepítéstől a több erdőre kiterjedő, egyéni szinkronizálási szabályok használatával végzett összetett központi telepítésekre változnak. A számos konfigurációs beállítás és mechanizmus miatt elengedhetetlen, hogy megtudja, milyen beállítások vannak érvényben, és hogyan lehet gyorsan üzembe helyezni egy kiszolgálót azonos konfigurációval. Ez a funkció bevezeti az adott szinkronizációs kiszolgáló konfigurációjának katalogizálása és a beállítások új központi telepítésbe való importálásának lehetőségét. A szinkronizálási beállítások különböző pillanatképeit összehasonlítva egyszerűen megjelenítheti a két kiszolgáló közötti különbségeket, illetve az idő múlásával megegyező kiszolgálót.

Minden alkalommal, amikor a konfiguráció megváltozik a Azure AD Connect varázslóból, a rendszer automatikusan exportál egy új, időbélyegzővel ellátott JSON-beállítási fájlt a **%ProgramData%\AADConnect**. A beállítások fájl neve az **alkalmazott-SynchronizationPolicy-*. JSON**, ahol a fájlnév utolsó része egy időbélyegző.

> [!IMPORTANT]
> A rendszer csak a Azure AD Connect által végzett módosításokat exportálja automatikusan. A PowerShell, a Synchronization Service Manager vagy a szinkronizálási szabályok szerkesztője által végrehajtott módosításokat szükség szerint exportálni kell igény szerint a naprakész másolás fenntartásához. Az igény szerinti exportálással egy biztonságos helyen helyezheti el a beállítások másolatát a vész-helyreállítási célokra.

## <a name="export-azure-ad-connect-settings"></a>Azure AD Connect Beállítások exportálása 

A konfigurációs beállítások összegzésének megtekintéséhez nyissa meg az Azure AD Connect eszközt, és válassza ki a további megnevezett feladatot, **vagy exportálja a jelenlegi konfigurációt**. A beállítások gyors összefoglalása megjelenik a kiszolgáló teljes konfigurációjának exportálási lehetőségével együtt.

Alapértelmezés szerint a beállítások a **%ProgramData%\AADConnect**-be lesznek exportálva. Azt is megteheti, hogy a beállításokat egy védett helyre menti, hogy vészhelyzet esetén is biztosítható legyen a rendelkezésre állás. A beállításokat a rendszer a JSON-fájlformátum használatával exportálja, és nem lehet kézzel létrehozni vagy szerkeszteni a logikai konzisztencia biztosítása érdekében. A kézzel létrehozott vagy szerkesztett fájlok importálása nem támogatott, és váratlan eredményekhez vezethet.

## <a name="import-azure-ad-connect-settings"></a>Azure AD Connect beállítások importálása

Előzőleg exportált beállítások importálása:
 
1. Telepítse a **Azure ad Connectt** egy új kiszolgálóra.
1. Válassza a **Testreszabás** lehetőséget az **üdvözlő** oldal után.
1. Válassza a **szinkronizálási beállítások importálása** lehetőséget. Tallózással keresse meg a korábban exportált JSON-beállításokat tartalmazó fájlt.
1. Válassza a **Telepítés** gombot.

   ![A szükséges összetevők telepítése képernyőt megjelenítő képernyőkép](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> Felülbírálhatja a beállításokat ezen a lapon, például SQL Server használatát LocalDB helyett, vagy egy meglévő szolgáltatásfiók használatát egy alapértelmezett VSA helyett. Ezeket a beállításokat a rendszer nem importálja a konfigurációs beállítások fájljából. Információ-és összehasonlítási célokat szolgálnak.

### <a name="import-installation-experience"></a>Telepítési élmény importálása 

Az importálás telepítési élményét szándékosan egyszerűvé kell tennie a felhasználó minimális bemenetével, így könnyen biztosítható a meglévő kiszolgálók reprodukálhatósága.

Itt láthatók a telepítési élményben elvégezhető változások. Az összes többi módosítás a Azure AD Connect varázslóból történő telepítés után is elvégezhető:
- **Azure Active Directory hitelesítő adatok**: alapértelmezés szerint a rendszer az eredeti kiszolgáló konfigurálására használt Azure globális rendszergazda fiókjának nevét javasolja. Meg *kell*   változtatni, ha egy új könyvtárba szeretné szinkronizálni az adatokat.
- **Felhasználói bejelentkezés**: az eredeti kiszolgálóhoz konfigurált bejelentkezési beállítások alapértelmezés szerint ki vannak választva, és automatikusan rákérdeznek a hitelesítő adatokra, illetve a konfigurálás során szükséges egyéb információkra. Ritka esetekben előfordulhat, hogy egy olyan kiszolgálót kell beállítania, amely különböző beállításokkal rendelkezik, hogy elkerülje az aktív kiszolgáló viselkedésének módosítását. Ellenkező esetben válassza a **tovább** lehetőséget, hogy ugyanazokat a beállításokat használja.
- Helyszíni **címtárbeli hitelesítő adatok**: a szinkronizálási beállításokban található minden helyszíni címtárhoz meg kell adnia a hitelesítő adatokat a szinkronizálási fiók létrehozásához, vagy egy előre létrehozott egyéni szinkronizálási fiókot kell megadnia. Ez az eljárás megegyezik a tiszta telepítési felülettel, azzal a kivétellel, hogy nem adhat hozzá vagy távolíthat el címtárakat.
- **Konfigurációs beállítások**: a tiszta telepítéshez hasonlóan dönthet úgy, hogy a kezdeti beállításokat konfigurálja az automatikus szinkronizálás indításához vagy az átmeneti üzemmód engedélyezéséhez. A fő különbség az, hogy az átmeneti üzemmód szándékosan engedélyezve van a konfiguráció és a szinkronizálás eredményének összehasonlításához, mielőtt aktívan exportálja az eredményeket az Azure-ba.

![A címtárak összekapcsolására szolgáló képernyőt bemutató képernyőkép](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> Csak egy szinkronizációs kiszolgáló lehet az elsődleges szerepkörben, és aktívan exportálhatja a konfigurációs módosításokat az Azure-ba. Az összes többi kiszolgálót átmeneti módba kell helyezni.

## <a name="migrate-settings-from-an-existing-server"></a>Beállítások áttelepíthetők egy meglévő kiszolgálóról 

Ha egy meglévő kiszolgáló nem támogatja a beállítások kezelését, dönthet úgy, hogy helyben frissíti a kiszolgálót, vagy áttelepíti a beállításokat egy új átmeneti kiszolgálón való használatra.

Az áttelepítéshez olyan PowerShell-parancsfájlt kell futtatni, amely kibontja a meglévő beállításokat egy új telepítésben való használathoz.Ezzel a módszerrel katalogizálhatja a meglévő kiszolgáló beállításait, majd alkalmazhatja őket egy újonnan telepített átmeneti kiszolgálóra.Az eredeti kiszolgáló beállításainak az újonnan létrehozott kiszolgálókhoz való összehasonlítása gyorsan megjeleníti a kiszolgálók közötti változásokat.Ahogy mindig, kövesse a szervezete minősítési folyamatát, és győződjön meg arról, hogy nincs szükség további konfigurálásra.

### <a name="migration-process"></a>Migrálási folyamat 
A beállítások áttelepíthetők:

1. Indítsa el **AzureADConnect.msi** az új átmeneti kiszolgálón, és állítsa le Azure ad Connect **üdvözlőlapján** .

1. Másolja **MigrateSettings.ps1** a Microsoft Azure ad Connect\Tools könyvtárból a meglévő kiszolgáló egy helyére. Ilyen például a C:\setup, ahol a telepítő egy olyan könyvtár, amely a meglévő kiszolgálón lett létrehozva.

   ![Azure AD Connect-címtárakat bemutató képernyőkép.](media/how-to-connect-import-export-config/migrate1.png)

1. Futtassa a parancsfájlt az itt látható módon, és mentse a teljes alsó szintű kiszolgáló konfigurációs könyvtárat. Másolja ezt a könyvtárat az új átmeneti kiszolgálóra. A teljes **exportált-ServerConfiguration-*** mappát át kell másolnia az új kiszolgálóra.

   ![Képernyőkép, amely a Windows PowerShellben lévő parancsfájlt jeleníti meg. ](media/how-to-connect-import-export-config/migrate2.png)
    ![ Az exportált-ServerConfiguration-* mappa másolását bemutató képernyőkép.](media/how-to-connect-import-export-config/migrate3.png)

1. **Azure ad Connect** indításához kattintson duplán a ikonra az asztalon. Fogadja el a Microsoft szoftverlicenc-szerződését, és a következő lapon válassza a **Testreszabás** lehetőséget.
1. Jelölje be a **szinkronizálási beállítások importálása** jelölőnégyzetet. Válassza a **Tallózás** lehetőséget a másolt exportált ServerConfiguration-* mappa tallózásához. Az áttelepített beállítások importálásához válassza ki a MigratedPolicy.js.

   ![A szinkronizálási beállítások importálása lehetőséget megjelenítő képernyőkép.](media/how-to-connect-import-export-config/migrate4.png)

## <a name="post-installation-verification"></a>Telepítés utáni ellenőrzés 

Az eredetileg importált beállítások fájljának az újonnan telepített kiszolgáló exportált beállítási fájljával való összehasonlítása elengedhetetlen lépés a tervezett és a létrejövő üzemelő példány közötti különbségek megismeréséhez. A kedvenc párhuzamos szöveges összehasonlító alkalmazása egy azonnali vizualizációt eredményez, amely gyorsan kiemeli a kívánt vagy véletlen változásokat.

A korábban manuálisan beállított manuális konfigurációs lépések azonban már nem szükségesek, ezért a szervezet minősítési folyamatát továbbra is biztosítani kell, hogy ne legyen szükség további konfigurálásra. Ez a konfiguráció akkor fordulhat elő, ha speciális beállításokat használ, amelyek jelenleg nem rögzítettek a beállítások kezelésének ezen kiadásában.

Ismert korlátozások:
- **Szinkronizálási szabályok**: az egyéni szabályok elsőbbségének a 0 és 99 közötti fenntartott tartományba kell esnie, hogy elkerülje a Microsoft szokásos szabályainak ütközését. Ha az egyéni szabályt a fenntartott tartományon kívülre helyezi, az egyéni szabályt a rendszer úgy is elmozdulhat, hogy az általános szabályok bekerülnek a konfigurációba. Hasonló probléma lép fel, ha a konfiguráció módosított szabványos szabályokat tartalmaz. A standard szabályok módosítása nem ajánlott, és a szabály elhelyezése valószínűleg helytelen.
- **Eszköz visszaírási**: ezek a beállítások katalogizálva vannak. A konfiguráció során jelenleg nincsenek alkalmazva. Ha az eszköz visszaírási engedélyezve lett az eredeti kiszolgálón, akkor manuálisan kell konfigurálnia a szolgáltatást az újonnan telepített kiszolgálón.
- **Szinkronizált objektumtípusok**: Bár lehetséges a szinkronizált objektumtípusok (például felhasználók, névjegyek és csoportok) listájának korlátozása a synchronization Service Manager használatával, ez a funkció jelenleg nem támogatott a szinkronizálási beállításokon keresztül. A telepítés befejezése után manuálisan kell újraalkalmaznia a speciális konfigurációt.
- **Egyéni futtatási profilok**: Bár lehetséges a futtatási profilok alapértelmezett készletének módosítása a synchronization Service Manager használatával, ez a funkció jelenleg nem támogatott a szinkronizálási beállításokon keresztül. A telepítés befejezése után manuálisan kell újraalkalmaznia a speciális konfigurációt.
- **A kiépítési hierarchia konfigurálása**: a synchronization Service Manager ezen speciális funkciója nem támogatott a szinkronizálási beállításokon keresztül. A kezdeti telepítés befejezése után manuálisan kell újrakonfigurálni.
- **Active Directory összevonási szolgáltatások (AD FS) (AD FS) és PingFederate hitelesítés**: a hitelesítési funkciókhoz társított bejelentkezési metódusok automatikusan előre ki vannak választva. Az összes többi szükséges konfigurációs paramétert interaktív módon kell megadnia.
- **A letiltott egyéni szinkronizálási szabályok engedélyezve lesznek importálva**: a rendszer a letiltott egyéni szinkronizálási szabályt engedélyezve állapotba importálja. Győződjön meg arról, hogy az új kiszolgálón is le van tiltva.

 ## <a name="next-steps"></a>Következő lépések

- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Testreszabott beállítások](how-to-connect-install-custom.md)
- [Az Azure AD Connect Health-ügynökök telepítése](how-to-connect-health-agent-install.md) 
