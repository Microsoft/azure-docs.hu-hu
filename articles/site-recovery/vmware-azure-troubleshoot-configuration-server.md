---
title: A konfigurációs kiszolgálóval kapcsolatos hibák elhárítása a VMware virtuális gépek és a fizikai kiszolgálók Azure-beli vész-helyreállítása során Azure Site Recovery használatával | Microsoft Docs
description: Ez a cikk a VMware virtuális gépek és a fizikai kiszolgálók Azure-ba való vész-helyreállítására szolgáló konfigurációs kiszolgáló a Azure Site Recovery használatával történő üzembe helyezésével kapcsolatos hibaelhárítási információkat tartalmaz.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: b5fd014732fd4cdfaa52f971b5e4d2c74db580d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371953"
---
# <a name="troubleshoot-configuration-server-issues"></a>A konfigurációs kiszolgáló problémáinak elhárítása

Ez a cikk segítséget nyújt a [Azure site Recovery](site-recovery-overview.md) konfigurációs kiszolgáló telepítésekor és felügyeletekor felmerülő problémák elhárításában. A konfigurációs kiszolgáló felügyeleti kiszolgálóként működik. A konfigurációs kiszolgáló használatával a helyszíni VMware virtuális gépeket és fizikai kiszolgálókat a Site Recovery segítségével állíthatja be az Azure-ba. A következő szakaszokban ismertetjük a leggyakoribb hibákat, amelyekkel új konfigurációs kiszolgáló hozzáadásakor és a konfigurációs kiszolgáló kezelésekor is találkozhat.

## <a name="registration-failures"></a>Regisztrációs hibák

A forrás számítógép a mobilitási ügynök telepítésekor regisztrálja a konfigurációs kiszolgálót. Ebben a lépésben a következő irányelveket követve hibakeresést végezhet:

1. Nyissa meg a C:\ProgramData\ASR\home\svsystems\var\ configurator_register_host_static_info. log fájlt. (A ProgramData mappa lehet rejtett mappa. Ha nem látja a ProgramData mappát, a Fájlkezelőben a **nézet** lap **Megjelenítés/elrejtés** szakaszában jelölje be a **rejtett elemek** jelölőnégyzetet.) Előfordulhat, hogy a hibákat több probléma okozta.

2. Keresse meg a sztringet, **nem található érvényes IP-cím**. Ha a karakterlánc megtalálható:
   1. Ellenőrizze, hogy a kért állomásnév megegyezik-e a forrásszámítógép gazdagép-azonosítójával.
   2. Ellenőrizze, hogy a forrásszámítógép rendelkezik-e legalább egy, a fizikai hálózati adapterhez hozzárendelt IP-címmel. Ahhoz, hogy az ügynök sikeres legyen, a forrásoldali gépnek rendelkeznie kell legalább egy érvényes, a fizikai hálózati adapterhez rendelt IP v4-címmel.
   3. Futtassa a következő parancsok egyikét a forrásszámítógépen a forrásoldali gép összes IP-címének lekéréséhez:
      - Windows esetén: `> ipconfig /all`
      - Linux esetén: `# ifconfig -a`

3. Ha a sztring **nem található érvényes IP-cím** , keresse meg a következő karakterláncot: **Reason =>null**. Ez a hiba akkor fordul elő, ha a forrásszámítógép üres gazdagépet használ a konfigurációs kiszolgálóval való regisztráláshoz. Ha a karakterlánc megtalálható:
    - A problémák megoldását követően kövesse a [forrásoldali számítógép regisztrálása a konfigurációs kiszolgálóval](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) című témakör útmutatásait, hogy manuálisan próbálja megismételni a regisztrációt.

4. Ha a karakterlánc **OK =>null** nem található, a forrásoldali gépen nyissa meg a C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log fájlt. (A ProgramData mappa lehet rejtett mappa. Ha nem látja a ProgramData mappát, a Fájlkezelőben a **nézet** lap **Megjelenítés/elrejtés** szakaszában jelölje be a **rejtett elemek** jelölőnégyzetet.) Előfordulhat, hogy a hibákat több probléma okozta. 

