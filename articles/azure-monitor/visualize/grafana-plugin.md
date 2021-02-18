---
title: Azure-szolgáltatások és-alkalmazások figyelése a Grafana használatával
description: Átirányíthatja Azure Monitor és Application Insights az Grafana-ben megtekintheti azokat.
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: e882b9d19564dab1ef2c0abb34b5d557cb4a5821
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620475"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Az Azure-szolgáltatások figyelése a Grafana-ben
Mostantól a [Azure monitor adatforrás beépülő](https://grafana.com/plugins/grafana-azure-monitor-datasource)modullal is figyelheti az Azure-szolgáltatásokat és-alkalmazásokat a [Grafana](https://grafana.com/) . A beépülő modul a Azure Monitor által összegyűjtött alkalmazás-teljesítményadatokat gyűjti, beleértve a különböző naplókat és mérőszámokat. Ezt az Grafana irányítópulton jelenítheti meg.

A következő lépésekkel állíthatja be a Grafana-kiszolgálót, és irányítópultokat hozhat létre a metrikák és naplók számára a Azure Monitorból.

## <a name="set-up-a-grafana-server"></a>Grafana-kiszolgáló beállítása

### <a name="set-up-grafana-locally"></a>Grafana helyi beállítása
Helyi Grafana-kiszolgáló beállításához [töltse le és telepítse a Grafana-et a helyi környezetben](https://grafana.com/grafana/download). A beépülő modul Azure Monitor-integrációjának használatához telepítse a Grafana 5,3-es vagy újabb verzióját.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Grafana beállítása az Azure-ban az Azure piactéren
1. Lépjen az Azure Marketplace-re, és válassza a Grafana Labs Grafana.

2. Adja meg a neveket és a részleteket. Új erőforráscsoport létrehozása. Tartsa nyomon a virtuális gép felhasználóneve, a virtuális gép jelszava és a Grafana-kiszolgáló rendszergazdai jelszava közül kiválasztott értékeket.  

3. Válassza a virtuális gép mérete és a Storage-fiók lehetőséget.

4. Konfigurálja a hálózati konfigurációs beállításokat.

5. Tekintse meg az összegzést, és válassza a **Létrehozás** lehetőséget a használati feltételek elfogadása után.

6. Az üzembe helyezés befejezése után válassza az **Ugrás az erőforrás-csoportba** lehetőséget. Ekkor megjelenik az újonnan létrehozott erőforrások listája.

    ![Grafana erőforráscsoport-objektumok](media/grafana-plugin/grafana1.png)

    Ha a hálózati biztonsági csoportot (ebben az esetben a *grafana-NSG* ) választja, láthatja, hogy a 3000-es port a grafana-kiszolgáló elérésére szolgál.

7. Kérje le a Grafana-kiszolgáló nyilvános IP-címét – térjen vissza az erőforrások listájához, és válassza a **nyilvános IP-cím** lehetőséget.

## <a name="sign-in-to-grafana"></a>Bejelentkezés a Grafana

1. A kiszolgáló IP-címének használatával nyissa meg a bejelentkezési oldalt a következő címen *\<IP address\> : http://: 3000* vagy a *\<DNSName> \: 3000* a böngészőben. Míg a 3000 az alapértelmezett port, a telepítés során lehetséges, hogy egy másik portot jelölt ki. Ekkor megjelenik a létrehozott Grafana-kiszolgáló bejelentkezési lapja.

    ![Grafana bejelentkezési képernyő](./media/grafana-plugin/grafana-login-screen.png)

2. Jelentkezzen be a Felhasználónév *rendszergazdája* és a korábban létrehozott Grafana-kiszolgáló rendszergazdai jelszavával. Ha helyi beállítást használ, az alapértelmezett jelszó a *rendszergazda* lesz, és a rendszer kérni fogja, hogy változtassa meg az első bejelentkezéskor.

## <a name="configure-data-source-plugin"></a>Adatforrás beépülő modul konfigurálása

Miután sikeresen bejelentkezett, látnia kell, hogy a Azure Monitor adatforrás beépülő modul már szerepel.

![A Grafana Azure Monitor beépülő modult tartalmaz](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Válassza az adatforrás **hozzáadása** lehetőséget a Azure monitor adatforrás hozzáadásához és konfigurálásához.

2. Adja meg az adatforrás nevét, és válassza a legördülő listából a **Azure monitor** lehetőséget.

3. Egyszerű szolgáltatásnév létrehozása – a Grafana egy Azure Active Directory egyszerű szolgáltatást használ a Azure Monitor API-khoz való kapcsolódáshoz és az adatok gyűjtéséhez. Az Azure-erőforrásokhoz való hozzáférés kezeléséhez létre kell hoznia egy meglévő szolgáltatásnevet, vagy használnia kell azt.
    * Egy egyszerű szolgáltatásnév létrehozásához tekintse meg [ezeket az utasításokat](../../active-directory/develop/howto-create-service-principal-portal.md) . Másolja és mentse a bérlői azonosítót (könyvtár-azonosítót), az ügyfél-azonosítót (Application ID) és az ügyfél titkát (az alkalmazás kulcsának értékét).
    * A figyelni kívánt előfizetés, erőforráscsoport vagy erőforrás Azure Active Directory alkalmazáshoz való hozzárendeléséhez tekintse meg az [alkalmazás társítása szerepkörhöz](../../active-directory/develop/howto-create-service-principal-portal.md) című részt. 
    A Log Analytics API-nak szüksége van a [log Analytics olvasó szerepkörre](../../role-based-access-control/built-in-roles.md#log-analytics-reader), amely magában foglalja az olvasói szerepkör engedélyeit, és hozzáadja azt.

4. Adja meg a használni kívánt API-k kapcsolati adatait. Csatlakozhat az összeshez vagy egy részhez is. 
    * Ha mindkét metrikához és naplóhoz csatlakozik Azure Monitorban, akkor ugyanazokat a hitelesítő adatokat újra felhasználhatja **Azure monitor API-val megegyező adatok** kiválasztásával.
    * A beépülő modul konfigurálásakor megadhatja, hogy melyik Azure-felhőben szeretné figyelni a beépülő modult (Public, Azure USA Government, Azure Germany vagy Azure China).
    * Ha Application Insights használ, a Application Insights API-t és az alkalmazás-azonosítót is használhatja Application Insights-alapú metrikák összegyűjtéséhez. További információ: [az API-kulcs és az alkalmazás-azonosító beszerzése](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Egyes adatforrás-mezők neve eltér a korrelált Azure-beállításoktól:
        > * A bérlő azonosítója az Azure-címtár azonosítója
        > * Az ügyfél-azonosító a Azure Active Directory alkalmazás azonosítója
        > * Az ügyfél titkos kulcsa a Azure Active Directory alkalmazás kulcsának értéke

5. Ha Application Insights használ, a Application Insights API-t és az alkalmazás-azonosítót is használhatja Application Insights-alapú metrikák összegyűjtéséhez. További információ: [az API-kulcs és az alkalmazás-azonosító beszerzése](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Válassza a **Mentés** lehetőséget, a Grafana pedig teszteli az egyes API-k hitelesítő adatait. A következőhöz hasonló üzenetnek kell megjelennie.  
    ![Grafana-adatforrás konfigurációja jóváhagyva](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Grafana-irányítópult létrehozása

1. Nyissa meg a Grafana kezdőlapját, és válassza az **új irányítópult** lehetőséget.

2. Az új irányítópulton válassza ki a **diagramot**. Más diagram-beállítási lehetőségeket is kipróbálhat, de ez a cikk a *gráfot* használja példaként.

3. Egy üres gráf jelenik meg az irányítópulton. Kattintson a panel címére, és válassza a **Szerkesztés** lehetőséget, és adja meg a diagramban ábrázolni kívánt adatok részleteit.
    ![Grafana új gráf](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Válassza ki a konfigurált Azure Monitor adatforrást.
   * Azure Monitor metrikák összegyűjtése – válassza ki a **Azure monitor** lehetőséget a szolgáltatás legördülő menüben. Megjelenik a választók listája, ahol kiválaszthatja az ebben a diagramban figyelni kívánt erőforrásokat és mérőszámot. A metrikák egy virtuális gépről való összegyűjtéséhez használja a **Microsoft. számítás/VirtualMachines** névteret. Miután kiválasztotta a virtuális gépeket és a metrikákat, elkezdheti az adatok megtekintését az irányítópulton.
     ![Grafana Graph-konfiguráció a Azure Monitorhoz](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Azure Monitor naplózási adatok gyűjtése – válassza az **Azure log Analytics** lehetőséget a szolgáltatás legördülő menüben. Válassza ki a lekérdezni kívánt munkaterületet, és állítsa be a lekérdezés szövegét. Ide másolhatja a már meglévő naplózási lekérdezéseket, vagy újat is létrehozhat. Amikor beírja a lekérdezést, az IntelliSense megjelenik, és javaslatot tesz az automatikus kiegészítési lehetőségekre. Válassza ki a vizualizáció típusát, az **idősorozat** - **táblázatot**, majd futtassa a lekérdezést.
    
     > [!NOTE]
     >
     > A beépülő modullal megadott alapértelmezett lekérdezés két makrót használ: "$ __timeFilter () és $ __interval. 
     > Ezek a makrók lehetővé teszik, hogy a Grafana dinamikusan kiszámítsa az időtartományt és az időkeretet, amikor egy diagram egy részébe nagyítja. Eltávolíthatja ezeket a makrókat, és használhat egy szabványos időszűrőt, például a *TimeGenerated > ezelõtt (1h)*, de ez azt jelenti, hogy a gráf nem támogatja a nagyítást a szolgáltatásban.
    
     ![Grafana Graph-konfiguráció az Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. A következő egy egyszerű irányítópult két diagrammal. A bal oldalon a két virtuális gép CPU-hányada látható. A jobb oldali diagram egy Azure Storage-fiókban lévő tranzakciókat jeleníti meg, a tranzakció API-típusa szerinti bontásban.
    ![Grafana – példa két diagramra](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Nem kötelező: az egyéni metrikák figyelése ugyanabban a Grafana-kiszolgálón

A InfluxDB-t és a-t is telepítheti az egyéni és az ügynök-alapú metrikák összegyűjtésére és ábrázolására is ugyanezt a Grafana-példányt. Számos adatforrás-bővítmény használható a metrikák irányítópulton való összekapcsolásához.

Ezt úgy is felhasználhatja, hogy a Prometheus-kiszolgáló mérőszámait is tartalmazza. Használja a Prometheus adatforrás beépülő modulját a Grafana beépülő moduljának galériájában.

Az alábbiakban a InfluxDB, a Prometheus és a Docker használatával foglalkozó cikkekben talál hasznos tudnivalókat
 - [Rendszermetrikák figyelése a TICK Stacktel Ubuntu 16,04 rendszeren](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [A Docker-gazdagépek,-tárolók és-tároló szolgáltatások figyelési megoldása](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Itt látható egy olyan teljes Grafana-irányítópult képe, amely Azure Monitor és Application Insights metrikákkal rendelkezik.
![Grafana – példa metrikák](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Speciális Grafana funkciók

### <a name="variables"></a>Változók
Egyes lekérdezési értékek kiválaszthatók a felhasználói felület legördülő menüjében, és frissíthetők a lekérdezésben. Példaként vegye figyelembe a következő lekérdezést:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Beállíthat egy változót, amely felsorolja az összes elérhető **megoldási** értéket, majd frissíti a lekérdezést annak használatára.
Új változó létrehozásához kattintson az irányítópult beállítások gombjára a jobb felső részen, válassza a **változók**, majd az **új** lehetőséget.
A változó lapon adja meg az adatforrás és a lekérdezés futtatását az értékek listájának lekéréséhez.
![Grafana változó konfigurálása](./media/grafana-plugin/grafana-configure-variable-dark.png)

A létrehozást követően állítsa be úgy a lekérdezést, hogy a kijelölt érték (ek) et használja, és a diagramok ennek megfelelően válaszolnak:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana változók használata](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Irányítópult-lejátszási listák létrehozása

A Grafana számos hasznos funkciója az irányítópult-lejátszási lista. Több irányítópultot is létrehozhat, és hozzáadhatja őket egy lejátszási listához az egyes irányítópultok megjelenítéséhez. Válassza a **Lejátszás** lehetőséget az irányítópultok cikluson keresztüli megjelenítéséhez. Előfordulhat, hogy egy nagyméretű fali figyelőn szeretné megjeleníteni őket, hogy a csoport számára megjelenjen a megfelelő állapotú tábla.

![Grafana lejátszási lista – példa](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a Grafana-környezetet az Azure-ban állította be, akkor a virtuális gépek akkor számítanak fel díjat, ha Ön használ vagy sem. Ha el szeretné kerülni a további költségek felmerülését, törölje a cikkben létrehozott erőforráscsoportot.

1. A Azure Portal bal oldali menüjében kattintson az **erőforráscsoportok** elemre, majd a **Grafana** elemre.
2. Az erőforráscsoport lapon kattintson a **Törlés** elemre, írja be a **Grafana** szöveget a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések
* [Azure Monitor mérőszámok áttekintése](../data-platform.md)
