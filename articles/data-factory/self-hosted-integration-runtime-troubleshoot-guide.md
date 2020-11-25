---
title: A Azure Data Factory saját üzemeltetésű integrációs moduljának hibakeresése
description: Ismerje meg, hogy miként lehet elhárítani a Azure Data Factory a saját üzemeltetésű integrációs modul hibáit.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/17/2020
ms.author: lle
ms.openlocfilehash: 93c35828444ec93a974769ed3a2f1981c0ec4368
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "96013460"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul hibáinak megoldása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory saját üzemeltetésű integrációs moduljának gyakori hibaelhárítási módszereit vizsgálja.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Saját üzemeltetésű IR-naplók összegyűjtése Azure Data Factory

A saját üzemeltetésű IR/Shared IR-ben futó sikertelen tevékenységek esetében Azure Data Factory támogatja a hibanapló megtekintését és feltöltését. A hibajelentési azonosító lekéréséhez kövesse az alábbi lépéseket, majd adja meg a jelentés AZONOSÍTÓját a kapcsolódó ismert problémák megkereséséhez.

1. Ugrás a **tevékenység-futtatások** lapra.

1. A **hiba** oszlopban kattintson az alábbi gombra.

    ![Tevékenység-futtatások lapja](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. Megjelenik a sikertelen tevékenység futtatásához kapcsolódó naplók. További segítségért kattintson a **naplók küldése** gombra.

    ![Naplók küldése](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. Kiválaszthatja azokat a naplókat, amelyeket el szeretne küldeni. A *saját* üzemeltetésű integrációs modul esetében feltölthet olyan naplókat, amelyek a sikertelen tevékenységgel vagy a saját üzemeltetésű IR-csomóponton lévő összes naplóval kapcsolatosak. A *megosztott IR* esetében csak a sikertelen tevékenységhez kapcsolódó naplók tölthetők fel.

    ![Naplók kiválasztása](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. A naplók feltöltésekor őrizze meg a jelentés AZONOSÍTÓjának rekordját, ha további segítségre van szüksége a probléma megoldásához.

    ![Naplók feltöltése](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> A naplózási és a feltöltési kérelmeket az összes online saját üzemeltetésű IR-példányon végrehajtja a rendszer. Győződjön meg arról, hogy az összes saját üzemeltetésű IR-példány online állapotban van, ha a hiányzó naplók. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Helyi integrációs modul – általános meghibásodás vagy hiba

### <a name="out-of-memory-issue"></a>Nincs probléma a memóriában

#### <a name="symptoms"></a>Hibajelenségek

A "OutOfMemoryException" probléma akkor fordul elő, ha a keresési tevékenységet a társított IR vagy a saját üzemeltetésű IR-vel próbálja futtatni.

#### <a name="cause"></a>Ok

Az új tevékenység a bácsi (OutOfMemory) hibával találkozhat, ha az IR-gépen jelenleg nagy a memóriahasználat. A problémát az egyidejű tevékenység-végrehajtás nagy mérete okozhatja, és a hiba a terv szerint történik.

#### <a name="resolution"></a>Feloldás

Ellenőrizze az erőforrás-használat és az egyidejű tevékenység végrehajtását az IR-csomóponton. Állítsa be a tevékenységek belső és kiváltó időpontját, hogy elkerülje a túl sok végrehajtást ugyanazon az IR-csomóponton.


### <a name="tlsssl-certificate-issue"></a>TLS-/SSL-tanúsítványhiba

#### <a name="symptoms"></a>Hibajelenségek

Ha TLS-/SSL-tanúsítványt szeretné engedélyezni (haladó) a **Helyi integrációs modul – konfigurációkezelő** -> **Távoli hozzáférés intranetről** használatával, a TLS-/SSL-tanúsítvány kiválasztása után az alábbi hiba jelenik meg:

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

A fenti esetben a felhasználó azt a tanúsítványt használja, amely nevének végén a „microsoft.com” áll.

#### <a name="cause"></a>Ok

Ez egy ismert probléma a WCF-ben: A WCF TLS-/SSL-ellenőrzési funkciója csak az SAN utolsó DNS-nevét ellenőrzi. 

#### <a name="resolution"></a>Feloldás

Az altartományokra is kibővített tanúsítvány támogatott az Azure Data Factory v2 helyi integrációs moduljában. Ez a probléma általában akkor jelentkezik, ha az SSL-tanúsítvány nem megfelelő. Az SAN utolsó DNS-nevének érvényesnek kell lennie. Az ellenőrzéshez kövesse az alábbi lépéseket. 
1.  Nyissa meg a felügyeleti konzolt, és a tanúsítvány részletei között kattintson a *tulajdonos* és a *tulajdonos alternatív neve* elemre. A fenti esetben például a *tulajdonos alternatív neve*("DNS Name = Microsoft.com.com") utolsó eleme nem legitim.
2.  A hibás DNS-név eltávolításához vegye fel a kapcsolatot a tanúsítvány kiállító vállalatával.

### <a name="concurrent-jobs-limit-issue"></a>Egyidejű feladatok korlátjával kapcsolatos probléma

#### <a name="symptoms"></a>Hibajelenségek

Amikor növelni próbálja az egyidejű feladatokra vonatkozó korlátot az Azure Data Factory felhasználói felületén, a rendszer *frissítés* közben lefagy.
Az egyidejű feladatok maximális értéke 24-re van beállítva, és növelni szeretné ezt a számot, hogy a feladatok gyorsabban futhassanak. A megadható minimális érték 3, a megadható maximális érték pedig 32. Az értéket 24 – 32 értékre növelte, és a *frissítés* gombra kattintva a felhasználói felületen megakadt *a frissítés, ahogy az* alábbiakban látható. A frissítés után az ügyfél továbbra is a 24-es értéket látta, az soha nem frissült 32-re.

![Frissítés állapota](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Ok

A beállításra korlátozás vonatkozik, mivel az érték a számítógép logicCore és Memória értékeitől függ, módosíthatja tehát egyszerűen egy kisebb értékre (például 24-re), és láthatja az eredményt.

> [!TIP] 
> - A logikai alapértékek számáról és a gép logikai alapszámának megkereséséről [ebben a cikkben](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)talál további információkat.
> - A Math. log kiszámításával kapcsolatos további információkért tekintse meg [ezt a cikket](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Helyi integrációs modul magas rendelkezésre állású SSL-tanúsítványával kapcsolatos probléma

#### <a name="symptoms"></a>Hibajelenségek

A helyi integrációs modul munkacsomópontja az alábbi hibát jelentette:

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Ok

Az SSL-/TLS-kézfogással kapcsolatos esetek kezelésekor előfordulhat, hogy tanúsítványlánc-ellenőrzéssel kapcsolatos problémákba ütközünk. 

#### <a name="resolution"></a>Feloldás

- Íme egy gyors és intuitív módszer az X. 509 tanúsítványlánc-létrehozási hiba megoldásához.
 
    1. Exportálja az ellenőrizni kívánt tanúsítványt. Lépjen a Számítógép-tanúsítvány kezelése területre, keresse meg az ellenőrizni kívánt tanúsítványt, majd kattintson a jobb egérgombbal a **Minden feladat** -> **Exportálás** elemre.
    
        ![Tevékenységek exportálása](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Másolja az exportált tanúsítványt az ügyfélszámítógépre. 
    3. Az ügyféloldalon futtassa a CMD-ben az alábbi parancsot. Győződjön meg arról, hogy az alábbi *\<certificate path>* és *\<output txt file path>* a helyőrzők a kapcsolódó elérési utakkal lettek lecserélve.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Például:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Ellenőrizze, hogy van-e hiba a kimeneti TXT-fájlban. A hiba összegzését a TXT-fájl végén találja.

        Például: 

        ![Hiba összegzése](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Ha nem lát hibát a naplófájl végén a lent látható módon, azt is megteheti, hogy a tanúsítványlánc sikeresen felépítve lett az ügyfélszámítógépen.
        
        ![Nincs hiba a naplófájlban](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Ha az AIA, a CDP és az OCSP konfigurálva van a tanúsítványfájl-fájlban. A szolgáltatás intuitív módon is ellenőrizhető.
 
    1. Ezt az információt a tanúsítvány részleteinek ellenőrzésével kérheti le.
    
        ![Tanúsítvány részletei](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. Futtassa az alábbi parancsot. Győződjön meg arról, hogy *\<certificate path>* a helyőrzőt a tanúsítvány kapcsolódó elérési útjára cserélte.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. Ezután megnyílik az **URL-lekérési eszköz**. Az AIA-, CDP- és OCSP-tanúsítványok ellenőrzéséhez kattintson a **Beolvasás** gombra.

        ![Lekérés gomb](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        A tanúsítványlánc sikeresen létrehozható, ha az AIA-tanúsítvány állapota „Ellenőrzött”, valamint a CDP- vagy OCSP-tanúsítvány állapota is „Ellenőrzött”.

        Ha az AIA és a CDP lekérése során hiba lép fel, működjön együtt a hálózatkezelésért felelős csapattal az ügyfélgép a cél URL-címhez való csatlakoztatásának előkészítésében. Elegendő, ha a HTTP- vagy az LDAP-útvonal ellenőrizhető.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>A helyi integrációs modul nem tudja betölteni a fájlt vagy a szerelvényt

#### <a name="symptoms"></a>Hibajelenségek

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
Például: 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>Ok

Ha folyamat-figyelőt használ, a következő eredményt láthatja:

[![Folyamat figyelője](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> Az alábbi képernyőképen látható módon állíthatja be a szűrőt.
> Azt mondja nekünk, hogy a dll- **rendszer. a ValueTuple** nem a GAC-hoz kapcsolódó mappában, vagy a *c:\Program Files\microsoft Integration Runtime\4.0\Gateway* vagy a *c:\Program Files\Microsoft Integration Runtime\4.0\Shared* mappában található.
> A rendszer a DLL-t alapvetően először a *GAC*-mappából tölti be, majd a *Közös*, végül pedig az *Átjáró* mappából. Ezért a DLL-t bármely olyan elérési útra áthelyezheti, amely hasznos lehet.

![Szűrők beállítása](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Feloldás

Megtalálhatja, hogy a **System.ValueTuple.dll** a *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* mappában található. A probléma megoldásához másolja a **System.ValueTuple.dll** a *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* mappába.

Ugyanezt a módszert használhatja más, hiányzó fájllal vagy szerelvénnyel kapcsolatos problémák megoldásához is.

#### <a name="more-information"></a>További információ

Annak oka, hogy a *%windir%\Microsoft.NET\assembly* és a *%windir%\assembly* alatti System.ValueTuple.dll látható, hogy .net-viselkedés. 

Az alábbi hibaüzenetből tisztán láthatja a szerelvény *rendszerét. a ValueTuple* nem létezik. Így ez a probléma akkor fordul elő, amikor az alkalmazás megpróbálja megnézni a szerelvény *System.ValueTuple.dll*.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
A GAC-val kapcsolatos további információkért tekintse meg [ezt a cikket](/dotnet/framework/app-domains/gac).


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>A helyi integrációs modul hiányzó kulcsának naplózása

#### <a name="symptoms"></a>Hibajelenségek

A kulcs hiányában a helyi integrációs modul hirtelen offline állapotba lép, az eseménynaplóban az alábbi hibaüzenet jelenik meg: `Authentication Key is not assigned yet`

![Hiányzó hitelesítési kulcs](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Ok

- A helyi integrációs modul csomópontja vagy a logikai helyi integrációs modul törölve lett a portálon.
- Egyszerű eltávolítás történt.

#### <a name="resolution"></a>Feloldás

Ha a fenti okok egyike sem érvényes, akkor nyissa meg a következő mappát: *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway*, és ellenőrizze, hogy a rendszer törli-e a **konfigurációk** nevű fájlt. Ha igen, akkor kövesse az [itt található](https://www.netwrix.com/how_to_detect_who_deleted_file.html) utasításokat, hogy megtudja, ki törölte a fájlt.

![Konfigurációs fájl keresése](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>A helyi integrációs modul nem használható két helyszíni adattároló áthidalásához

#### <a name="symptoms"></a>Hibajelenségek

Miután létre lett hozva a helyi integrációs modult a forrás- és a céladattárolók esetében is, össze szeretné kapcsolni a két integrációs modult a másolás befejezéséhez. Ha az adattárak különböző virtuális hálózatok vannak konfigurálva, vagy nem tudják megérteni az átjáró mechanizmusát, a következőhöz hasonló hibák lépnek fel: *a forrás illesztőprogramja nem található a cél IR-ben*; *a cél IR nem fér hozzá a forráshoz*.
 
#### <a name="cause"></a>Ok

A helyi integrációs modul a másolási tevékenység központi csomópontjaként lett kialakítva, nem pedig az egyes adattárolók esetében telepítendő ügyfélügynökként.
 
A fenti esetben az egyes adattárolókhoz tartozó társított szolgáltatást ugyanazzal az integrációs modullal kell létrehozni, és az integrációs modulnak képesnek kell lennie az adattárolók elérésére a hálózaton keresztül. Függetlenül attól, hogy az integrációs modul a forrás adattárolóval, a cél adattárolóval vagy egy külső gépen lett telepítve, ha a két társított szolgáltatás eltérő integrációs modullal lett létrehozva, de azonos másolási tevékenységben használják őket, a rendszer a cél integrációs modult fogja használni, és mindkét adattároló illesztőit telepíteni kell a cél integrációs modul gépén.

#### <a name="resolution"></a>Feloldás

Telepítse a forrás- és a célhely illesztőit a cél integrációs modulon, és győződjön meg arról, hogy az hozzáfér a forrás adattárolóhoz.
 
Ha az adatforgalom nem tud áthaladni a két adattároló közötti hálózaton (például kétféle virtuális hálózaton lettek konfigurálva), akkor előfordulhat, hogy a másolást nem fejezi be egyetlen tevékenységben, még akkor sem, ha az integrációs modul telepítve van. Ebben az esetben létrehozhat két másolási tevékenységet két integrációs modullal, mindegyiket egy-egy virtuális hálózatban: Egy integrációs modul az 1. adattárolóból való másoláshoz az Azure Blob Storage-be, egy másik pedig az Azure Blob Storage-ból való másoláshoz a 2. adattárolóba. Ezzel szimulálható azt a követelmény, hogy a két szétkapcsolt adattárolót összekötő hidat az integrációs modul használatával kell létrehozni.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>A hitelesítő adatok szinkronizálási hibája miatt hitelesítőadat-vesztés történt a magas rendelkezésreállási szinten

#### <a name="symptoms"></a>Hibajelenségek

A rendszer törölte az adatforrás hitelesítő adatait („XXXXXXXXXX”) az aktuális integrációsmodul-csomópontról a következő hasznos adattal: „amikor törli a kapcsolati szolgáltatást az Azure Portalon, vagy a feladat hibás hasznos adattal rendelkezik, újból hozzon létre egy új kapcsolati szolgáltatást a hitelesítő adataival”.

#### <a name="cause"></a>Ok

A helyi integrációs modul két csomóponttal lett létrehozva a magas rendelkezésreállási módban, de a hitelesítő adataik nincsenek szinkronizálva, ami azt jelenti, hogy a kézbesítő csomópontban tárolt hitelesítő adatok nem lettek szinkronizálva a többi munkavégző csomóponttal. Ha bármilyen feladatátvétel történik a kézbesítő csomópontból a munkavégző csomópontba, de a hitelesítő adatok csak a korábbi kézbesítő csomópontban voltak megtalálhatók, akkor a feladat meghiúsul, amikor a hitelesítő adatokhoz próbál hozzáférni, és a fenti hibaüzenet jelenik meg.

#### <a name="resolution"></a>Feloldás

A probléma elkerülésének egyetlen módja annak biztosítása, hogy a két csomópont hitelesítő adatai szinkronizálva legyenek. Máskülönben az új kézbesítő esetében újra meg kell adnia a hitelesítő adatokat.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>A tanúsítvány nem választható ki a hiányzó titkos kulcs miatt

#### <a name="symptoms"></a>Hibajelenségek

1.  Importálja a PFX-fájlt a tanúsítványtárolóba.
2.  Ha az integrációs modul konfigurációkezelőjének felhasználói felületén keresztül választja ki a tanúsítványt, a következő hibaüzenet jelenik meg:

    ![Hiányzó titkos kulcs](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Ok

- A felhasználói fiók kevés jogosultsággal rendelkezik, és nem fér hozzá a titkos kulcshoz.
- A tanúsítvány aláírásként, de nem kulcscsereként lett létrehozva.

#### <a name="resolution"></a>Feloldás

1.  Olyan rendszerjogosultságú fiókot használjon a felhasználói felület esetében, amely hozzáfér a titkos kulcshoz.
2.  A tanúsítvány importálásához futtassa az alábbi parancsot:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>Saját üzemeltetésű IR-beállítás

### <a name="the-integration-runtime-registration-error"></a>A Integration Runtime regisztrációs hiba 

#### <a name="symptoms"></a>Hibajelenségek

Néha a saját üzemeltetésű integrációs modult egy másik fiókban szeretnénk futtatni az alábbi okokból:
- A vállalati házirend nem engedélyezi a szolgáltatásfiókot.
- Némi hitelesítés szükséges.

Miután módosította a szolgáltatásfiókot a szolgáltatás paneljén, előfordulhat, hogy a Integration Runtime működése leáll.

![IR-regisztrációs hiba](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Ok

Számos olyan erőforrás van, amely csak a szolgáltatásfiók számára van megadva. A szolgáltatásfiók másik fiókra való módosításakor az összes függő erőforrás engedélye változatlan marad.

#### <a name="resolution"></a>Feloldás

A hiba vizsgálatához lépjen a Integration Runtime eseménynaplóba.

![IR-Eseménynapló](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

Ha a hiba a fenti *UnauthorizedAccessException* jelenik meg, kövesse az alábbi utasításokat:


1. A Windows szolgáltatás paneljén keresse meg a *DIAHostService* bejelentkezési szolgáltatás fiókját.

    ![Bejelentkezési szolgáltatásfiók](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Győződjön meg arról, hogy a bejelentkezési szolgáltatás fiókja rendelkezik-e az R/W engedéllyel a (z) *%ProgramData%\Microsoft\DataTransfer\DataManagementGateway* mappában.

    - Alapértelmezés szerint, ha a szolgáltatás bejelentkezési fiókja nem módosult, akkor az R/W engedélyének kell lennie.

        ![Szolgáltatás engedélye](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - Ha módosította a szolgáltatás bejelentkezési fiókját, kövesse az alábbi lépéseket a probléma enyhítéséhez:
        1. Törölje a jelenlegi saját üzemeltetésű integrációs modul eltávolítását.
        1. Telepítse a saját üzemeltetésű IR-biteket.
        1. A szolgáltatásfiók módosításához kövesse az alábbi utasításokat: 
            1. Lépjen a saját üzemeltetésű IR telepítési mappájába, váltson a következő mappára: *Microsoft Integration Runtime\4.0\Shared*.
            1. Indítsa el a parancssort emelt szintű jogosultság használatával. Cserélje *\<user>* le *\<password>* a és a nevet a saját felhasználónevére és jelszavára, majd futtassa az alábbi parancsot:
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. Ha a LocalSystem fiókra szeretne váltani, ügyeljen arra, hogy megfelelő formátumot használjon ehhez a fiókhoz. Az alábbi példa a megfelelő formátumot mutatja be:

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                Ne **használja a** formátumot az alább látható módon:

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. Alternatív megoldásként, mivel a helyi rendszer magasabb jogosultsággal rendelkezik, mint a rendszergazda, közvetlenül is megváltoztathatja azt a "szolgáltatások" szolgáltatásban.
            1. Használhat helyi/tartományi felhasználót az IR szolgáltatás bejelentkezési fiókjához.            
        1. Regisztrálja a Integration Runtime.

Ha a hiba a következőképpen jelenik *meg: "Integration Runtime szolgáltatás" (DIAHostService) indítása sikertelen. Ellenőrizze, hogy rendelkezik-e megfelelő jogosultsággal a rendszerszolgáltatások indításához*, kövesse az alábbi utasításokat:

1. A Windows szolgáltatás paneljén keresse meg a *DIAHostService* bejelentkezési szolgáltatás fiókját.
   
    ![Bejelentkezési szolgáltatásfiók](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Győződjön meg arról, hogy a bejelentkezési szolgáltatás fiókja rendelkezik-e a Windows-szolgáltatás indításához szükséges **Bejelentkezés szolgáltatásként** :

    ![Bejelentkezés szolgáltatásként](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>További információ

Ha a fenti két minta egyike sem vonatkozik a megoldásra, próbálja meg összegyűjteni a következő Windows-eseménynaplókat: 
- Alkalmazások és szolgáltatások naplói – > Integration Runtime
- Windows-naplók – > alkalmazás

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>Nem található a regisztráció gomb a saját üzemeltetésű integrációs modul regisztrálásához    

#### <a name="symptoms"></a>Hibajelenségek

A **regisztráció** gomb nem található a Configuration Manager felhasználói felületen a saját üzemeltetésű integrációs modul regisztrálásakor.

![Nincs regisztráció gomb](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Ok

A *Integration Runtime 3,0*-es verziójának megjelenése óta egy meglévő Integration Runtime csomóponton található **regisztráció** gomb el lett távolítva a tisztább és biztonságosabb környezet lehetővé tételéhez. Ha regisztrált egy csomópontot valamilyen integrációs modulba (online vagy nem online), akkor a csomópont másik integrációs modulba történő újraregisztrálásához először el kell távolítania az előző csomópontot, és ezt követően tudja telepíteni és regisztrálni a csomópontot.

#### <a name="resolution"></a>Feloldás

1. Nyissa meg a Vezérlőpultot a meglévő Integration Runtime eltávolításához.

    > [!IMPORTANT] 
    > Az alábbi eljárásban válassza az Igen lehetőséget. Ne tartsa meg az adattárolást az eltávolítási folyamat során.

    ![Adat törlése](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Ha nem rendelkezik az Integration Runtime telepítő MSI-vel, lépjen a [letöltőközpontban](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) a legújabb Integration Runtime letöltéséhez.
1. Telepítse az MSI-t, és regisztrálja a Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>A saját üzemeltetési integrációs modul nem regisztrálható a localhost miatt    

#### <a name="symptoms"></a>Hibajelenségek

Nem lehet regisztrálni a saját üzemeltetésű integrációs modult egy új gépre get_LoopbackIpOrNamekor.

**Hibakeresés:** Futásidejű hiba történt.
A "Microsoft. DataTransfer. DIAgentHost. DataSourceCache" típusú inicializáló kivételt váltott ki.
Nem helyreállítható hiba történt egy adatbázis-keresés során.
 
**Kivétel részletei:** System. TypeInitializationException: a "Microsoft. DataTransfer. DIAgentHost. DataSourceCache" típus inicializáló kivételt váltott ki. ---> System .net. Sockets. SocketException: A rendszer nem helyreállítható hibát észlelt a System .net. DNS. GetAddrInfo (karakterlánc neve) adatbázisban való keresése során.

#### <a name="cause"></a>Ok

A probléma általában a localhost feloldásakor történik.

#### <a name="resolution"></a>Feloldás

A localhost 127.0.0.1 használata a fájl futtatásához és a probléma megoldásához.


### <a name="self-hosted-setup-failed"></a>A saját üzemeltetésű telepítés nem sikerült    

#### <a name="symptoms"></a>Hibajelenségek

Nem lehet eltávolítani egy meglévő IR-t, vagy új IR-t telepíteni vagy frissíteni egy meglévő IR-t egy új IR-re.

#### <a name="cause"></a>Ok

A telepítés a Windows Installer szolgáltatástól függ. Lehetséges, hogy a telepítési problémát a következők okozhatják:
- Nincs elég szabad lemezterület
- Engedélyek hiánya
- Az NT szolgáltatás valamilyen okból zárolva van
- A CPU-kihasználtság túl magas
- Az MSI-fájl lassú hálózati helyen van tárolva
- Egyes rendszerfájlok vagy kibocsátásiegység-forgalmi jegyzékek véletlenül lettek megérintve


### <a name="ir-service-account-failed-to-fetch-certificate-access"></a>Az IR szolgáltatási fiók nem tudta beolvasni a tanúsítvány-hozzáférést

#### <a name="symptoms"></a>Hibajelenségek

Ha saját üzemeltetésű IR-t telepít a Microsoft Integration Runtime Configuration Manageren keresztül, a rendszer létrehoz egy megbízható HITELESÍTÉSSZOLGÁLTATÓval rendelkező tanúsítványt. A tanúsítvány nem alkalmazható a két csomópont közötti kommunikáció titkosítására. 

A hiba adatai az alábbiak szerint jelennek meg: 

`Failed to change Intranet communication encryption mode: Failed to grant Integration Runtime service account the access of to the certificate 'XXXXXXXXXX'. Error code 103`

![Nem sikerült megadni az IR szolgáltatásfiók tanúsítványának elérését](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Ok

A tanúsítvány KSP (kulcstároló-szolgáltató) használ, ami még nem támogatott. A a (z) a (z) csak a CSP (kriptográfiai szolgáltató) tanúsítványát támogatja eddig.

#### <a name="resolution"></a>Feloldás

Ebben az esetben a CSP-tanúsítvány ajánlott.

**1. megoldás:** A tanúsítvány importálásához használja az alábbi parancsot:

```
Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx 
```

![A certutil használata](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**2. megoldás:** Tanúsítványok átalakítása:

OpenSSL PKCS12/pfx-profil – a .\xxxx.pfx. \ xxxx_new. PEM-Password pass:*\<EnterPassword>*

OpenSSL PKCS12/pfx-profil-export-in. \ xxxx_new. PEM-out xxxx_new. pfx

Konvertálás előtt és után:

![A tanúsítvány módosítása előtt](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![A tanúsítvány módosítása után](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)


## <a name="self-hosted-ir-connectivity-issues"></a>Saját üzemeltetésű IR-kapcsolati problémák

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>A saját üzemeltetésű Integration Runtime nem tud kapcsolódni a Cloud Service-hez

#### <a name="symptoms"></a>Hibajelenségek

![Saját üzemeltetésű IR-kapcsolati probléma](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Ok 

A saját üzemeltetésű integrációs modul nem tud kapcsolódni a Data Factory szolgáltatáshoz (háttér). Ezt a problémát általában a tűzfal hálózati beállításai okozzák.

#### <a name="resolution"></a>Feloldás

1. Győződjön meg arról, hogy fut-e az Integration Runtime szolgáltatás.
    
   ![A saját üzemeltetésű IR-szolgáltatás futási állapota](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Ha a szolgáltatás fut, folytassa a 3. lépéssel.

1. Ha nincs proxy konfigurálva a saját üzemeltetésű integrációs modulban (amely az alapértelmezett beállítás), futtassa a következő PowerShell-parancsot azon a gépen, amelyen a saját üzemeltetésű integrációs modul telepítve van:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > A szolgáltatás URL-címe eltérő lehet a Data Factory helyétől függően. A szolgáltatás URL-címét az **ADF UI**  >  **Connections**  >  **Integration Runtimes** saját üzemeltetésű  >  **IR**  >  -**csomópontok**  >  **megtekintése szolgáltatás URL-** címének szerkesztése szakaszban találja.
            
    A várt válasz a következő:
            
    ![PowerShell-parancs válasza](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Ha nem kapja meg a várt választ, használja a következő módszerek egyikét az adott helyzetnek megfelelően:
            
    * Ha "a távoli név nem oldható fel" üzenet jelenik meg, a tartománynévrendszer (DNS) problémája van. A probléma megoldásához forduljon a hálózati csapathoz.
    * Ha "SSL/TLS-tanúsítvány nem megbízható" üzenetet kap, ellenőrizze, hogy a tanúsítvány megbízható-e https://wu2.frontend.clouddatahub.net/ a gépen, majd telepítse a nyilvános tanúsítványt a Tanúsítványkezelő használatával. A műveletnek csökkentenie kell a problémát.
    * Nyissa meg a **Windows**  >  **Eseménynapló (naplók)**  >  **alkalmazásait és szolgáltatásait tartalmazó naplókat**  >  **Integration Runtime** , és keresse meg a DNS, a tűzfalszabály vagy a vállalati hálózati beállítások által okozott hibát. (Ha ilyen hibát talál, kényszerítse a kapcsolatok bezárását.) Mivel minden vállalat testreszabott hálózati beállításokat tartalmaz, a problémák elhárításához forduljon a hálózati csapatához.

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
            
![2. PowerShell-parancs válasza](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Proxyval kapcsolatos megfontolások:
> *    Győződjön meg arról, hogy a proxykiszolgálót a biztonságos címzettek listára kell helyezni. Ha igen, győződjön meg arról, hogy [ezek a tartományok](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) a biztonságos címzettek listán vannak.
> *    Győződjön meg arról, hogy a "wu2.frontend.clouddatahub.net/" TLS/SSL-tanúsítvány megbízható-e a proxykiszolgálón.
> *    Ha Active Directory hitelesítést használ a proxyn, módosítsa a szolgáltatásfiókot arra a felhasználói fiókra, amely a proxyt "Integration Runtime szolgáltatás" néven éri el.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Hibaüzenet: a saját üzemeltetésű integrációs modul csomópontja/logikai a (z) rendszer inaktív/"fut (korlátozott)" állapotban van.

#### <a name="cause"></a>Ok 

Előfordulhat, hogy a saját üzemeltetésű integrált futásidejű csomópont **inaktív** állapotú, ahogy az alábbi képernyőképen is látható:

![Inaktív Self-Hosted IR-csomópont](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Ez a viselkedés akkor fordul elő, ha a csomópontok nem tudnak kommunikálni egymással.

#### <a name="resolution"></a>Feloldás

1. Jelentkezzen be a csomópont által üzemeltetett virtuális gépre. Az **alkalmazások és szolgáltatások naplóiban**  >  **Integration Runtime**, nyissa meg Eseménynapló, és szűrje az összes hibát.

1. Győződjön meg arról, hogy a hibanapló a következő hibát tartalmazza-e: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Ha ezt a hibát látja, futtassa a következő parancsot a parancssorban: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Ha a következő hibaüzenetet kapja, lépjen kapcsolatba az informatikai részleggel a probléma megoldása érdekében. A sikeres Telnet után lépjen kapcsolatba Microsoft ügyfélszolgálata, ha továbbra is problémái vannak az integratív futtatókörnyezet csomópontjának állapotával.
        
   ![Parancssori hiba](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Győződjön meg arról, hogy a hibanapló tartalmazza-e a következőket:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. A probléma megoldásához próbálja meg a következő módszerek egyikét vagy mindkettőt:
    - Helyezze az összes csomópontot ugyanabba a tartományba.
    - Adja hozzá az IP-címet a gazdagépek leképezéséhez az összes üzemeltetett virtuális gép gazdagépének fájljaiban.

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Kapcsolódási probléma a saját üzemeltetésű IR és Data Factory vagy a saját üzemeltetésű IR és adatforrás/fogadó között

A hálózati kapcsolat problémájának megoldásához tudnia kell, hogyan gyűjtheti a hálózati nyomkövetést, hogyan használhatja azt, és [elemezheti a netmon nyomkövetést](#how-to-analyze-netmon-trace) , mielőtt a netmon-eszközöket valós esetekben alkalmazza a saját üzemeltetésű integrációs modulból.

#### <a name="symptoms"></a>Hibajelenségek

Időnként, ha a kapcsolódási problémákat (például az alábbiakat) a saját üzemeltetésű integrációs modul és a Data Factory között elhárítja: 

![HTTP-kérelem sikertelen](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Vagy a saját üzemeltetésű IR és adatforrás/fogadó között a következő hibákat tapasztaljuk:

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>Megoldás:

A fenti problémák megoldásakor a további hibaelhárításhoz tekintse meg a következő utasításokat:

Végezze el a netmon nyomkövetést, és elemezze tovább.
- Először beállíthatja a szűrőt úgy, hogy a kiszolgálóról az ügyfél felé irányuló alaphelyzetbe állítson. Az alábbi példában látható, hogy a kiszolgáló oldalon Data Factory kiszolgáló található.

    ![A adatfeldolgozó kiszolgáló](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- A csomag alaphelyzetbe állítása után a következő TCP-vel keresheti meg a beszélgetést.

    ![Beszélgetés keresése](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Ezután a szűrő eltávolításával lekérheti az ügyfél és Data Factory kiszolgáló közötti konverziót.

    ![Beszélgetés beolvasása](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Az összegyűjtött netmon-nyomkövetés alapján megtudhatjuk, hogy a TTL (TimeToLive) összesen 64. A [cikkben](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) említett **alapértelmezett TTL-és ugrási határértékek** (az alább leírtak szerint) alapján láthatjuk, hogy ez a Linux rendszer, amely alaphelyzetbe állítja a csomagot, és a leválasztást okoz.

    Az alapértelmezett TTL-és ugrás-határértékek eltérőek lehetnek a különböző operációs rendszerek között:
    - Linux kernel 2,4 (kb. 2001): 255 TCP, UDP és ICMP rendszerhez
    - Linux kernel 4,10 (2015): 64 TCP, UDP és ICMP rendszerhez
    - Windows XP (2001): 128 TCP, UDP és ICMP esetén
    - Windows 10 (2015): 128 TCP, UDP és ICMP esetén
    - Windows Server 2008:128 TCP, UDP és ICMP esetén
    - Windows Server 2019 (2018): 128 TCP, UDP és ICMP rendszerhez
    - macOS (2001): 64 a TCP, az UDP és az ICMP protokollhoz

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Azonban a fenti példában a 64 helyett 61 jelenik meg, mert ha a hálózati csomag eléri a célhelyet, a különböző ugrásokra, például útválasztók/hálózati eszközökre kell mutatnia. Az útválasztók/hálózati eszközök száma a végső TTL-ben lesz levonva.
    Ebben az esetben láthatjuk, hogy az Alaphelyzetbe állítás a 64-es TTL-vel rendelkező Linux rendszerből is elvégezhető.

- A negyedik ugrást a saját üzemeltetésű IR-ből kell ellenőrizni annak ellenőrzéséhez, hogy az eszköz alaphelyzetbe állítása honnan származhat.
 
    *Hálózati csomag az A Linux rendszertől a TTL 64-> B TTL 64 mínusz 1 = 63-> C TTL 63 mínusz 1 = 62-> TTL 62 mínusz 1 = 61 saját üzemeltetésű IR*

- Ideális esetben a TTL 128 lesz, ami azt jelenti, hogy a Windows rendszer fut a Data Factory. Ahogy az alábbi példában is látható, a *128 – 107 = 21 ugrás*, ami azt jelenti, hogy a csomaghoz tartozó 21 ugrást a rendszer a TCP 3-kézfogás során Data Factory a saját üzemeltetésű IR-be küldi.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Ezért be kell vonnia a hálózati csapatot annak ellenőrzéséhez, hogy a negyedik Ugrás a saját üzemeltetésű integrációs modulból származik-e. Ha a tűzfal Linux rendszer, akkor ellenőrizze, hogy az eszköz miért állítja vissza a csomagot a TCP 3 kézfogás után. Ha azonban nem tudja, hol kell megvizsgálnia a vizsgálatot, próbálja meg a netmon-nyomkövetést a saját üzemeltetésű integrációs modulból és a tűzfalból összekapcsolni a problémás idő alatt, hogy kiderítse, melyik eszköz állíthatja alaphelyzetbe ezt a csomagot Ebben az esetben arra is szükség van, hogy a hálózati csapatot tovább folytassa.

### <a name="how-to-analyze-netmon-trace"></a>Netmon-nyomkövetés elemzése

> [!NOTE] 
> Az alábbi utasítás a netmon nyomkövetésre vonatkozik. Mivel a netmon-nyomkövetés jelenleg nem támogatott, a Wireshark is kihasználhatja.

Ha a 888-es netmon-nyomkövetéssel rendelkező Telnet- **8.8.8.8** próbálja meg összegyűjteni, az alábbi nyomkövetést kellene látnia:

![netmon nyomkövetés 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![netmon nyomkövetés 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Ez azt jelenti, hogy a **888**-es porton alapuló TCP-csatlakozás nem végezhető el a **8.8.8.8** -kiszolgáló oldalára, így két **SynReTransmit** további csomagot láthat. Mivel a Source **HOST2** nem tudott kapcsolódni az **8.8.8.8** -hez az első csomagban, továbbra is a kapcsolódást fogja végezni.

> [!TIP]
> - Kattintson a **szűrő**  ->  **szabványos szűrő**  ->  **címek**  ->  **IPv4-címek** betöltése lehetőségre.
> - Adja meg a bemeneti **IPv4.-címek = = 8.8.8.8** szűrőként, majd kattintson az **alkalmaz** gombra. Ezt követően csak a helyi gépről érkező kommunikáció jelenik meg a cél **8.8.8.8**.

![címek szűrése 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![címek szűrése 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Az alábbi példa egy jó forgatókönyv megjelenését mutatja be. 

- Ha a Telnet **8.8.8.8 53** a probléma nélkül működik, láthatja a TCP 3 kézfogást, majd a munkamenet a TCP 4 kézfogással fejeződik be.

    ![példa az 1. jó forgatókönyvre](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![helyes forgatókönyv 2. példa](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- A fenti TCP 3 kézfogás alapján a következő munkafolyamat látható:

    ![TCP 3 kézfogás munkafolyamata](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- A munkamenet és a munkafolyamata befejezéséhez a TCP 4 kézfogás a következőképpen fog megjelenni:

    ![TCP 4 kézfogás](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4 kézfogás munkafolyamata](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


### <a name="receiving-email-to-update-the-network-configuration-to-allow-communication-with-new-ip-addresses"></a>E-mail fogadása a hálózati konfiguráció frissítéséhez az új IP-címekkel való kommunikáció engedélyezéséhez

#### <a name="email-notification-from-microsoft"></a>E-mailes értesítés a Microsofttól

Az alábbi e-mail-értesítés jelenhet meg, amely azt ajánlja, hogy frissítse a hálózati konfigurációt, hogy az új IP-címekkel kommunikáljon Azure Data Factory a 2020. november 8. között:

   ![E-mailes értesítés](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="how-to-determine-if-you-are-impacted-by-this-notification"></a>Annak megállapítása, hogy érinti-e ez az értesítés

Ez az értesítés a következő forgatókönyvekre van hatással:
##### <a name="scenario-1-outbound-communication-from-self-hosted-integration-runtime-running-on-premises-behind-the-corporate-firewall"></a>1. forgatókönyv: a vállalati tűzfal mögötti helyszínen futó helyi Integration Runtime kimenő kommunikációja
Hogyan állapítható meg, hogy hatással van-e a következőkre:
- Ha a tűzfalszabályok a teljes tartománynevek alapján a következő dokumentumban ismertetett módszerrel vannak meghatározva, akkor nem érinti a rendszer: [tűzfal-konfiguráció és engedélyezési lista beállítása az IP-címekhez](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway).
- A rendszer akkor is érintett, ha a kimenő IP-címek engedélyezési listáját explicit módon engedélyezi a vállalati tűzfalon.

Érintett művelet: a hálózati infrastruktúra csapatának értesítése a hálózati konfiguráció frissítéséhez, hogy a legújabb Data Factory IP-címeket a 2020. november 8-án használja.  A legújabb IP-címek letöltéséhez nyissa meg a [szolgáltatás címkék IP-címtartomány letöltési hivatkozását](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-2-outbound-communication-from-self-hosted-integration-runtime-running-on-an-azure-vm-inside-customer-managed-azure-virtual-network"></a>2. forgatókönyv: az Azure-beli virtuális GÉPEN futó, saját üzemeltetésű Integration Runtime kimenő kommunikációja az ügyfél által felügyelt Azure Virtual Network szolgáltatáson belül
Hogyan állapítható meg, hogy hatással van-e a következőkre:
- Ellenőrizze, hogy van-e olyan kimenő NSG-szabály a magánhálózaton, amely saját üzemeltetésű Integration Runtime tartalmaz. Ha nincsenek kimenő korlátozások, akkor nincs hatása.
- Ha a kimenő szabályok korlátozásai vannak, ellenőrizze, hogy a szolgáltatás címkéjét használja-e. Ha a szolgáltatás címkét használja, akkor nem kell módosítania vagy semmit hozzáadnia, mivel az új IP-címtartományok a meglévő szolgáltatási címke alatt találhatók. 
 ![Cél-ellenőrzési](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)
- Akkor is hatással van, ha explicit módon engedélyezi a kimenő IP-címek engedélyezési listáját a NSG-szabályok beállításban az Azure-beli virtuális hálózaton.

Érintett művelet: értesítés a hálózati infrastruktúra csapatáról az Azure-beli virtuális hálózati konfiguráció NSG-szabályainak frissítéséhez, hogy a legújabb Data Factory IP-címeket használják a 2020. november 8-án.  A legújabb IP-címek letöltéséhez nyissa meg a [szolgáltatás címkék IP-címtartomány letöltési hivatkozását](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-customer-managed-azure-virtual-network"></a>3. forgatókönyv: a SSIS Integration Runtime kimenő kommunikációja az ügyfél által felügyelt Azure virtuális hálózaton
- Ellenőrizze, hogy van-e olyan kimenő NSG-szabály a magánhálózaton, amely SSIS Integration Runtime tartalmaz. Ha nincsenek kimenő korlátozások, akkor nincs hatása.
- Ha a kimenő szabályok korlátozásai vannak, ellenőrizze, hogy a szolgáltatás címkéjét használja-e. Ha a szolgáltatás címkét használja, akkor nem kell módosítania vagy semmit hozzáadnia, mivel az új IP-címtartományok a meglévő szolgáltatási címke alatt találhatók.
- Akkor is hatással van, ha explicit módon engedélyezi a kimenő IP-címek engedélyezési listáját a NSG-szabályok beállításban az Azure-beli virtuális hálózaton.

Érintett művelet: értesítés a hálózati infrastruktúra csapatáról az Azure-beli virtuális hálózati konfiguráció NSG-szabályainak frissítéséhez, hogy a legújabb Data Factory IP-címeket használják a 2020. november 8-án.  A legújabb IP-címek letöltéséhez nyissa meg a [szolgáltatás címkék IP-címtartomány letöltési hivatkozását](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### <a name="could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Nem hozható létre megbízhatósági kapcsolat a biztonságos SSLTLS csatornához 

#### <a name="symptoms"></a>Hibajelenségek

A saját üzemeltetésű IR nem tudott kapcsolódni az ADF szolgáltatáshoz.

A következő hibaüzenet jelenik meg, ha ellenőrzi a (z) vagy az ügyfél értesítési naplóit a CustomLogEvent táblában:

`The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.The remote certificate is invalid according to the validation procedure.`

Az ADF szolgáltatás kiszolgálói tanúsítványának megkeresése:

A legegyszerűbb módszer az ADF szolgáltatás URL-címének megnyitása a böngészőben, például megnyitva a számítógépen, ahol a rendszer telepíti a-t https://eu.frontend.clouddatahub.net/ , majd megtekinti a kiszolgálói tanúsítvány információit:

  ![Az ADF szolgáltatás kiszolgálói tanúsítványának keresése](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Kiszolgálói tanúsítvány elérési útjának keresése](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Ok

A probléma két lehetséges oka:

- Az ADF szolgáltatás-kiszolgáló tanúsítványának legfelső szintű HITELESÍTÉSSZOLGÁLTATÓja nem megbízható azon a gépen, amelyen a rendszer telepíti a-t. 
- A proxyt használja a környezetében, és az ADF-kiszolgáló tanúsítványát a proxy váltja fel, a lecserélt kiszolgáló tanúsítványát pedig nem bízza meg azon a gépen, amelyen a rendszer telepíti a szolgáltatásvezérlő szolgáltatást.

#### <a name="resolution"></a>Feloldás

- Az 1. okból győződjön meg arról, hogy az ADF-kiszolgáló tanúsítványát és a tanúsítványát a rendszer megbízhatónak tartja a számítógépen, amelyen telepítve van a rendszer.
- A 2. okból bízza meg a lecserélt legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT a lecserélt számítógépen, vagy konfigurálja a proxyt, hogy ne cserélje le az ADF-kiszolgáló tanúsítványát

Tekintse át [ezt a cikket](https://docs.microsoft.com/skype-sdk/sdn/articles/installing-the-trusted-root-certificate) a Windows rendszerű tanúsítványok megbízhatóságának részleteiért.

#### <a name="additional-info"></a>További információ
Egy új SSL-tanúsítványt vezetünk be, amely a DigiCert-ből van aláírva, ellenőrizze, hogy a DigiCert globális root G2 a megbízható legfelső szintű HITELESÍTÉSSZOLGÁLTATÓban van-e.

  ![DigiCert globális root G2](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Ha nem, töltse le innen [.](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ) 


## <a name="self-hosted-ir-sharing"></a>Saját üzemeltetésű integrációs modul megosztása

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>A saját üzemeltetésű integrációs modul megosztása egy másik bérlőn nem támogatott 

#### <a name="symptoms"></a>Hibajelenségek

Előfordulhat, hogy más adatgyárakat (különböző bérlőket) is meg kell hívnia, miközben megpróbálta megosztani a saját üzemeltetésű integrációs modult Azure Data Factory felhasználói felületen, de nem tudja megosztani a saját üzemeltetésű integrációs modult különböző bérlők adatelőállítói között.

#### <a name="cause"></a>Ok

A saját üzemeltetésű integrációs modul nem osztható meg több Bérlővel.


## <a name="next-steps"></a>További lépések

A hibaelhárítással kapcsolatos további segítségért próbálkozzon a következő erőforrásokkal:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&egy kérdés oldal](/answers/topics/azure-data-factory.html)
*  [Stack túlfolyó fórum a Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Adatforgalom teljesítményének feltérképezése útmutató](concepts-data-flow-performance.md)