5. Keresse meg a karakterláncot **post kérelem: (7) – nem sikerült csatlakozni a kiszolgálóhoz**. Ha a karakterlánc megtalálható:
    1. Oldja meg a hálózati problémákat a forrásoldali gép és a konfigurációs kiszolgáló között. Ellenőrizze, hogy a konfigurációs kiszolgáló elérhető-e a forrás gépről hálózati eszközök, például a ping, a traceroute vagy egy webböngésző használatával. Győződjön meg arról, hogy a forrásoldali számítógép elérheti a konfigurációs kiszolgálót a 443-es porton keresztül.
    2. Győződjön meg arról, hogy a forrásoldali számítógépen található bármely tűzfalszabály blokkolja-e a forrás-és a konfigurációs kiszolgáló közötti kapcsolatot. A hálózati rendszergazdákkal együttműködve oldja fel a kapcsolódási problémákat.
    3. Győződjön meg arról, hogy az [site Recovery mappában](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) felsorolt mappák ki vannak zárva a víruskereső szoftverből.
    4. Hálózati problémák megoldásakor próbálja megismételni a regisztrációt a [forráskiszolgáló regisztrálása a konfigurációs kiszolgálóval](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)című rész útmutatásait követve.

6. Ha a string **post kérelem: (7) – nem sikerült csatlakozni a kiszolgálóhoz** , a naplófájlban keresse meg a következő karakterlánc- **kérelmet: (60) – a társ-tanúsítvány nem hitelesíthető a megadott hitelesítésszolgáltatói tanúsítványokkal**. Ez a hiba akkor fordulhat elő, ha a konfigurációs kiszolgáló tanúsítványa lejárt, vagy a forrásoldali gép nem támogatja a TLS 1,0-es vagy újabb protokollokat. Akkor is előfordulhat, ha a tűzfal blokkolja a TLS-kommunikációt a forrásoldali gép és a konfigurációs kiszolgáló között. Ha a karakterlánc megtalálható: 
    1. A megoldáshoz kapcsolódjon a konfigurációs kiszolgáló IP-címéhez a forrásoldali gépen lévő webböngészővel. Használja az URI https: \/ \/<konfigurációs kiszolgáló IP-címét \> : 443/. Győződjön meg arról, hogy a forrásoldali számítógép elérheti a konfigurációs kiszolgálót a 443-es porton keresztül.
    2. Győződjön meg arról, hogy a forrásoldali számítógépen lévő tűzfalszabályok hozzáadására vagy eltávolítására van szükség ahhoz, hogy a forrásszámítógép a konfigurációs kiszolgálóval kommunikáljon. Az esetlegesen használatban lévő tűzfal-szoftverek miatt nem lehet kilistázni az összes szükséges tűzfal-konfigurációt. A hálózati rendszergazdákkal együttműködve oldja fel a kapcsolódási problémákat.
    3. Győződjön meg arról, hogy az [site Recovery mappában](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) felsorolt mappák ki vannak zárva a víruskereső szoftverből.  
    4. A problémák megoldását követően próbálja megismételni a regisztrációt a [forráskiszolgáló regisztrálása a konfigurációs kiszolgálóval](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)című rész útmutatásait követve.

7. Linux rendszeren, ha a <INSTALLATION_DIR \> /etc/drscout.conf megsérült a platform értéke, a regisztráció meghiúsul. A probléma azonosításához nyissa meg a/var/log/ua_install. log fájlt. Keresse meg a **konfigurációt megszakító karakterláncot, mert VM_PLATFORM érték vagy Null, vagy nem VMware/Azure**. A platformot **VMware** -re vagy **Azure**-ra kell beállítani. Ha a drscout. conf fájl sérült, javasoljuk, hogy [távolítsa el a mobilitási ügynököt](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) , majd telepítse újra a mobilitási ügynököt. Ha az Eltávolítás sikertelen, hajtsa végre a következő lépéseket: a. Nyissa meg a Installation_Directory/uninstall.sh fájlt, és véleményezze a **StopServices** függvény hívását.
    b. Nyissa meg a Installation_Directory/VX/bin/uninstall.sh fájlt, és véleményezze a **stop_services** függvény hívását.
    c. Nyissa meg a Installation_Directory/FX/uninstall.sh fájlt, és tegye megjegyzésbe az FX szolgáltatás leállítására tett kísérlet teljes szakaszát.
    d. [Távolítsa el](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) a mobilitási ügynököt. A sikeres eltávolítást követően indítsa újra a rendszerét, majd próbálja meg újra telepíteni a mobilitási ügynököt.

8. Győződjön meg arról, hogy a többtényezős hitelesítés nincs engedélyezve a felhasználói fiókhoz. A Azure Site Recovery jelenleg nem támogatja a többtényezős hitelesítést a felhasználói fiókhoz. Regisztrálja a konfigurációs kiszolgálót a többtényezős hitelesítés engedélyezett felhasználói fiókja nélkül.  

