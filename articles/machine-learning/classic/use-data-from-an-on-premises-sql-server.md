---
title: 'ML Studio (klasszikus): helyszíni SQL Server – Azure'
description: SQL Server-adatbázisból származó adatokkal speciális elemzési műveleteket hajthat végre Azure Machine Learning Studio (klasszikus) használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 8cdf1029371e0e11c38616e7800652ca9debbba7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100517399"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-a-sql-server-database"></a>Elemzések végrehajtása Azure Machine Learning Studio (klasszikus) SQL Server-adatbázis használatával

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


A helyszíni adatokkal dolgozó vállalatok gyakran szeretnék kihasználni a felhő méretezését és rugalmasságát a gépi tanulási munkaterhelésekhez. Azonban nem szeretnék megszakítani a jelenlegi üzleti folyamatokat és munkafolyamatokat azáltal, hogy áthelyezik a helyszíni és a felhőbe. Azure Machine Learning Studio (klasszikus) mostantól támogatja az adatok SQL Server adatbázisból való olvasását, majd az adatokkal rendelkező modellek betanítását és pontozását. Már nem kell manuálisan átmásolnia és szinkronizálnia az adatait a felhő és a helyszíni kiszolgáló között. Ehelyett a Azure Machine Learning Studio (klasszikus) **adatimportálási** modulja most már közvetlenül a SQL Server-adatbázisból is olvasható a betanítási és pontozási feladatokhoz.

Ez a cikk áttekintést nyújt arról, hogyan lehet bejutni SQL Serverba az adatAzure Machine Learning Studioba (klasszikus). Feltételezi, hogy már ismeri a Studio (klasszikus) fogalmakat, például a munkaterületeket, modulokat, adatkészleteket, kísérleteket *stb*.

> [!NOTE]
> Ez a funkció ingyenes munkaterületek esetén nem érhető el. További információ a Machine Learning díjszabásáról és szintjeiről: [Azure Machine learning díjszabása](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>A Data Factory saját üzemeltetésű Integration Runtime telepítése
Azure Machine Learning Studio (klasszikus) SQL Server-adatbázis eléréséhez le kell töltenie és telepítenie kell a Data Factory saját üzemeltetésű Integration Runtime, korábbi nevén adatkezelés átjáróként. Ha a Machine Learning Studio (klasszikus) szolgáltatásban konfigurálja a kapcsolatot, lehetősége van a Integration Runtime (IR) letöltésére és telepítésére az alább ismertetett **letöltés és regisztrálás adatátjáró** használatával.


Az IR-t az idő előtt is telepítheti, ha letölti és futtatja az MSI-telepítőcsomagot a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717). Az MSI-t a meglévő IR legújabb verzióra történő frissítésére is használhatja, minden beállítás megőrzött.

A Data Factory Self-Hosted Integration Runtime a következő előfeltételekkel rendelkezik:

* A Data Factory Self-Hosted integrációhoz a .NET-keretrendszer 4.6.1-es vagy újabb verziójának megfelelő 64 bites operációs rendszer szükséges.
* A Windows operációs rendszer támogatott verziói a Windows 10, a Windows Server 2012, a Windows Server 2012 R2, a Windows Server 2016. 
* Az IR-gép ajánlott konfigurációja legalább 2 GHz-es, 4 magos PROCESSZORral, 8 GB RAM-mal és 80 GB-os lemezzel rendelkezik.
* Ha a gazdaszámítógép hibernált állapotba kerül, az IR nem válaszol az adatkérésekre. Ezért konfigurálja a megfelelő energiasémát a számítógépen az IR telepítése előtt. Ha a gép hibernált állapotra van állítva, az IR-telepítés üzenet jelenik meg.
* Mivel a másolási tevékenység meghatározott gyakorisággal történik, az erőforrás-használat (CPU, memória) a gépen is ugyanazt a mintát követi, mint a csúcs és az üresjárati idő. Az erőforrás-használat az áthelyezett adatok mennyiségétől függ. Ha folyamatban van több másolási feladat, megfigyelheti az erőforrás-használatot csúcsidőben. Habár a fent felsorolt minimális konfiguráció technikailag elegendő, érdemes lehet olyan konfigurációt használni, amely több erőforrással rendelkezik, mint a minimális konfiguráció az adatáthelyezéshez megadott terheléstől függően.

