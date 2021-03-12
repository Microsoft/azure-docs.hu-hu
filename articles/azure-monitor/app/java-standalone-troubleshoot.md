---
title: A Java-Azure Monitor Application Insights hibaelhárítása
description: Ismerje meg, hogyan lehet elhárítani a Java-ügynököt a Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: f971466f25c2b7a4bd28e5b7eec6268f1b2e8b3d
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225574"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Hibaelhárítási útmutató: Azure Monitor Application Insights Javához

Ebben a cikkben néhány olyan gyakori problémát ismertetünk, amelyek a Java-alkalmazások a Application Insights Java-ügynökkel való kiszerelése során merülhetnek fel. Emellett a problémák megoldásának lépéseit is tárgyaljuk. A Application Insights a Azure Monitor platform szolgáltatás egyik funkciója.

## <a name="check-the-self-diagnostic-log-file"></a>Az öndiagnosztika naplófájljának ellenőrzéséhez

Alapértelmezés szerint a Application Insights Java 3,0-ügynöke létrehoz egy nevű naplófájlt, `applicationinsights.log` amely ugyanabban a könyvtárban található, amely a `applicationinsights-agent-3.0.2.jar` fájlt tartalmazza.

Ez a naplófájl az első hely, amely az esetlegesen felmerülő problémákra utal.

## <a name="jvm-fails-to-start"></a>A JVM nem indul el

Ha a JVM nem tudja elindítani a "hiba a zip-fájl vagy a JAR-jegyzékfájl hiányában" hibaüzenetet, próbálja meg újra letölteni az ügynök jar-fájlt, mert előfordulhat, hogy a fájlátvitel során megsérült.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Frissítés a Application Insights Java 2. x SDK-ból

