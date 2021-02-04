---
title: A Azure Data Factory saját üzemeltetésű integrációs moduljának hibakeresése
description: Ismerje meg, hogy miként lehet elhárítani a Azure Data Factory a saját üzemeltetésű integrációs modul hibáit.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: lle
ms.openlocfilehash: 3735d7b21faf62905ed4c06dbef80c9737e7329e
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551076"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Helyi integrációs modul hibaelhárítása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory a saját üzemeltetésű integrációs modul (IR) gyakori hibaelhárítási módszereit vizsgálja.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Saját üzemeltetésű IR-naplók összegyűjtése Azure Data Factory

A saját üzemeltetésű vagy megosztott IR-ben futó sikertelen tevékenységek esetében Azure Data Factory támogatja a hibák megtekintését és feltöltését. A hibajelentési azonosító lekéréséhez kövesse az itt található utasításokat, majd adja meg a jelentés AZONOSÍTÓját a kapcsolódó ismert problémák kereséséhez.

1. A Data Factory területen válassza a **folyamat futtatása** lehetőséget.

1. A **tevékenység futtatása** alatt, a **hiba** oszlopban kattintson a Kiemelt gombra a tevékenységek naplóinak megjelenítéséhez, ahogy az alábbi képernyőfelvételen látható:

    ![Képernyőkép a "tevékenységek futtatása" szakaszról a "minden folyamat futtatása" panelen.](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

    A tevékenység naplói megjelennek a sikertelen tevékenység futtatásához.

    ![Képernyőfelvétel a sikertelen tevékenységhez tartozó tevékenységek naplóiról.](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png) 
    
1. További segítségért válassza a **naplók küldése** lehetőséget.
 
   Megnyílik a saját üzemeltetésű **integrációs modul (IR) naplóinak megosztása a Microsoft** ablakával.

    ![Képernyőkép a "saját üzemeltetésű Integration Runtime (IR) naplók megosztása a Microsofttal" ablak.](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Válassza ki az elküldeni kívánt naplókat. 
    * A *saját* üzemeltetésű integrációs modul esetében feltölthet olyan naplókat, amelyek a sikertelen tevékenységgel vagy a saját üzemeltetésű integrációs modul összes naplójának használatával kapcsolatosak. 
    * *Megosztott IR* esetén csak a sikertelen tevékenységgel kapcsolatos naplók tölthetők fel.

1. A naplók feltöltésekor tartsa a jelentés AZONOSÍTÓjának rekordját későbbi használatra, ha további segítségre van szüksége a probléma megoldásához.

    ![Képernyőkép a megjelenített jelentés AZONOSÍTÓról az IR naplók feltöltési folyamat ablakában.](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> A naplózási és feltöltési kérelmeket az összes online saját üzemeltetésű IR-példányon hajtja végre. Ha bármilyen napló hiányzik, ellenőrizze, hogy az összes saját üzemeltetésű IR-példány online állapotban van-e. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Helyi integrációs modul – általános meghibásodás vagy hiba

### <a name="out-of-memory-issue"></a>Nincs probléma a memóriában

#### <a name="symptoms"></a>Hibajelenségek

OutOfMemoryException (bácsi) hiba történik, amikor keresési tevékenységet próbál futtatni egy csatolt IR-vel vagy egy saját üzemeltetésű IR-vel.

#### <a name="cause"></a>Ok

Egy új tevékenység egy bácsi-hibát okozhat, ha az IR-gép a pillanatnyi nagy memóriahasználat megtapasztalja. A problémát nagy mennyiségű párhuzamos tevékenység okozhatja, és a hiba a terv szerint történik.

#### <a name="resolution"></a>Feloldás

Keresse meg az erőforrás-használatot és az egyidejű tevékenységek végrehajtását az IR-csomóponton. Állítsa be a tevékenységek belső és kiváltó időpontját, hogy elkerülje a túl sok végrehajtást egy adott IR-csomóponton.

### <a name="concurrent-jobs-limit-issue"></a>Egyidejű feladatok korlátjával kapcsolatos probléma

#### <a name="symptoms"></a>Hibajelenségek

Ha az egyidejű feladatok korlátját a Azure Data Factory felületen próbálja meg emelni, a folyamat a *frissítés* állapota alatt leáll.

Példa: az egyidejű feladatok maximális száma jelenleg 24 értékre van állítva, és azt szeretné, hogy a feladatok gyorsabban fussanak. A megadható minimális érték 3, a maximális érték pedig 32. Az értéket 24 – 32, majd a **frissítés** gombra kattintva növelheti. A folyamat beragad a *frissítési* állapotba, ahogy az alábbi képernyőképen is látható. Frissíti az oldalt, és az érték továbbra is 24-ként jelenik meg. A várt módon nem frissült 32-re.

![Képernyőkép az integrációs modul csomópontok paneljéről, amely megjeleníti a "frissítés" állapotba ragadt folyamatot.](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Ok

Az egyidejű feladatok számának korlátozása a számítógép Logic Core-és memóriájától függ. Próbálja meg lefelé módosítani az értéket egy értékre (például 24), majd tekintse meg az eredményt.

> [!TIP] 
> - Ha többet szeretne megtudni a logikai mag darabszámáról és a gép logikai alapszámának meghatározásáról, tekintse meg [a következő négy módszert: a processzorok számának megállapítása a CPU-ban Windows 10 rendszerben](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - A Math. log kiszámításához nyissa meg a logaritmus- [kalkulátort](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>Saját üzemeltetésű IR magas rendelkezésre állás (HA) SSL-tanúsítvány probléma

#### <a name="symptoms"></a>Hibajelenségek

A saját üzemeltetésű IR-munkacsomópont a következő hibát jelezte:

"Nem sikerült lekérni a megosztott állapotokat az elsődleges csomópontból. TCP://abc.cloud.corp.Microsoft.com: 8060/ExternalService. SVC/. Tevékenység azonosítója: XXXXX az X. 509 tanúsítvány CN = ABC. Cloud. Corp. microsoft. com, OU = Test, O = a Microsoft lánc kiépítése nem sikerült. A használt tanúsítvány megbízhatósági lánca nem ellenőrizhető. Cserélje le a tanúsítványt, vagy módosítsa a certificateValidationMode. A visszavonási függvény nem tudta megnézni a visszavonást, mert a visszavonási kiszolgáló offline állapotban volt. "

#### <a name="cause"></a>Ok

Ha az SSL/TLS-kézfogáshoz kapcsolódó eseteket kezeli, előfordulhat, hogy a tanúsítványlánc ellenőrzésével kapcsolatos problémák merülhetnek fel. 

#### <a name="resolution"></a>Feloldás

- Íme egy gyors, intuitív módszer egy X. 509 tanúsítványlánc-létrehozási hiba megoldásához:
 
    1. Exportálja az ellenőrizni kívánt tanúsítványt. Ehhez tegye a következőket:
    
       a. A Windows területen válassza a **Start**, a **tanúsítványok** beírása, majd a **számítógép-tanúsítványok kezelése** lehetőséget.
       
       b. A Fájlkezelőben a bal oldali ablaktáblán keresse meg az ellenőriznie kívánt tanúsítványt, kattintson rá a jobb gombbal, majd válassza a **minden feladat**  >  **Exportálás** lehetőséget.
    
        ![Képernyőfelvétel: "minden feladat" > "Exportálás" vezérlő a tanúsítványhoz a "számítógép-tanúsítványok kezelése" panelen.](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Másolja az exportált tanúsítványt az ügyfélszámítógépre. 
    3. Az ügyfél oldalon, a parancssori ablakban futtassa a következő parancsot. Ügyeljen arra, hogy *\<certificate path>* *\<output txt file path>* a és az aktuális elérési utakat cserélje le.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Például:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Keressen hibákat a kimeneti TXT-fájlban. A hiba összegzése a TXT-fájl végén található.

        Például: 

        ![Képernyőkép a TXT-fájl végén található hiba összegzéséről.](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Ha nem jelenik meg hibaüzenet a naplófájl végén, ahogy az alábbi képernyőképen is látható, azt is megteheti, hogy a tanúsítványlánc sikeresen létrejött az ügyfélszámítógépen.
        
        ![Képernyőkép a naplófájlról, amely nem tartalmaz hibát.](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Ha egy AIA (szolgáltatói információ-hozzáférés), CDP (CRL-elérési pont) vagy OCSP (online tanúsítványállapot-protokoll) fájlnévkiterjesztés van konfigurálva a tanúsítványfájl-ben, akkor azt intuitív módon is megtekintheti:
 
    1. Ezt az információt a tanúsítvány részleteinek ellenőrzésével érheti el, ahogy az alábbi képernyőképen is látható:
    
        ![Képernyőkép a tanúsítvány részleteiről.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    
    1. Futtassa az alábbi parancsot. Ügyeljen arra, hogy a a *\<certificate path>* tanúsítvány tényleges elérési útját cserélje le.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        Megnyílik az URL-lekérési eszköz. 
        
    1. Az AIA, a CDP és az OCSP fájlnév-kiterjesztésű tanúsítványok ellenőrzéséhez válassza a **lekérés** lehetőséget.

        ![Képernyőkép az URL-lekérési eszközről és a beolvasás gombról.](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        Sikeresen létrehozta a tanúsítványláncot, ha *ellenőrizte* a tanúsítvány ÁLLAPOTÁT az AIA-ból, és a rendszer *ellenőrizte* a tanúsítvány állapotát a CDP vagy az OCSP alapján.

        Ha az AIA vagy a CDP beolvasására irányuló kísérlet során nem sikerül, a hálózati csapattal együttműködve kérje le az ügyfélszámítógépet a cél URL-címhez való csatlakozásra. Elég lesz, ha a HTTP-elérési út vagy a Lightweight Directory Access Protocol (LDAP) elérési útja ellenőrizhető.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>A helyi integrációs modul nem tudja betölteni a fájlt vagy a szerelvényt

#### <a name="symptoms"></a>Hibajelenségek

A következő hibaüzenet jelenik meg:

"Nem tölthető be a következő fájl vagy szerelvény:" XXXXXXXXXXXXXXXX, Version = 4.0.2.0, Culture = semleges, PublicKeyToken = XXXXXXXXX "vagy annak egyik függősége. A megadott fájl nem található. Tevékenység azonosítója: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3 "
 
Az alábbiakban egy konkrétabb hibaüzenet jelenik meg: 

"Nem tölthető be a következő fájl vagy szerelvény: System. ValueTuple, Version = 4.0.2.0, Culture = semleges, PublicKeyToken = XXXXXXXXX" vagy annak valamelyik függősége. A megadott fájl nem található. Tevékenység azonosítója: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3 "

#### <a name="cause"></a>Ok

A folyamat figyelője szolgáltatásban a következő eredményt tekintheti meg:

[![Képernyőkép a Process monitor paths listájáról.](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> A Process monitor szolgáltatásban a következő képernyőképen látható szűrőket állíthatja be.
>
> Az előző hibaüzenet azt mondja, hogy a DLL-rendszer. ValueTuple nem a kapcsolódó *globális szerelvény-gyorsítótár* (GAC) mappában, a *c:\Program Files\microsoft Integration Runtime\4.0\Gateway* mappában vagy a *c:\Program Files\Microsoft Integration Runtime\4.0\Shared* mappában található.
>
> Alapvetően a folyamat először tölti be a DLL-t a *GAC* mappájából, majd a *megosztott* mappából, végül pedig az *átjáró* mappájából. Ezért a DLL-t bármely hasznos elérési útról betöltheti.

<br>

![Képernyőkép a "folyamat figyelése szűrőről" oldalról, amely felsorolja a DLL szűrőit.](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Feloldás

A *System.ValueTuple.dll* fájl a *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* mappában található. A probléma megoldásához másolja a *System.ValueTuple.dll* fájlt a *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* mappába.

Ugyanezt a módszert használhatja a hiányzó fájl-vagy összeállítási problémák megoldásához.

#### <a name="more-information-about-this-issue"></a>További információ erről a hibáról

A *%windir%\Microsoft.NET\assembly* és a *%windir%\assembly* alatti *System.ValueTuple.dll* az az oka, hogy ez egy .net-viselkedés. 

A következő hiba esetén látható, hogy a *System. ValueTuple* szerelvény hiányzik. Ez a probléma akkor fordul elő, amikor az alkalmazás megpróbálja megnézni a *System.ValueTuple.dll* szerelvényt.
 
" \<LogProperties> \<ErrorInfo> [{" Code ": 0," üzenet ":" a "Npgsql. PoolManager" típus-inicializáló kivételt váltott ki. "," EventType ": 0," kategória ": 5," adat": {} ," MsgId ": NULL," ExceptionType ":" System. TypeInitializationException "," Source ":" Npgsql "," StackTrace ":" "," InnerEventInfos ": [{" code ": 0," Message ":" nem tölthető be a fájl vagy szerelvény "System. ValueTuple, Version = 4.0.2.0, Culture = semleges, PublicKeyToken = XXXXXXXXX" vagy az egyik függősége. A rendszeren nem található a megadott fájl. "," EventType ": 0," kategória ": 5," információ ": {} ," MsgId":NULL,"ExceptionType":"System. IO. FileNotFoundException "," Source ":" Npgsql "," StackTrace ":" "," InnerEventInfos ": []}]}] \</ErrorInfo> \</LogProperties> "
 
További információ a GAC-ról: [globális szerelvény-gyorsítótár](/dotnet/framework/app-domains/gac).


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>Hiányzik a saját üzemeltetésű Integration Runtime hitelesítési kulcsa

#### <a name="symptoms"></a>Hibajelenségek

A saját üzemeltetésű integrációs modul hirtelen offline állapotba kerül a hitelesítési kulcs nélkül, és az Eseménynapló a következő hibaüzenetet jeleníti meg: 

"A hitelesítési kulcs még nincs hozzárendelve"

![Képernyőkép az Integration Runtime esemény panelről, amely azt mutatja, hogy a hitelesítési kulcs még nincs hozzárendelve.](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Ok

- A rendszer törölte a saját üzemeltetésű IR-csomópontot vagy a Azure Portal logikai saját üzemeltetésű IR-t.
- A rendszer tiszta eltávolítást hajtott végre.

#### <a name="resolution"></a>Feloldás

Ha az előző okok egyike sem érvényes, lépjen a *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway* mappába, és ellenőrizze, hogy a *konfigurációs* fájl törölve lett-e. Ha törölték, kövesse a Netwrix cikkben található utasításokat, és [észlelje, hogy ki törölt egy fájlt a Windows-fájlkiszolgálók közül](https://www.netwrix.com/how_to_detect_who_deleted_file.html).

![Képernyőkép az Eseménynapló részletei panelről a konfigurációk fájljának ellenőrzéséhez.](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>Nem használható a saját üzemeltetésű integrációs modul a két helyszíni adattárolók áthidalása érdekében

#### <a name="symptoms"></a>Hibajelenségek

Miután létrehozta a saját üzemeltetésű IRs-t a forrás-és a cél adattárolóhoz, össze kívánja kapcsolni a két IRs-t a másolási tevékenység befejezéséhez. Ha az adattárolók különböző virtuális hálózatokban vannak konfigurálva, vagy az adattárolók nem értik az átjáró mechanizmusát, akkor a következő hibák valamelyike jelenik meg: 

* "A forrás illesztőprogramja nem található a cél IR-ben"
* "A forrás nem érhető el a cél IR-vel"
 
#### <a name="cause"></a>Ok

A saját üzemeltetésű integrációs modul egy másolási tevékenység központi csomópontjának lett kialakítva, nem pedig az egyes adattárokhoz telepítendő ügyfél-ügynökhöz.
 
Ebben az esetben létre kell hoznia a társított szolgáltatást az egyes adattárolók esetében ugyanazzal az IR-vel, és az IR-nek képesnek kell lennie az adattár elérésére a hálózaton keresztül. Nem számít, hogy az IR telepítve van-e a forrás-adattárba vagy a célhely adattárolóba, vagy egy harmadik gépen. Ha két társított szolgáltatás eltérő IRs-vel lett létrehozva, de ugyanabban a másolási tevékenységben használják, a cél IR-t használja a rendszer, és telepítenie kell mindkét adattár illesztőprogramjait a cél IR-gépen.

#### <a name="resolution"></a>Feloldás

Telepítse az illesztőprogramokat mind a forrás-, mind a cél adattárolóhoz a cél IR-re, és győződjön meg arról, hogy az képes hozzáférni a forrás-adattárhoz.
 
Ha a forgalom nem haladhat át a hálózaton két adattár között (például két virtuális hálózatban van konfigurálva), akkor előfordulhat, hogy nem végezheti el a másolást egy tevékenységben még a telepített IR-vel sem. Ha nem tudja befejezni a másolást egyetlen tevékenységben, két másolási tevékenységet hozhat létre két IRs-vel, mindegyiket egy SZELLŐZŐben: 
* Egy IR másolása az adattárból az Azure-ba Blob Storage
* Másoljon egy másik IR-t az Azure Blob Storageból a 2. adattárba. 

Ez a megoldás szimulálhatja azt a követelményt, hogy az IR használatával olyan hidat hozzon létre, amely összekapcsolja a két leválasztott adattárolót.


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>A hitelesítő adatok szinkronizálása probléma miatt a hitelesítő adatok elvesztése HA

#### <a name="symptoms"></a>Hibajelenségek

Ha az adatforráshoz tartozó "XXXXXXXXXX" hitelesítő adat törölve lett az aktuális Integration Runtime-csomópontból a hasznos adatokkal, a következő hibaüzenet jelenik meg:

"Ha törli a Azure Portal a link Service-t, vagy a feladat nem megfelelő adattartalommal rendelkezik, hozzon létre egy új link Service-t a hitelesítő adataival."

#### <a name="cause"></a>Ok

A saját üzemeltetésű integrációs modul HA két csomóponttal rendelkezik, de a csomópontok nem a hitelesítő adatok szinkronizálási állapotában vannak. Ez azt jelenti, hogy a kézbesítő csomópontban tárolt hitelesítő adatok nem szinkronizálhatók más munkavégző csomópontokkal. Ha bármilyen feladatátvétel történik a diszpécser csomópontról a feldolgozó csomópontra, és a hitelesítő adatok csak az előző diszpécser csomópontban találhatók, a feladat sikertelen lesz, amikor a hitelesítő adatokhoz próbál hozzáférni, és az előző hibaüzenetet kapja.

#### <a name="resolution"></a>Feloldás

A probléma elkerülésének egyetlen módja, ha ellenőrzi, hogy a két csomópont a hitelesítő adatok szinkronizálási állapotában van-e. Ha nincsenek szinkronban, újra meg kell adnia az új diszpécser hitelesítő adatait.


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>A tanúsítvány nem választható, mert a titkos kulcs hiányzik.

#### <a name="symptoms"></a>Hibajelenségek

* A PFX-fájlt importálta a tanúsítványtárolóba.
* Ha a tanúsítványt az IR Configuration Manager felhasználói felületén keresztül választotta, a következő hibaüzenetet kapta:

   "Az intranetes kommunikáció titkosítási módjának módosítása sikertelen. Valószínű, hogy a (z) " \<*certificate name*> " tanúsítvány nem rendelkezhet olyan titkos kulccsal, amely képes a kulcscsere használatára, vagy a folyamat nem rendelkezhet hozzáférési jogokkal a titkos kulcshoz. A részletek a belső kivételben találhatók. "

    ![Képernyőfelvétel a Integration Runtime Configuration Manager beállítások panelről, "titkos kulcs hiánya" hibaüzenet jelenik meg.](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Ok

- A felhasználói fiók alacsony jogosultsági szinttel rendelkezik, és nem fér hozzá a titkos kulcshoz.
- A tanúsítvány aláírásként lett létrehozva, de nem kulcscsereként.

#### <a name="resolution"></a>Feloldás

* A felhasználói felület működtetéséhez olyan fiókot használjon, amely megfelelő jogosultságokkal rendelkezik a titkos kulcs eléréséhez.  
* Importálja a tanúsítványt a következő parancs futtatásával:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

## <a name="self-hosted-ir-setup"></a>Saját üzemeltetésű IR-beállítás

### <a name="integration-runtime-registration-error"></a>Integrációs modul regisztrációs hibája 

#### <a name="symptoms"></a>Hibajelenségek

Előfordulhat, hogy esetenként egy saját üzemeltetésű integrációs modult szeretne futtatni egy másik fiókban a következő okok valamelyike miatt:
- A vállalati házirend nem engedélyezi a szolgáltatásfiókot.
- Némi hitelesítés szükséges.

Miután módosította a szolgáltatásfiókot a szolgáltatás ablaktáblán, előfordulhat, hogy az integrációs modul működése leáll, és a következő hibaüzenet jelenik meg:

"A Integration Runtime (helyi) csomópont hibát észlelt a regisztráció során. Nem lehet csatlakozni a Integration Runtime (helyi) gazdagép szolgáltatásához. "

![A Integration Runtime Configuration Manager ablak képernyőképe, amely egy IR-regisztrációs hibát jelez.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Ok

Számos erőforrás csak a szolgáltatásfiók számára adható meg. Ha egy másik fiókra módosítja a szolgáltatásfiókot, az összes függő erőforrás engedélyei változatlanok maradnak.

#### <a name="resolution"></a>Feloldás

A hiba vizsgálatához lépjen az Integration Runtime eseménynaplóba.

![Képernyőkép az IR-Eseménynaplóról, amely azt jelzi, hogy futásidejű hiba történt.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

* Ha az eseménynaplóban szereplő hiba "UnauthorizedAccessException", tegye a következőket:

    1. A Windows szolgáltatás paneljén keresse meg a *DIAHostService* bejelentkezési szolgáltatás fiókját.

        ![Képernyőkép a bejelentkezési szolgáltatásfiók tulajdonságai panelről.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Ellenőrizze, hogy a bejelentkezési szolgáltatás fiókja rendelkezik-e olvasási/írási engedéllyel a *%ProgramData%\Microsoft\DataTransfer\DataManagementGateway* mappához.

        - Alapértelmezés szerint, ha a szolgáltatás bejelentkezési fiókja nem módosult, olvasási/írási engedéllyel kell rendelkeznie.

            ![Képernyőkép a szolgáltatás engedélyei panelről.](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

        - Ha módosította a szolgáltatás bejelentkezési fiókját, a következő lépésekkel csökkentheti a problémát:
 
            a. Végezze el a jelenlegi saját üzemeltetésű integrációs modul tiszta eltávolítását.   
            b. Telepítse a saját üzemeltetésű IR-biteket.  
            c. Módosítsa a szolgáltatásfiókot a következő módon:  

             i. Nyissa meg a saját üzemeltetésű IR telepítési mappát, majd váltson a *Microsoft Integration Runtime\4.0\Shared* mappára.  
             ii. Nyisson meg egy parancssori ablakot emelt szintű jogosultságok használatával. Cserélje le a *\<user>* és a *\<password>* nevet a saját felhasználónevére és jelszavára, majd futtassa a következő parancsot:   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. Ha a LocalSystem fiókra szeretne váltani, ügyeljen arra, hogy a fiók megfelelő formátumát használja: `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                Ne *használja ezt* a formátumot: `dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`     
             iv. Opcionálisan, mivel a helyi rendszer magasabb jogosultságokkal rendelkezik, mint a rendszergazda, közvetlenül is megváltoztathatja azt a "szolgáltatások" szolgáltatásban.  
             v. Helyi/tartományi felhasználót is használhat az IR szolgáltatás bejelentkezési fiókjához.            

            d. Regisztrálja az integrációs modult.

* Ha a hiba a "Service" Integration Runtime szolgáltatás (DIAHostService) indítása sikertelen volt. Ellenőrizze, hogy rendelkezik-e megfelelő jogosultsággal a rendszerszolgáltatások indításához, tegye a következőket:

    1. A Windows szolgáltatás paneljén keresse meg a *DIAHostService* bejelentkezési szolgáltatás fiókját.
    
        ![Képernyőkép a szolgáltatásfiók "Bejelentkezés" paneljéről.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Ellenőrizze, hogy a bejelentkezési szolgáltatás fiókja rendelkezik-e **szolgáltatás** engedéllyel a Windows-szolgáltatás indításához:

        ![Képernyőkép a "Bejelentkezés szolgáltatásként" tulajdonságok panelről.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>További információ

Ha a fenti két feloldási minta egyike sem vonatkozik az adott esetre, próbálja meg összegyűjteni a következő Windows-eseménynaplókat: 
- Az alkalmazások és szolgáltatások naplói > Integration Runtime
- Windows-naplók > alkalmazás

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>Nem található a regisztráció gomb a saját üzemeltetésű integrációs modul regisztrálásához    

#### <a name="symptoms"></a>Hibajelenségek

Ha saját üzemeltetésű integrációs modult regisztrál, a **regisztráció** gomb nem jelenik meg a Configuration Manager ablaktáblán.

![Képernyőkép a Configuration Manager panelről, amely egy üzenetet jelenít meg, amely szerint az Integration Runtime csomópontja nincs regisztrálva.](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Ok

A Integration Runtime 3,0 kiadásának köszönhetően a meglévő Integration Runtime-csomópontokon található **regisztráció** gomb el lett távolítva a tisztább és biztonságosabb környezet lehetővé tételéhez. Ha egy csomópont regisztrálva van egy integrációs modulban, függetlenül attól, hogy online állapotban van-e vagy sem, regisztrálja újra egy másik integrációs modulba az előző csomópont eltávolításával, majd telepítse és regisztrálja a csomópontot.

#### <a name="resolution"></a>Feloldás

1. A Vezérlőpulton távolítsa el a meglévő integrációs modult.

    > [!IMPORTANT] 
    > Az alábbi folyamat során válassza az **Igen** lehetőséget. Az eltávolítási folyamat során ne tartsa meg az adattárolást.

    ![Képernyőfelvétel: az "igen" gomb az összes felhasználói adatok az integrációs modulból való törléséhez.](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Ha nem rendelkezik az Integration Runtime telepítő MSI-fájljával, lépjen a [letöltőközpontban](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) a legújabb Integration Runtime letöltéséhez.
1. Telepítse az MSI-fájlt, és regisztrálja az integrációs modult.


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>Nem lehet regisztrálni a saját üzemeltetésű IR-t a localhost miatt    

#### <a name="symptoms"></a>Hibajelenségek

Get_LoopbackIpOrName használatakor nem lehet regisztrálni a saját üzemeltetésű IR-t egy új gépre.

**Hibakeresés:** Futásidejű hiba történt.
A „Microsoft.DataTransfer.DIAgentHost.DataSourceCache” típusinicializáló kivételt jelzett.
Nem helyreállítható hiba történt egy adatbázis-keresés során.
 
**Kivétel részletei:** System. TypeInitializationException: a "Microsoft. DataTransfer. DIAgentHost. DataSourceCache" típus inicializáló kivételt váltott ki. ---> System .net. Sockets. SocketException: A rendszer nem helyreállítható hibát észlelt a System .net. DNS. GetAddrInfo (karakterlánc neve) adatbázisban való keresése során.

#### <a name="cause"></a>Ok

A probléma általában akkor fordul elő, ha a localhost megoldódik.

#### <a name="resolution"></a>Feloldás

A fájl üzemeltetéséhez és a probléma megoldásához használja a localhost IP-cím 127.0.0.1 nevet.

### <a name="self-hosted-setup-failed"></a>A saját üzemeltetésű telepítés nem sikerült    

#### <a name="symptoms"></a>Hibajelenségek

Nem lehet eltávolítani egy meglévő IR-t, új IR-t telepíteni, vagy egy meglévő IR-t frissíteni egy új IR-re.

#### <a name="cause"></a>Ok

Az Integration Runtime telepítése a Windows Installer szolgáltatástól függ. A telepítési problémák a következő okokból merülhetnek fel:
- Nincs elég szabad lemezterület.
- Engedélyek hiánya.
- A Windows NT szolgáltatás zárolva van.
- A CPU-kihasználtság túl magas.
- Az MSI-fájl lassú hálózati helyen van tárolva.
- Egyes rendszerfájlok vagy beállításjegyzékek véletlenül nem voltak megérintve.

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>Az IR szolgáltatási fiók nem tudta beolvasni a tanúsítvány-hozzáférést

#### <a name="symptoms"></a>Hibajelenségek

Ha Microsoft Integration Runtime Configuration Manager használatával telepít egy saját üzemeltetésű integrációs modult, a rendszer létrehoz egy megbízható hitelesítésszolgáltatóval (CA) rendelkező tanúsítványt. Nem lehetett alkalmazni a tanúsítványt a két csomópont közötti kommunikáció titkosítására, és a következő hibaüzenet jelenik meg: 

"Nem sikerült módosítani az intranetes kommunikációs titkosítási módot: nem sikerült megadnia Integration Runtime szolgáltatásfiók hozzáférését a (z) tanúsítványhoz \<*certificate name*> . 103-es hibakód

![A hibaüzenetet megjelenítő képernyőkép: "... Nem sikerült megadni Integration Runtime szolgáltatásfiók tanúsítvány-hozzáférését ".](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Ok

A tanúsítvány a kulcstároló-szolgáltató (KSP) tárolóját használja, amely még nem támogatott. A dátumig a saját üzemeltetésű IR csak a kriptográfiai szolgáltató (CSP) tárolót támogatja.

#### <a name="resolution"></a>Feloldás

Ebben az esetben javasoljuk, hogy a CSP-tanúsítványokat használja.

**1\. megoldás** 

A tanúsítvány importálásához futtassa a következő parancsot:

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

![A tanúsítvány importálására szolgáló certutil-parancs képernyőképe.](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**2\. megoldás** 

A tanúsítvány konvertálásához futtassa a következő parancsokat:

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

Konvertálás előtt és után:

![Képernyőkép az eredményről a tanúsítvány konvertálása előtt.](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Képernyőkép az eredményről a tanúsítvány konvertálása után.](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>Saját üzemeltetésű Integration Runtime 5. x verziója
A Azure Data Factory saját üzemeltetésű integrációs modul 5. x verziójára való frissítéshez a .net- **keretrendszer futtatókörnyezetének 4.7.2** vagy újabb verzióra van szükség. A letöltési oldalon a legújabb 4. x-es verzióra és a legújabb két 5. x verzióra mutató hivatkozásokat talál. 

Azure Data Factory v2-ügyfelek esetén:
- Ha az automatikus frissítés be van kapcsolva, és már frissítette a .NET-keretrendszer futtatókörnyezetét a 4.7.2 vagy újabb verzióra, a rendszer automatikusan frissíti a saját üzemeltetésű integrációs modult a legújabb 5. x verzióra.
- Ha az automatikus frissítés be van kapcsolva, és nem frissítette a .NET-keretrendszer futtatókörnyezetét a 4.7.2 vagy újabb verzióra, a saját üzemeltetésű integrációs modul nem frissül automatikusan a legújabb 5. x verzióra. A saját üzemeltetésű integrációs modul a jelenlegi 4. x verzióban fog maradni. A .NET-keretrendszer futtatókörnyezetének frissítését a Portálon és a saját üzemeltetésű Integration Runtime-ügyfélen tekintheti meg.
- Ha az automatikus frissítés ki van kapcsolva, és a .NET-keretrendszer futtatókörnyezetét már 4.7.2 vagy újabb verzióra frissítette, akkor manuálisan letöltheti a legújabb 5. x verziót, és telepítheti a gépre.
- Ha az automatikus frissítés ki van kapcsolva, és nem frissítette a .NET-keretrendszer futtatókörnyezetét 4.7.2 vagy újabb verzióra. Amikor megpróbálja manuálisan telepíteni a saját üzemeltetésű Integration Runtime 5. x verzióját, és regisztrálni a kulcsot, először frissítenie kell a .NET-keretrendszer futtatókörnyezetének verziószámát.


Azure Data Factory v1-ügyfelek esetén:
- A saját üzemeltetésű integrációs modul 5. X verziója nem támogatja a Azure Data Factory v1-es verzióját.
- A saját üzemeltetésű Integration Runtime automatikusan a 4. x legújabb verziójára lesz frissítve. És a 4. x legújabb verziója nem jár le. 
- Ha megpróbálja manuálisan telepíteni a saját üzemeltetésű Integration Runtime 5. x verzióját, és regisztrálni a kulcsot, értesítést kap arról, hogy a saját üzemeltetésű Integration Runtime 5. x verziója nem támogatja a Azure Data Factory v1-et.


## <a name="self-hosted-ir-connectivity-issues"></a>Saját üzemeltetésű IR-kapcsolati problémák

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>A saját üzemeltetésű Integration Runtime nem tud kapcsolódni a Cloud Service-hez

#### <a name="symptoms"></a>Hibajelenségek

Amikor megkísérli regisztrálni a saját üzemeltetésű integrációs modult, Configuration Manager a következő hibaüzenetet jeleníti meg:

"A Integration Runtime (helyi) csomópont hibát észlelt a regisztráció során."

![Képernyőfelvétel: "a Integration Runtime (helyi) csomópont hibát észlelt a regisztráció során" üzenet.](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Ok 

A saját üzemeltetésű integrációs modul nem tud kapcsolódni a Azure Data Factory szolgáltatás háttér-végponthoz. Ezt a problémát általában a tűzfal hálózati beállításai okozzák.

#### <a name="resolution"></a>Feloldás

1. Ellenőrizze, hogy fut-e az Integration Runtime szolgáltatás. Ha igen, folytassa a 2. lépéssel.
    
   ![Képernyőfelvétel: a saját üzemeltetésű IR szolgáltatás fut.](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Ha nincs proxy konfigurálva a saját üzemeltetésű integrációs modulban, ez az alapértelmezett beállítás, futtassa a következő PowerShell-parancsot azon a gépen, amelyen a saját üzemeltetésű Integration Runtime telepítve van:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > A szolgáltatás URL-címe eltérő lehet a saját adatelőállító-példányának helyétől függően. A szolgáltatás URL-címének megkereséséhez válassza az **ADF felhasználói felület**  >  **kapcsolatainak**  >  **integrációs** modulok  >  **Szerkesztés saját üzemeltetésű IR**  >  -**csomópontok**  >  **nézet szolgáltatás URL-címei** elemet.
            
    A várt válasz a következő:
            
    ![Képernyőkép a PowerShell-parancs válaszáról.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Ha nem kapja meg a várt választ, használja a következő módszerek egyikét a megfelelő módon:
            
    * Ha "a távoli név nem oldható fel" üzenet jelenik meg, a tartománynévrendszer (DNS) problémája van. A probléma megoldásához forduljon a hálózati csapathoz.
    * Ha "SSL/TLS-tanúsítvány nem megbízható" üzenetet kap, [ellenőrizze a tanúsítványon](https://wu2.frontend.clouddatahub.net/) , hogy megbízható-e a gépen, majd telepítse a nyilvános tanúsítványt a Tanúsítványkezelő használatával. A műveletnek csökkentenie kell a problémát.
    * Nyissa meg a **Windows**  >  **Eseménynapló (naplók)**  >  **alkalmazások és szolgáltatások naplóit**  >  **Integration Runtime**, és keresse meg a DNS, a tűzfalszabály vagy a vállalati hálózati beállítások által okozott hibát. Ha ilyen hibát talál, kényszerítse a kapcsolatok bezárását. Mivel minden vállalat saját hálózati beállításokkal rendelkezik, a problémák elhárításához forduljon a hálózati csapatához.

1. Ha a "proxy" konfigurálva van a saját üzemeltetésű integrációs modulban, ellenőrizze, hogy a proxykiszolgáló hozzáférhet-e a szolgáltatási végponthoz. A minta parancsokért lásd: [PowerShell, webes kérelmek és proxyk](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

A várt válasz a következő:
            
![Képernyőkép a PowerShell-parancs várt válaszáról.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Proxyval kapcsolatos megfontolások:
> * Ellenőrizze, hogy be kell-e állítani a proxykiszolgálót a biztonságos címzettek listájára. Ha igen, győződjön meg arról, hogy [ezek a tartományok](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) a biztonságos címzettek listán vannak.
> * Ellenőrizze, hogy a "wu2.frontend.clouddatahub.net/" SSL/TLS-tanúsítvány megbízható-e a proxykiszolgálón.
> * Ha Active Directory hitelesítést használ a proxyn, módosítsa a szolgáltatásfiókot arra a felhasználói fiókra, amely a proxyt "Integration Runtime szolgáltatás" néven éri el.

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>Hibaüzenet: a saját üzemeltetésű integrációs modul csomópontja/logikai saját üzemeltetésű IR inaktív/"futó (korlátozott)" állapotban van.

#### <a name="cause"></a>Ok 

Előfordulhat, hogy a saját üzemeltetésű integrált futásidejű csomópont **inaktív** állapotú, ahogy az alábbi képernyőképen is látható:

![Képernyőfelvétel a saját üzemeltetésű, integrált futásidejű csomópontról inaktív állapottal](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Ez a viselkedés akkor fordul elő, ha a csomópontok nem tudnak kommunikálni egymással.

#### <a name="resolution"></a>Feloldás

1. Jelentkezzen be a csomóponton futó virtuális gépre (VM). Az **alkalmazások és szolgáltatások naplóiban**  >  **Integration Runtime**, nyissa meg Eseménynapló, és szűrje a hibákat a naplók alapján.

1. Ellenőrizze, hogy a hibanapló tartalmazza-e a következő hibát: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Ha ezt a hibát látja, futtassa a következő parancsot egy parancssori ablakban: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Ha a "nem sikerült megnyitni a kapcsolatot a gazdagéphez" parancssori hibaüzenet jelenik meg, amely a következő képernyőképen látható, lépjen kapcsolatba az informatikai részleggel a probléma megoldásához. A sikeres Telnet után lépjen kapcsolatba Microsoft ügyfélszolgálata, ha továbbra is problémái vannak az Integration Runtime csomópontjának állapotával.
        
   ![Képernyőkép: a (z) "nem sikerült megnyitni a gazdagéphez való kapcsolódást" parancssori hiba.](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Ellenőrizze, hogy a hibanapló tartalmazza-e a következő bejegyzést:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. A probléma megoldásához próbálja meg a következő módszerek egyikét vagy mindkettőt:
    - Helyezze az összes csomópontot ugyanabba a tartományba.
    - Adja hozzá az IP-címet a gazdagépek leképezéséhez az összes üzemeltetett virtuális gép gazdagépének fájljaiban.

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>Kapcsolódási probléma a saját üzemeltetésű integrációs modul és a saját maga által üzemeltetett IR-példány, illetve a saját üzemeltetésű integrációs modul, valamint az adatforrás vagy a fogadó között

A hálózati kapcsolat problémájának megoldásához ismernie kell a hálózati nyomkövetés összegyűjtését, a használatának megismerését és [a Microsoft Hálózatfigyelő (netmon) nyomkövetés elemzését](#analyze-the-netmon-trace) , mielőtt a saját üzemeltetésű integrációs modulból valós esetekben alkalmazná a netmon eszközöket.

#### <a name="symptoms"></a>Hibajelenségek

Időnként előfordulhat, hogy a saját üzemeltetésű integrációs modul és a saját maga által üzemeltetett adatfeldolgozó-példány között bizonyos kapcsolódási problémák elhárítására van szükség, ahogyan az a következő képernyőképen vagy a saját üzemeltetésű IR és az adatforrás vagy a fogadó között látható. 

![A "feldolgozott HTTP-kérelem sikertelen" üzenet képernyőképe](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Mindkét példányban a következő hibák merülhetnek fel:

* "A másolás a következő hibával meghiúsult: Type = Microsoft. DataTransfer. Common. Shared. HybridDeliveryException, Message = nem tud csatlakozni SQL Server a következőhöz:" IP-cím ""

* "Egy vagy több hiba történt. Hiba történt a kérelem elküldésekor. Az alapul szolgáló internetkapcsolat bezárult: váratlan hiba történt a fogadásban. Nem olvashatók be az adatok a szállítási kapcsolatban: a távoli gazdagép kényszerített módon lezárta a meglévő kapcsolatokat. A távoli gazdagép tevékenység-azonosítója kényszerített módon lezárta a meglévő kapcsolatokat. "

#### <a name="resolution"></a>Feloldás

Amikor az előző hibákba ütközik, kövesse az ebben a szakaszban található utasításokat.

- Netmon-nyomkövetés gyűjtése az elemzéshez: 

    1. A szűrőt beállíthatja úgy, hogy a kiszolgálóról az ügyfél oldalára állítsa vissza a visszaállítást. A következő példában látható képernyőképen láthatja, hogy a kiszolgálóoldali oldal a Data Factory-kiszolgáló.

        ![Képernyőfelvétel a The The Refactory-kiszolgálóról.](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

    1. A csomag alaphelyzetbe állítása után a következő Transmission Control Protocol (TCP) alapján keresheti meg a beszélgetést.

        ![Képernyőkép a TCP-beszélgetésről.](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

    1. A szűrő eltávolításával lekérheti a beszélgetést az ügyfél és a Data Factory-kiszolgáló között.

        ![Képernyőkép a beszélgetés részleteiről.](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)

- Az összegyűjtött netmon-nyomkövetés elemzése azt mutatja, hogy az élettartam (TTL) összesen 64. Az [IP-cím élettartama (TTL) és az ugrási korlát alapjai](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) című cikkben említett értékek szerint az alábbi listában kinyert érték azt mutatja, hogy ez a Linux rendszer, amely alaphelyzetbe állítja a csomagot, és leválasztja a leválasztást.

    Az alapértelmezett TTL-és ugrás-korlátozási értékek a különböző operációs rendszerek között változnak, az itt felsoroltak szerint:
    - Linux kernel 2,4 (kb. 2001): 255 a TCP, a User Datagram Protocol (UDP) és a Internet Control Message Protocol (ICMP) számára
    - Linux kernel 4,10 (2015): 64 TCP, UDP és ICMP rendszerhez
    - Windows XP (2001): 128 TCP, UDP és ICMP esetén
    - Windows 10 (2015): 128 TCP, UDP és ICMP esetén
    - Windows Server 2008:128 TCP, UDP és ICMP esetén
    - Windows Server 2019 (2018): 128 TCP, UDP és ICMP rendszerhez
    - macOS (2001): 64 a TCP, az UDP és az ICMP protokollhoz

    ![A 61-es TTL-értéket ábrázoló képernyőkép.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Az előző példában az élettartam 61 helyett 64, mert amikor a hálózati csomag eléri a célját, a különböző ugrásokra, például útválasztók vagy hálózati eszközökre kell mutatnia. Az útválasztók vagy hálózati eszközök száma a végső TTL előállítására van levonva.
    
    Ebben az esetben láthatja, hogy az Alaphelyzetbe állítás a Linux rendszerből a TTL 64-mel is elvégezhető.

-  Annak megerősítéséhez, hogy az eszköz alaphelyzetbe állítása honnan származhat, ellenőrizze a negyedik ugrást a saját üzemeltetésű integrációs modulból.
 
    *Hálózati csomag az A Linux rendszertől a TTL 64-> B TTL 64 mínusz 1 = 63-> C TTL 63 mínusz 1 = 62-> TTL 62 mínusz 1 = 61 saját üzemeltetésű IR*

- Ideális esetben a TTL-ugrások száma 128, ami azt jelenti, hogy a Windows operációs rendszer futtatja a saját adatfeldolgozó-példányát. Ahogy az az alábbi példában is látható, a *128 mínusz 107 = 21 ugrás*, ami azt jelenti, hogy a csomaghoz tartozó 21 ugrást a rendszer az adatgyár-példányból a saját üzemeltetésű IR-be küldi a TCP 3 kézfogás során.
 
    ![A 107-es TTL-értéket ábrázoló képernyőkép.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Ezért meg kell adnia a hálózati csapatot, hogy ellenőrizze, mi a negyedik Ugrás a saját üzemeltetésű IR-ből. Ha ez a tűzfal, a Linux rendszerhez hasonlóan ellenőrizze a naplókat, hogy miért állítja vissza az eszköz a csomagot a TCP 3 kézfogás után. 
    
    Ha nem tudja, hol kell megvizsgálnia, próbálja meg a saját üzemeltetésű integrációs modul és a tűzfal netmon-nyomkövetését a problémás idő alatt. Ez a megközelítés segít kideríteni, hogy melyik eszközre lehet alaphelyzetbe állítani a csomagot, és a leválasztást okozta. Ebben az esetben arra is szükség van, hogy a hálózati csapatot tovább folytassa.

### <a name="analyze-the-netmon-trace"></a>A netmon nyomkövetésének elemzése

> [!NOTE] 
> Az alábbi utasítások a netmon-nyomkövetésre vonatkoznak. Mivel a netmon nyomkövetés jelenleg nem támogatott, a Wireshark erre a célra használhatja.

Ha a **8.8.8.8 888** -as verzióval próbálkozik az összegyűjtött netmon-nyomkövetéssel, az alábbi képernyőképeken látnia kell a nyomkövetést:

![A "nem sikerült megnyitni a csatlakozást a gazdagéphez a 888-es porton" hibaüzenet jelenik meg.](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![A netmon nyomkövetés leírását ábrázoló képernyőkép.](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Az előző képek azt mutatják, hogy a **888**-es porton nem lehetett TCP-kapcsolat a **8.8.8.8** -kiszolgáló oldalára, így két **SynReTransmit** további csomag jelenik meg. Mivel a Source **HOST2** nem tudott csatlakozni az **8.8.8.8** -hez az első csomaggal, a kapcsolat megpróbálkozik.

> [!TIP]
> A kapcsolódáshoz próbálkozzon a következő megoldással:
> 1. Válassza a **Betöltés szűrő**  >  **szabványos szűrő**  >  **címek**  >  **IPv4-címek** lehetőséget.
> 1. A szűrő alkalmazásához írja be a következőt: **IPv4. címek = = 8.8.8.8**, majd válassza az **alkalmaz** lehetőséget. Ekkor meg kell jelennie a helyi gép és a cél **8.8.8.8** közötti kommunikációnak.

![A szűrési címeket bemutató képernyőkép.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![A további szűrési címeket bemutató képernyőkép.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

A sikeres forgatókönyvek az alábbi példákban láthatók: 

- Ha a 53-es Telnet- **8.8.8.8** bármilyen probléma nélkül elvégezhető, akkor sikeres volt a TCP 3-kézfogás, és a munkamenet egy TCP 4 kézfogással fejeződik be.

    ![A sikeres csatlakoztatási forgatókönyvet bemutató képernyőkép.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![A sikeres kapcsolati forgatókönyv részleteit bemutató képernyőkép.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Az előző TCP 3-kézfogás a következő munkafolyamatot állítja elő:

    ![Egy TCP 3 kézfogási munkafolyamat ábrája.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- A munkamenet befejezéséhez a TCP 4 kézfogást a következő munkafolyamatok illusztrálják:

    ![Képernyőkép a TCP 4 kézfogás részleteiről.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![A TCP 4 kézfogási munkafolyamatának ábrája.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>Microsoft e-mail értesítés a hálózati konfiguráció frissítéséről

Előfordulhat, hogy a következő e-mail-értesítést kapja, amely azt javasolja, hogy frissítse a hálózati konfigurációt, hogy lehetővé tegye az új IP-címekkel való kommunikációt a Azure Data Factory a 2020. november 8.

   ![A hálózati konfiguráció frissítését kérő Microsoft e-mail-értesítés képernyőképe.](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="determine-whether-this-notification-affects-you"></a>Annak megállapítása, hogy az értesítés hatással van-e

Ez az értesítés a következő forgatókönyvekre vonatkozik:

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>1. forgatókönyv: kimenő kommunikáció egy helyi integrációs modulból, amely a helyszínen fut a vállalati tűzfal mögött

Az érintettek meghatározása:

- *Nem* érinti a tűzfalszabályok olyan teljes tartománynevek (FQDN-EK) alapján történő definiálását, amelyek a [tűzfalbeállítások beállítása és az IP-címek engedélyezési listája](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-addresses)című részben leírt módszert használják.

- Az *érintett,* ha explicit módon engedélyezi a kimenő IP-címek engedélyezési listáját a vállalati tűzfalon.

   Ha érintett, végezze el a következő műveletet: 2020. november 8-án értesítse a hálózati infrastruktúra csapatát, hogy frissítse a hálózati konfigurációt a legújabb, a gyári IP-címek használatára. A legújabb IP-címek letöltéséhez nyissa meg a [szolgáltatás címkéit a letölthető JSON-fájlok használatával](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>2. forgatókönyv: a saját üzemeltetésű integrációs modul kimenő kommunikációja, amely egy Azure-beli virtuális GÉPEN fut egy ügyfél által felügyelt Azure-beli virtuális hálózaton belül

Az érintettek meghatározása:

- Ellenőrizze, hogy rendelkezik-e kimenő hálózati biztonsági csoporttal (NSG) a saját üzemeltetésű integrációs modult tartalmazó privát hálózatban. Ha nincs kimenő korlátozás, a rendszer nem érinti.

- Ha a kimenő szabályok korlátozásai vannak, ellenőrizze, hogy a szolgáltatás címkéit használja-e. Ha szolgáltatási címkéket használ, nem érinti a rendszer. Nem kell semmit módosítania vagy hozzáadnia, mert az új IP-címtartomány a meglévő szolgáltatási címkék alatt található. 

  ![Képernyőkép – a cél DataFactory, amely a célhelyként jeleníti meg a célhelyet.](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

- Ha *explicit* módon engedélyezi a kimenő IP-címek engedélyezési LISTÁJÁT a NSG-szabályok beállításban az Azure-beli virtuális hálózaton.

   Ha érintett, tegye a következőket: 2020. november 8-án értesíti a hálózati infrastruktúra csapatát, hogy frissítse az Azure-beli virtuális hálózati konfiguráció NSG-szabályait, hogy a legfrissebb adatgyári IP-címeket használják. A legújabb IP-címek letöltéséhez nyissa meg a [szolgáltatás címkéit a letölthető JSON-fájlok használatával](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>3. forgatókönyv: a SSIS Integration Runtime kimenő kommunikációja az ügyfél által felügyelt Azure-beli virtuális hálózaton

Az érintettek meghatározása:

- Ellenőrizze, hogy van-e kimenő NSG-szabály egy SQL Server Integration Services (SSIS) Integration Runtime tartalmazó privát hálózaton. Ha nincs kimenő korlátozás, a rendszer nem érinti.

- Ha a kimenő szabályok korlátozásai vannak, ellenőrizze, hogy a szolgáltatás címkéit használja-e. Ha szolgáltatási címkéket használ, nem érinti a rendszer. Nem kell módosítania vagy felvennie semmit, mert az új IP-címtartomány a meglévő szolgáltatási címkék alatt található.

- Ha *explicit* módon engedélyezi a kimenő IP-címek engedélyezési LISTÁJÁT a NSG-szabályok beállításban az Azure-beli virtuális hálózaton.

  Ha érintett, tegye a következőket: 2020. november 8-án értesíti a hálózati infrastruktúra csapatát, hogy frissítse az Azure-beli virtuális hálózati konfiguráció NSG-szabályait, hogy a legfrissebb adatgyári IP-címeket használják. A legújabb IP-címek letöltéséhez nyissa meg a [szolgáltatás címkéit a letölthető JSON-fájlok használatával](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>Nem sikerült megbízhatósági kapcsolatot létesíteni az SSL/TLS biztonságos csatorna számára 

#### <a name="symptoms"></a>Hibajelenségek

A saját üzemeltetésű integrációs modul nem tudott csatlakozni a Azure Data Factory szolgáltatáshoz.

Ha bejelöli a saját üzemeltetésű IR-eseménynaplót vagy az ügyfél-értesítési naplókat a CustomLogEvent táblában, a következő hibaüzenet jelenik meg:

"Az alapul szolgáló kapcsolat bezárult: nem hozható létre megbízhatósági kapcsolat az SSL/TLS biztonságos csatorna számára. A távoli tanúsítvány az érvényesítési eljárás szerint érvénytelen. "

A Data Factory szolgáltatás kiszolgálói tanúsítványának megkeresésének legegyszerűbb módja az Data Factory szolgáltatás URL-címének megnyitása a böngészőben. Nyissa meg például a [kiszolgáló tanúsítványának keresése hivatkozást](https://eu.frontend.clouddatahub.net/) azon a gépen, amelyen a saját üzemeltetésű integrációs modul telepítve van, majd tekintse meg a kiszolgálói tanúsítvány információit.

  ![Képernyőkép a Azure Data Factory szolgáltatás ellenőrzési kiszolgáló tanúsítvány paneljéről.](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Képernyőkép az ablakról a kiszolgálói tanúsítvány elérési útjának ellenőrzéséhez.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Ok

Ennek a hibának két lehetséges oka van:

- 1. ok: a Data Factory Service Server-tanúsítvány legfelső szintű HITELESÍTÉSSZOLGÁLTATÓja nem megbízható azon a gépen, amelyen a saját üzemeltetésű IR telepítve van. 
- 2. ok: proxyt használ a környezetben, a Data Factory szolgáltatás kiszolgálói tanúsítványát a proxy váltja fel, és a lecserélt kiszolgáló tanúsítványa nem megbízható a saját üzemeltetésű integrációs modult futtató gép számára.

#### <a name="resolution"></a>Feloldás

- 1. ok: Ellenőrizze, hogy a Data Factory-kiszolgáló tanúsítványát és annak tanúsítványláncét a számítógép, amelyen a saját üzemeltetésű integrációs modul telepítve van-e.
- 2. ok: bízza meg a lecserélt legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT a saját üzemeltetésű IR-gépen, vagy konfigurálja úgy a proxyt, hogy ne cserélje le a Data Factory-kiszolgáló tanúsítványát.

További információ a Windows rendszerű tanúsítványok megbízhatóságáról: [a megbízható legfelső szintű tanúsítvány telepítése](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate).

#### <a name="additional-information"></a>További információ
Egy új SSL-tanúsítványt vezettünk be, amely a DigiCert-ből van aláírva. Ellenőrizze, hogy a DigiCert globális root G2 a megbízható legfelső szintű HITELESÍTÉSSZOLGÁLTATÓban van-e.

  ![Képernyőfelvétel: a DigiCert globális root G2 mappája a megbízható legfelső szintű hitelesítésszolgáltatók könyvtárában.](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Ha nem a megbízható legfelső szintű HITELESÍTÉSSZOLGÁLTATÓban [van, töltse le itt](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ). 


## <a name="self-hosted-ir-sharing"></a>Saját üzemeltetésű integrációs modul megosztása

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>A saját üzemeltetésű integrációs modul más Bérlővel való megosztása nem támogatott 

#### <a name="symptoms"></a>Hibajelenségek

Előfordulhat, hogy más adatgyárakat (különböző bérlőket) is figyelembe kell vennie, ahogy a saját üzemeltetésű integrációs modult szeretné megosztani a Azure Data Factory felhasználói felületen, de nem oszthatja meg a különböző bérlők adatelőállítói között.

#### <a name="cause"></a>Ok

A saját üzemeltetésű IR nem osztható meg a bérlők között.

## <a name="next-steps"></a>Következő lépések

A hibaelhárítással kapcsolatos további segítségért próbálkozzon a következő erőforrásokkal:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [A Microsoft Q&egy oldalt](/answers/topics/azure-data-factory.html)
*  [Stack túlfolyó fórum a Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Adatforgalom teljesítményének feltérképezése útmutató](concepts-data-flow-performance.md)