## <a name="installation-failure-failed-to-load-accounts"></a>Telepítési hiba: nem sikerült betölteni a fiókokat

Ez a hiba akkor fordul elő, ha a szolgáltatás nem tudja beolvasni az adatátviteli kapcsolatokat a mobilitási ügynök telepítésekor és a konfigurációs kiszolgálóval való regisztráláskor. A probléma megoldásához ellenőrizze, hogy a TLS 1,0 engedélyezve van-e a forrásoldali gépen.

## <a name="vcenter-discovery-failures"></a>vCenter-felderítési hibák

A vCenter-felderítési hibák elhárításához adja hozzá a vCenter-kiszolgálót a mellőzési lista proxybeállításait. 

- Töltse [le a PsExec eszközt innen a](/sysinternals/downloads/psexec) rendszerfelhasználói tartalmak eléréséhez.
- Az Internet Explorer rendszerfelhasználói tartalomban való megnyitásához futtassa a következő parancssori PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"
- Adja hozzá a proxybeállításokat az IE-ben, és indítsa újra a tmanssvc szolgáltatást.
- A DRA-proxybeállítások konfigurálásához futtassa a CD C:\Program Files\Microsoft Azure Site Recovery Providert
- Következő lépésként hajtsa végre DRCONFIGURATOR.EXE/configure/AddBypassUrls [adja hozzá vCenter Server IP-címét/teljes tartománynevét az **vCenter Server/vSphere ESXi-kiszolgáló konfigurálása** a [konfigurációs kiszolgáló üzembe helyezése](vmware-azure-deploy-configuration-server.md#configure-settings)során]

## <a name="change-the-ip-address-of-the-configuration-server"></a>A konfigurációs kiszolgáló IP-címének módosítása

Azt javasoljuk, hogy ne változtassa meg a konfigurációs kiszolgáló IP-címét. Győződjön meg arról, hogy a konfigurációs kiszolgálóhoz rendelt összes IP-cím statikus IP-cím. Ne használjon DHCP IP-címeket.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: az SAML-jogkivonat érvénytelen.

A hiba elkerüléséhez győződjön meg arról, hogy a rendszeridőt követő idő több, mint 15 percen belül nem különbözik a helyi időponttól. A regisztráció befejezéséhez futtassa ismét a telepítőt.

## <a name="failed-to-create-a-certificate"></a>Nem sikerült létrehozni a tanúsítványt

Nem hozható létre Site Recovery hitelesítéshez szükséges tanúsítvány. Futtassa újra a telepítőt, miután meggyőződni arról, hogy helyi rendszergazdaként futtatja a telepítőt.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Nem sikerült aktiválni a Windows-licencet a kiszolgáló normál ÉRTÉKELÉSéről a Server standard verzióra

1. A OVF-en keresztüli konfigurációs kiszolgáló üzembe helyezésének részeként a rendszer próbaverziós licencet használ, amely 180 napig érvényes. Az érvényesség lejárta előtt aktiválni kell ezt a licencet. Ez azt eredményezheti, hogy a konfigurációs kiszolgáló gyakori leállítása, és így akadályozza a replikálási tevékenységeket.
2. Ha nem tudja aktiválni a Windows-licencet, a probléma megoldásához jusson el a [Windows támogatási csapatához](https://aka.ms/Windows_Support) .

## <a name="register-source-machine-with-configuration-server"></a>Forrásoldali számítógép regisztrálása a konfigurációs kiszolgálóval

### <a name="if-the-source-machine-runs-windows"></a>Ha a forrásszámítógép Windows rendszert futtat

Futtassa a következő parancsot a forrásoldali gépen:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Beállítás | Részletek
--- | ---
Használat | UnifiedAgentConfigurator.exe/CSEndPoint <konfigurációs kiszolgáló IP-címének \> /PassphraseFilePath <a hozzáférési kód elérési útja\>
Ügynök konfigurációs naplói | A%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log. alatt található
/CSEndPoint | Kötelező paraméter. A konfigurációs kiszolgáló IP-címét adja meg. Bármilyen érvényes IP-címet használjon.
/PassphraseFilePath |  Kötelező. A jelszó helye. Használjon bármely érvényes UNC-vagy helyi elérési útvonalat.

### <a name="if-the-source-machine-runs-linux"></a>Ha a forrásoldali gép Linux rendszert futtat

Futtassa a következő parancsot a forrásoldali gépen:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Beállítás | Részletek
--- | ---
Használat | CD-/usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh-i <konfigurációs kiszolgáló IP \> -címe – P <hozzáférési fájl elérési útja\>
-i | Kötelező paraméter. A konfigurációs kiszolgáló IP-címét adja meg. Bármilyen érvényes IP-címet használjon.
– P |  Kötelező. A fájl teljes elérési útja, amelyben a rendszer menti a jelszót. Bármilyen érvényes mappát használjon.

## <a name="unable-to-configure-the-configuration-server"></a>Nem lehet konfigurálni a konfigurációs kiszolgálót

Ha a virtuális gépen a konfigurációs kiszolgálótól eltérő alkalmazásokat telepít, előfordulhat, hogy nem tudja konfigurálni a fő célt. 

A konfigurációs kiszolgálónak egyetlen cél-kiszolgálónak kell lennie, és megosztott kiszolgálóként való használata nem támogatott. 

További információ: konfigurációs gyakori kérdések a [konfigurációs kiszolgáló üzembe helyezése](vmware-azure-deploy-configuration-server.md#faqs)című témakörben. 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>A védett elemek elavult bejegyzéseinek eltávolítása a konfigurációs kiszolgáló adatbázisából 

Az elavult védett gépek a konfigurációs kiszolgálón való eltávolításához kövesse az alábbi lépéseket. 
 
1. Az elavult bejegyzés forrásoldali számítógépének és IP-címének meghatározása: 

    1. Nyissa meg a MYSQL-cmdline rendszergazdai módban. 
    2. Hajtsa végre a következő parancsokat. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Ez visszaadja a regisztrált gépek listáját, valamint azok IP-címeit és utolsó szívverését. Keresse meg az elavult replikációs párokkal rendelkező gazdagépet.

2. Nyisson meg egy rendszergazda jogú parancssort, és navigáljon a C:\ProgramData\ASR\home\svsystems\bin. 
4. A regisztrált gazdagépek adatainak és a konfigurációs kiszolgáló elavult bejegyzési adatainak eltávolításához futtassa a következő parancsot a forrásoldali géppel és az elavult bejegyzés IP-címével. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Ha "helyszíni-VM01" nevű forráskiszolgáló-bejegyzést használ a 10.0.0.4 IP-címével, akkor Ehelyett használja a következő parancsot.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Indítsa újra a következő szolgáltatásokat a forrásoldali gépen a konfigurációs kiszolgálóval való újraregisztráláshoz. 
 
    - InMage Scout Application Service
    - Inmage Scout VX-ügynök – Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>A frissítés meghiúsul, ha a szolgáltatások nem állnak le

A konfigurációs kiszolgáló frissítése meghiúsul, ha bizonyos szolgáltatások nem állnak le. 

A probléma azonosításához keresse meg a C:\ProgramData\ASRSetupLogs\ CX_TP_InstallLogFile a konfigurációs kiszolgálón. Ha a következő hibákat keresi, a probléma megoldásához kövesse az alábbi lépéseket: 

```output
2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
2018-06-28 14:28:12.944   Stopping svagents service.
2018-06-28 14:31:32.949   Unable to stop svagents service.
2018-06-28 14:31:32.949   Stopping svagents service.
2018-06-28 14:34:52.960   Unable to stop svagents service.
2018-06-28 14:34:52.960   Stopping svagents service.
2018-06-28 14:38:12.971   Unable to stop svagents service.
2018-06-28 14:38:12.971   Rolling back the install changes.
2018-06-28 14:38:12.971   Upgrade has failed.
```

A hiba megoldása érdekében:

Állítsa le manuálisan a következő szolgáltatásokat:

- cxprocessserver
- Inmage Scout VX-ügynök – Sentinel/Outpost, 
- Microsoft Azure Recovery Services ügynök, 
- Microsoft Azure Site Recovery szolgáltatás, 
- tmansvc
  
A konfigurációs kiszolgáló frissítéséhez futtassa újra az [egyesített telepítőt](service-updates-how-to.md#links-to-currently-supported-update-rollups) .

## <a name="azure-active-directory-application-creation-failure"></a>Azure Active Directory alkalmazás-létrehozási hiba

Nem rendelkezik megfelelő engedélyekkel ahhoz, hogy alkalmazásokat hozzon létre Azure Active Directory (HRE) az [Open Virtualization Application (petesejt)](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
) sablonnal.

A probléma megoldásához jelentkezzen be a Azure Portalba, és tegye a következők egyikét:

- Kérje meg az alkalmazás fejlesztői szerepkörét a HRE-ben. Az alkalmazás fejlesztői szerepkörével kapcsolatos további információkért tekintse meg a [rendszergazdai szerepkörre vonatkozó engedélyeket Azure Active Directory](../active-directory/roles/permissions-reference.md).
- Győződjön meg arról, hogy a **felhasználó létrehozhat** -e alkalmazás-jelölőt *true* értékre a HRE-ben. További információkért lásd [: útmutató: a portál használata az erőforrásokhoz hozzáférő Azure ad-alkalmazás és egyszerű szolgáltatásnév létrehozásához](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>A Process Server/fő cél nem tud kommunikálni a konfigurációs kiszolgálóval 

A Process Server (PS) és a fő cél (MT) modulok nem tudnak kommunikálni a konfigurációs kiszolgálóval (CS), és az állapotuk nem kapcsolódik Azure Portal.

Ezt jellemzően a 443-es port hibája okozza. A következő lépésekkel oldja fel a portot, és engedélyezze újra a kommunikációt a CS használatával.

**Annak ellenőrzése, hogy a MARS-ügynököt a fő célként megadott ügynök hívja-e meg**

Annak ellenőrzéséhez, hogy a fő célként megadott ügynök létrehozhat-e TCP-munkamenetet a konfigurációs kiszolgáló IP-címéhez, keresse meg az alábbihoz hasonló nyomkövetést a fő célkiszolgáló naplófájljaiban:

TCP \<Replace IP with CS IP here> : 52739 \<Replace IP with CS IP here> : 443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT//cserélje le az IP-címet CS IP-címmel

Ha az MT-ügynök naplóiban az alábbihoz hasonló nyomkövetéseket talál, az MT-ügynök a 443-as porton hibát jelez:

```output
#~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
#~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
```
 
Ez a hiba akkor fordulhat elő, ha más alkalmazások is a 443-es portot használják, vagy egy tűzfal a portot blokkoló beállítások miatt.

A hiba megoldása érdekében:

- Ellenőrizze, hogy a tűzfal nem blokkolja-e a 443-es portot.
- Ha a port egy másik, a portot használó alkalmazás miatt nem érhető el, állítsa le és távolítsa el az alkalmazást.
  - Ha az alkalmazás leállítása nem valósítható meg, állítson be egy új, tiszta CS-ot.
- Indítsa újra a konfigurációs kiszolgálót.
- Indítsa újra az IIS szolgáltatást.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Helytelen UUID-bejegyzések miatt a konfigurációs kiszolgáló nincs csatlakoztatva

Ez a hiba akkor fordulhat elő, ha több konfigurációs kiszolgáló (CS) példány UUID-bejegyzés van az adatbázisban. A probléma gyakran előfordul a konfigurációs kiszolgáló virtuális gépe klónozásakor.

A hiba megoldása érdekében:

1. Távolítsa el az elavult/régi CS virtuális gépet a vCenter-ből. További információ:  [kiszolgálók eltávolítása és a védelem letiltása](site-recovery-manage-registration-and-protection.md).
2. Jelentkezzen be a konfigurációs kiszolgáló virtuális gépre, és kapcsolódjon a MySQL svsdb1-adatbázishoz. 
3. Hajtsa végre a következő lekérdezést:

    > [!IMPORTANT]
    >
    > Győződjön meg arról, hogy beírja a klónozott konfigurációs kiszolgáló UUID-adatait vagy a konfigurációs kiszolgáló elavult bejegyzését, amely már nem használatos a virtuális gépek számára. Helytelen UUID megadása esetén a rendszer elveszíti az összes meglévő védett elem információit.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. A portál oldalának frissítése.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>A hitelesítő adatok megadásakor a rendszer végtelen bejelentkezési hurkot hoz létre

Miután megadta a megfelelő felhasználónevet és jelszót a konfigurációs kiszolgáló OVF, az Azure-bejelentkezés továbbra is kéri a megfelelő hitelesítő adatokat.

Ez a probléma akkor fordulhat elő, ha a rendszer ideje helytelen.

A hiba megoldása érdekében:

Állítsa be a megfelelő időt a számítógépen, majd próbálja megismételni a bejelentkezést. 