Ha már használja a Application Insights Java 2. x SDK-t az alkalmazásban, továbbra is használhatja azt. A Java 3,0-ügynök felismeri. További információ: [frissítés a Java 2. x SDK-ból](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Frissítés Application Insights Java 3,0 előzetes verzióról

Ha a Java 3,0 Preview-ügynökről frissít, gondosan tekintse át az összes [konfigurációs beállítást](./java-standalone-config.md) . A JSON-struktúra teljes mértékben megváltozott az 3,0-es általánosan elérhető kiadásban.

Ezek a változások a következők:

-  A konfigurációs fájl neve módosult a következőre: `ApplicationInsights.json` `applicationinsights.json` .
-  A `instrumentationSettings` csomópont már nem létezik. A rendszer az összes tartalmat `instrumentationSettings` áthelyezi a gyökér szintjére. 
-  A (z `sampling` ),, `jmxMetrics` és rendszerű konfigurációs csomópontok `instrumentation` `heartbeat` kikerülnek a `preview` gyökér szintjére.

## <a name="some-logging-is-not-auto-collected"></a>Egyes naplózások nem lesznek automatikusan begyűjtve

A naplózás csak akkor rögzítve van, ha először megfelel a naplózási keretrendszerhez konfigurált szintnek, másrészt pedig megfelel a Application Insightshoz konfigurált szintnek is.

Ha például a naplózási keretrendszer úgy van konfigurálva, hogy a `WARN` csomagból (vagy felette) naplózza a csomagot `com.example` , és Application Insights úgy van beállítva, hogy rögzítse `INFO` (és a fentiket), akkor Application Insights csak `WARN` a csomagból (és felett) rögzíti `com.example` .

A legjobb módszer annak megállapítására, hogy egy adott naplózási utasítás megfelel-e a naplózási keretrendszerek konfigurált küszöbértékének, annak ellenőrzéséhez, hogy megjelenik-e a normál alkalmazási naplóban (például fájl vagy konzol).

Azt is vegye figyelembe, hogy ha egy kivétel objektumot ad át a naplózó, akkor a rendszer megjeleníti a napló üzenetét (és a kivétel-objektum részleteit) a tábla helyett a Azure Portal táblázatban `exceptions` `traces` .

További részletekért tekintse meg az [automatikusan összegyűjtött naplózási konfigurációt](./java-standalone-config.md#auto-collected-logging) .

## <a name="import-ssl-certificates"></a>SSL-tanúsítványok importálása

Ez a szakasz segítséget nyújt az SSL-tanúsítványokkal kapcsolatos kivételek elhárításához és kijavításához a Java-ügynök használata esetén.

A probléma megoldásához két különböző elérési út van:
* Alapértelmezett Java-tároló használata esetén
* Egyéni Java-tároló használata esetén

Ha nem tudja biztosan, hogy melyik elérési utat szeretné követni, ellenőrizze, hogy van-e JVM ARG `-Djavax.net.ssl.trustStore=...` .
Ha _nincs_ ilyen JVM ARG, akkor valószínűleg az alapértelmezett Java-tárolót használja.
_Ha egy_ ilyen JVM ARG-t használ, akkor valószínűleg egyéni tárolót használ, és a JVM ARG az egyéni tárolóra mutat.

### <a name="if-using-the-default-java-keystore"></a>Ha az alapértelmezett Java-tárolót használja:

Általában az alapértelmezett Java-tároló már rendelkezik az összes HITELESÍTÉSSZOLGÁLTATÓI főtanúsítvánnyal. Előfordulhat azonban, hogy bizonyos kivételek, például a betöltési végpont tanúsítványa más főtanúsítvánnyal is aláírható. Ezért javasoljuk, hogy a probléma megoldásához kövesse a következő három lépést:

1.  Ellenőrizze, hogy az Application Insights végpont aláírásához használt főtanúsítvány már szerepel-e az alapértelmezett tárolóban. A megbízható HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok alapértelmezés szerint a ben tárolódnak `$JAVA_HOME/jre/lib/security/cacerts` . A Java-tárolóban lévő tanúsítványok listázásához használja a következő parancsot:
    > `keytool -list -v -keystore $PATH_TO_KEYSTORE_FILE`
 
    A kimenet átirányítható egy ideiglenes fájlba, például a következőre (később könnyebben megkereshető lesz)
    > `keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts > temp.txt`

2. A tanúsítványok listája után az alábbi [lépéseket](#steps-to-download-ssl-certificate) követve töltse le az Application Insights-végpont aláírásához használt főtanúsítványt.

    Miután letöltötte a tanúsítványt, létrehoz egy SHA-1 kivonatot a tanúsítványon az alábbi parancs használatával:
    > `keytool -printcert -v -file "your_downloaded_root_certificate.cer"`
 
    Másolja az SHA-1 értéket, és ellenőrizze, hogy ez az érték szerepel-e a korábban mentett "temp.txt" fájlban.  Ha nem találja az SHA-1 értéket a temp fájlban, az azt jelzi, hogy a letöltött legfelső szintű tanúsítvány hiányzik az alapértelmezett Java-tárolóban.


3. Importálja a főtanúsítványt az alapértelmezett Java-tárolóba a következő parancs használatával:
    >   `keytool -import -file "the cert file" -alias "some meaningful name" -keystore "path to cacerts file"`
 
    Ebben az esetben a következő lesz:
 
    > `keytool -import -file "your downloaded root cert file" -alias "some meaningful name" $JAVA_HOME/jre/lib/security/cacerts`


### <a name="if-using-a-custom-java-keystore"></a>Egyéni Java-tároló használata esetén:

Ha egyéni Java-tárolót használ, előfordulhat, hogy importálnia kell az Application Insights-végpont (ok) legfelső szintű SSL-tanúsítványát.
A probléma megoldásához a következő két lépést javasoljuk:
1. Az alábbi [lépéseket](#steps-to-download-ssl-certificate) követve letöltheti a főtanúsítványt a Application Insights végpontról.
2. Az alábbi parancs használatával importálja a gyökér SSL-tanúsítványt az egyéni Java-tárolóba:
    > `keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name.cer" -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-ssl-certificate"></a>Az SSL-tanúsítvány letöltésének lépései

1.  Nyissa meg a kedvenc böngészőjét, és keresse `IngestionEndpoint` meg az alkalmazásához használt kapcsolódási karakterláncban található URL-címet.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png" alt-text="A Application Insightsi kapcsolatok karakterláncát megjelenítő képernyőkép." lightbox="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png":::

2.  Válassza a **hely adatainak megtekintése** (zárolás) ikont a böngészőben, majd válassza a **tanúsítvány** lehetőséget.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Képernyőfelvétel a tanúsítvány beállításról a hely adataiban." lightbox="media/java-ipa/troubleshooting/certificate-icon-capture.png":::

3.  A "Leaf" tanúsítvány letöltése helyett le kell töltenie a "root" tanúsítványt az alábbi ábrán látható módon. Később kattintson a tanúsítvány elérési útja elemre, > válassza ki a főtanúsítványt – > kattintson a tanúsítvány megtekintése elemre. Ekkor megjelenik egy új tanúsítvány menü, és letöltheti a tanúsítványt az új menüből.

    :::image type="content" source="media/java-ipa/troubleshooting/root-certificate-selection.png" alt-text="Képernyőkép a főtanúsítvány kiválasztásáról." lightbox="media/java-ipa/troubleshooting/root-certificate-selection.png":::

4.  Lépjen a **részletek** lapra, és válassza a **Másolás fájlba** lehetőséget.
5.  Válassza a **tovább** gombot, válassza a **Base-64 kódolású X. 509 (. CER)** formátuma, majd kattintson ismét a **tovább** gombra.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="A tanúsítvány-Exportálás varázsló képernyőképe, a kiválasztott formátummal." lightbox="media/java-ipa/troubleshooting/certificate-export-wizard.png":::

6.  Válassza ki azt a fájlt, ahová menteni szeretné az SSL-tanúsítványt. Ezután válassza a **következő**  >  **Befejezés** lehetőséget. "Az Exportálás sikeres volt" üzenetnek kell megjelennie.

> [!WARNING]
> Az aktuális tanúsítvány lejárta előtt meg kell ismételnie ezeket a lépéseket az új tanúsítvány beszerzéséhez. A lejárati információkat a **tanúsítvány** párbeszédpanel **részletek** lapján tekintheti meg.
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Az SSL-tanúsítvány részleteit megjelenítő képernyőkép." lightbox="media/java-ipa/troubleshooting/certificate-details.png":::