Data Factory saját üzemeltetésű Integration Runtime beállításakor és használatakor vegye figyelembe a következőket:

* Egyetlen számítógépre csak egyetlen IR-példányt telepíthet.
* Több helyszíni adatforráshoz is használhat egyetlen IR-t.
* A különböző számítógépeken több IRs is csatlakoztatható ugyanazon helyszíni adatforráshoz.
* Egyszerre csak egy munkaterülethez konfigurálhat egy IRs-t. Jelenleg az IRs nem osztható meg a munkaterületek között.
* Egyetlen munkaterülethez több IRs is konfigurálható. Előfordulhat például, hogy egy olyan IR-t szeretne használni, amely a tesztelési adatforrásokhoz van csatlakoztatva a fejlesztés során, valamint egy éles IR-t, amikor készen áll a működővé tenni.
* Az IR-nek nem kell ugyanazon a gépen lennie, mint az adatforrásnak. Az adatforráshoz közelebb maradva azonban kevesebb időt biztosít az átjárónak az adatforráshoz való kapcsolódásra. Azt javasoljuk, hogy az IR-t olyan gépre telepítse, amely eltér a helyszíni adatforrást üzemeltető gépen, így az átjáró és az adatforrás nem versenyez az erőforrásokkal.
* Ha a számítógépen már telepítve van egy Power BI vagy Azure Data Factory forgatókönyveket kiszolgáló IR, telepítsen egy különálló IR-t a Azure Machine Learning Studio (klasszikus) számára egy másik számítógépen.

  > [!NOTE]
  > Nem futtathat Data Factory saját üzemeltetésű Integration Runtime és Power BI Gateway ugyanazon a számítógépen.
  >
  >
* A Azure Machine Learning Studio (klasszikus) Data Factory saját üzemeltetésű Integration Runtimet kell használnia, még akkor is, ha más-más adataihoz is használ Azure ExpressRoute. Az adatforrást helyszíni adatforrásként (tűzfal mögött) kell kezelnie, még akkor is, ha a ExpressRoute-t használja. A Machine Learning és az adatforrás közötti kapcsolat létesítéséhez használja a Data Factory saját üzemeltetésű Integration Runtime.

A telepítési előfeltételekről, a telepítési lépésekről és a hibaelhárítási tippekről a [Data Factory Integration Runtime](../../data-factory/concepts-integration-runtime.md)cikkben talál részletes információt.

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Adatok beáramlása a SQL Server-adatbázisból a Azure Machine Learningba
Ebben az útmutatóban egy Azure Data Factory Integration Runtime kell beállítania egy Azure Machine Learning munkaterületen, konfigurálnia kell, majd el kell olvasnia egy SQL Server-adatbázis adatait.

> [!TIP]
> Mielőtt elkezdené, tiltsa le a böngésző előugró ablak-blokkoló eszközét `studio.azureml.net` . Ha a Google Chrome böngészőt használja, töltse le és telepítse a Google Chrome webáruházban elérhető több beépülő modul egyikét, [majd kattintson az alkalmazás kiterjesztésére](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Azure Data Factory saját üzemeltetésű Integration Runtime korábbi nevén adatkezelés átjáró. Az útmutató lépésről lépésre oktatóanyag továbbra is átjáróként hivatkozik rá.  

### <a name="step-1-create-a-gateway"></a>1. lépés: átjáró létrehozása
Első lépésként létre kell hoznia és be kell állítania az átjárót az SQL-adatbázis eléréséhez.

1. Jelentkezzen be [Azure Machine learning Studio (klasszikus)](https://studio.azureml.net/Home/) webhelyre, és válassza ki azt a munkaterületet, amelyen dolgozni szeretne.
2. Kattintson a bal oldali **Beállítások** panelre, majd kattintson a felül található **adatátjárók** fülre.
3. Kattintson a képernyő alján található **új adatátjáró** elemre.

    ![Új adatátjáró](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. Az **új adatátjáró** párbeszédpanelen adja meg az **átjáró nevét** , és szükség esetén adjon meg egy **leírást**. Kattintson a jobb alsó sarokban található nyílra a konfiguráció következő lépéseként.

    ![Adja meg az átjáró nevét és leírását](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. Az adatátjáró letöltése és regisztrálása párbeszédpanelen másolja az ÁTJÁRÓ regisztrációs KULCSát a vágólapra.

    ![Az adatátjáró letöltése és regisztrálása](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Ha még nem töltötte le és nem telepítette a Microsoft adatkezelés-átjárót, kattintson az **adatkezelési átjáró letöltése** elemre. Ezzel elvégezheti a Microsoft letöltőközpontból, ahol kiválaszthatja a szükséges átjáró verzióját, letöltheti és telepítheti. A telepítési előfeltételekről, a telepítési lépésekről és a hibaelhárítási tippekről részletes információkat talál az [adatok áthelyezése a helyszíni források és a felhő között adatkezelés átjáróval](../../data-factory/tutorial-hybrid-copy-portal.md)című cikkben.
7. Az átjáró telepítése után a adatkezelés átjáró Configuration Manager megnyílik, és megjelenik az **átjáró regisztrálása** párbeszédpanel. Illessze be a vágólapra másolt **átjáró-regisztrációs kulcsot** , majd kattintson a **regisztráció** elemre.
8. Ha már telepített egy átjárót, futtassa a adatkezelés átjáró Configuration Manager. Kattintson a **kulcs módosítása** gombra, illessze be az előző lépésben vágólapra másolt **átjáró regisztrációs kulcsot** , majd kattintson **az OK** gombra.
9. Ha a telepítés befejeződött, megjelenik a Microsoft adatkezelés Gateway-Configuration Manager-átjárójának **regisztrálása** párbeszédpanel. Illessze be az ÁTJÁRÓ regisztrációs KULCSát, amelyet az előző lépésben a vágólapra másolt, majd kattintson a **regisztráció** elemre.

    ![Átjáró regisztrálása](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Az átjáró konfigurációja akkor fejeződik be, ha a Microsoft adatkezelés Gateway Configuration Manager **Kezdőlap** lapján a következő értékek vannak beállítva:

    * Az **átjáró** neve és a **példány neve** az átjáró nevére van beállítva.
    * A **regisztráció** a **regisztrált** értékre van állítva.
    * Az **állapot** az **elindítva** értékre van állítva.
    * A Lenti állapotsor megjeleníti a **adatkezelés Gateway Cloud Service-hez csatlakoztatott átjárót** , zöld pipa mellett.

      ![adatkezelés Gateway Manager](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Ha a regisztráció sikeres, a Azure Machine Learning Studio (klasszikus) is frissül.

    ![Az átjáró regisztrálása sikerült](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. Az **adatátjáró letöltése és regisztrálása** párbeszédpanelen kattintson a pipa jelre a beállítás befejezéséhez. A **Beállítások** lap az átjáró állapotát "online" állapotként jeleníti meg. A jobb oldali ablaktáblában megtalálhatja az állapotot és egyéb hasznos információkat.

    ![Átjáró beállításai](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. A Microsoft adatkezelés átjáró Configuration Manager váltson a **tanúsítvány** lapra. Az ezen a lapon megadott tanúsítvány a portálon megadott helyszíni adattároló hitelesítő adatainak titkosítására és visszafejtésére szolgál. Ez a tanúsítvány az alapértelmezett tanúsítvány. A Microsoft azt javasolja, hogy változtassa meg a saját tanúsítványát, amelyről biztonsági másolatot készít a Tanúsítványkezelő rendszerében. Kattintson a **módosítás** gombra a saját tanúsítványának használatához.

    ![Átjáró tanúsítványának módosítása](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. választható Ha engedélyezni szeretné a részletes naplózást az átjáróval kapcsolatos problémák elhárítása érdekében, a Microsoft adatkezelés átjáró Configuration Manager váltson a **diagnosztika** lapra, és jelölje be a **részletes naplózás engedélyezése hibaelhárítási célokra** beállítást. A naplózási információk a Windows Eseménynapló az **alkalmazások és szolgáltatások naplók**  - &gt; **adatkezelés átjáró** csomópontja alatt találhatók. A **diagnosztika** lapon is tesztelheti a helyszíni adatforrással létesített kapcsolatokat az átjáró használatával.

    ![Részletes naplózás engedélyezése](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Ezzel befejezte az átjáró telepítési folyamatát Azure Machine Learning Studio (klasszikus).
Most már készen áll a helyszíni adatai használatára.

Mindegyik munkaterülethez létrehozhat és beállíthat több átjárót a Studio (klasszikus) használatával. Előfordulhat például, hogy egy átjáróval szeretne csatlakozni a tesztelési adatforrásokhoz a fejlesztés során, valamint egy másik átjárót az éles adatforrásokhoz. A Azure Machine Learning Studio (klasszikus) lehetőséget biztosít több átjáró beállítására a vállalati környezettől függően. Jelenleg nem oszthat meg átjárókat a munkaterületek között, és csak egy átjárót telepíthet egyetlen számítógépre. További információkért lásd: [adatok áthelyezése a helyszíni források és a felhő között adatkezelés átjáróval](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>2. lépés: a helyszíni adatforrásból származó adatok beolvasása az átjáró használatával
Az átjáró beállítása után hozzáadhat egy **adatimportálási** modult egy olyan kísérlethez, amely beírja az adatokat a SQL Server-adatbázisból.

1. A Machine Learning Studio (klasszikus) területen válassza a **kísérletek** fület, kattintson a bal alsó sarokban található **+ új** elemre, majd válassza az **üres kísérlet** lehetőséget (vagy válassza ki az elérhető több minta kísérlet egyikét).
2. Keresse meg és húzza az **adatimportálási** modult a kísérleti vászonra.
3. Kattintson a **Save (Mentés** ) gombra a vászon alatt. Írja be a "Azure Machine Learning Studio (klasszikus) helyszíni SQL Server oktatóanyag" kifejezést a kísérlet neveként, válassza ki a munkaterületet, majd kattintson az **OK** pipa jelre.

   ![Kísérlet mentése új névvel](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Kattintson az **adatimportálási** modulra a kiválasztásához, majd a vászon jobb oldalán található **Tulajdonságok** ablaktáblán válassza a "helyszíni SQL Database" lehetőséget az **adatforrás** legördülő listájában.
5. Válassza ki a telepített és regisztrált **adatátjárót** . Beállíthat egy másik átjárót az "(új adatátjáró hozzáadása...)" lehetőség kiválasztásával.

   ![Adatátjáró kiválasztása adatmodul importálásához](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Adja meg az SQL **Database-kiszolgáló nevét** és az **adatbázis nevét**, valamint azt az SQL **Database-lekérdezést** , amelyet végre szeretne hajtani.
7. Kattintson az **értékek megadása** a **Felhasználónév és a jelszó** területen, majd adja meg az adatbázis hitelesítő adatait. A SQL Server konfigurálásának módjától függően integrált Windows-hitelesítést vagy SQL Server hitelesítést használhat.

   ![Adja meg az adatbázis hitelesítő adatait](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Az "értékek szükségesek" érték a "Values set" változót zöld pipa jelöli. Csak egyszer kell megadnia a hitelesítő adatokat, hacsak nem változik az adatbázis adatai vagy jelszava. A Azure Machine Learning Studio (klasszikus) az átjáró telepítésekor megadott tanúsítványt használja a Felhőbeli hitelesítő adatok titkosításához. Az Azure nem tárolja titkosítás nélkül a helyszíni hitelesítő adatokat.

   ![Adatmodul tulajdonságainak importálása](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. A kísérlet futtatásához kattintson a **Futtatás** gombra.

Miután a kísérlet futása befejeződött, az **adatok importálása** modul kimeneti portjára kattintva megjelenítheti az adatbázisból importált adatokat, és kiválaszthatja a **Megjelenítés** elemet.

Miután befejezte a kísérletet, üzembe helyezheti és működővé tenni a modellt. A Batch végrehajtási szolgáltatás használatával az **adatimportálási** modulban konfigurált SQL Server adatbázisból származó adatok beolvasása és pontozása történik. Habár a kérés-válasz szolgáltatást használhatja a helyszíni adatkereséshez, a Microsoft az [Excel-bővítmény](excel-add-in-for-web-services.md) használatát javasolja helyette. Jelenleg az **exportálási adataival** SQL Server adatbázisba való írás nem támogatott a kísérletekben vagy a közzétett webszolgáltatásokban.