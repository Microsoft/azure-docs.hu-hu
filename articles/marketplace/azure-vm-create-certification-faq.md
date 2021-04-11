---
title: Virtuális gép (VM) tanúsítványának hibaelhárítása Az Azure Marketplace-en
description: Az Azure Marketplace-en futó virtuális gépek (VM-lemezképek) tesztelésével és hitelesítésével kapcsolatos gyakori problémák elhárítása.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: mathapli
ms.author: mathapli
ms.date: 01/18/2021
ms.openlocfilehash: 7a9fa4a6f44e88caa6f98d5e1c56adaa065fa29a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558708"
---
# <a name="troubleshoot-virtual-machine-certification"></a>Virtuális gépek minősítésének hibáinak megoldása

Amikor közzéteszi a virtuális gép (VM) lemezképét az Azure Marketplace-en, az Azure csapata ellenőrzi, hogy a rendszer rendszerindító, biztonságos és kompatibilis-e az Azure-ban. Ha a virtuális gép rendszerképe nem felel meg a minőségi tesztek valamelyikének, nem kerül közzétételre. Hibaüzenet jelenik meg, amely leírja a problémát.

Ez a cikk a virtuálisgép-rendszerkép közzétételekor, valamint a kapcsolódó megoldásokkal kapcsolatos gyakori hibaüzeneteket ismerteti.

> [!NOTE]
> Ha kérdése van a cikkről vagy a fejlesztésre vonatkozó javaslatokról, forduljon a [partner Center támogatási szolgálatához](https://aka.ms/marketplacepublishersupport).

## <a name="vm-extension-failure"></a>VM-bővítmény hibája

Ellenőrizze, hogy a rendszerkép támogatja-e a virtuálisgép-bővítményeket.

Virtuálisgép-bővítmények engedélyezése:

1. Válassza ki a linuxos virtuális gépet.
1. Lépjen a **diagnosztikai beállítások menüpontra**.
1. Az alapmátrixok engedélyezéséhez frissítse a **Storage-fiókot**.
1. Kattintson a **Mentés** gombra.

   ![Képernyőfelvétel: a vendég szintű figyelés engedélyezése.](./media/create-vm/vm-certification-issues-solutions-1.png)

Annak ellenőrzése, hogy a virtuálisgép-bővítmények megfelelően vannak-e aktiválva:

1. A virtuális gépen válassza a virtuálisgép- **bővítmények** fület, majd ellenőrizze a **Linux diagnosztikai bővítmény** állapotát.
1. Keresse meg a kiépítési állapotot.

   - Ha az állapot kiosztása *sikeres* volt, a bővítmények tesztelési esete lett átadva.  
   - Ha az állapot kiosztása *nem sikerült*, a bővítmények tesztelési esete meghiúsult, és be kell állítania a megerősített jelzőt.

   ![A kiépítés sikerességét bemutató képernyőkép.](./media/create-vm/vm-certification-issues-solutions-2.png)

   Ha a virtuálisgép-bővítmény nem sikerül, tekintse meg a következő témakört: a [Linux diagnosztikai bővítmény használata a metrikák és naplók figyelésére](../virtual-machines/extensions/diagnostics-linux.md) az engedélyezéshez. Ha nem szeretné, hogy a virtuálisgép-bővítmény engedélyezve legyen, forduljon a támogatási csapathoz, és kérje meg, hogy tiltsa le.

## <a name="vm-provisioning-issue"></a>VIRTUÁLIS gépek kiépítési hibája

Győződjön meg arról, hogy a virtuális gép üzembe helyezési folyamatát szigorúan követte az ajánlat elküldése előtt. A virtuális gép kiépítési JSON-formátumának megtekintéséhez lásd: [virtuálisgép-rendszerkép tesztelése](azure-vm-image-test.md).

A kiépítési problémák a következő meghibásodási helyzetekben lehetnek:

|Eset|Hiba|Ok|Megoldás|
|---|---|---|---|
|1|Érvénytelen virtuális merevlemez (VHD)|Ha a VHD-láblécben megadott cookie-érték helytelen, a VHD-fájl érvénytelennek tekintendő.|Hozza létre újra a lemezképet, és küldje el a kérést.|
|2|Érvénytelen blob-típus|A virtuális gép kiépítés meghiúsult, mert a használt blokk egy oldal típusa helyett blob típusú.|Hozza létre újra a lemezképet, és küldje el a kérést.|
|3|Kiépítési időtúllépés vagy nem megfelelően általánosítva|Probléma van a virtuális gépek általánosításával.|Hozza létre újra a rendszerképet az általánosítással, és küldje el a kérelmet.|
|

> [!NOTE]
> A virtuális gépek általánosításával kapcsolatos további információkért lásd:
> - [Linux-dokumentáció](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Windows-dokumentáció](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="vhd-specifications"></a>VHD-specifikációk

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Conectix cookie-k és egyéb VHD-specifikációk

A "conectix" karakterlánc a VHD-specifikáció részét képezi. A fájl létrehozóját azonosító VHD-láblécben 8 bájtos cookie-ként van definiálva. A Microsoft által létrehozott összes VHD-fájl rendelkezik ezzel a cookie-val.

A VHD-formázott Blobok 512 bájtos lábléctel rendelkeznek ebben a formátumban:

|Merevlemez láblécének mezői|Méret (bájt)|
|---|---|
Cookie|8
Funkciók|4
Fájlformátum verziója|4
Adateltolás|8
Időbélyegző|4
Létrehozó alkalmazás|4
Létrehozó verziója|4
Létrehozó gazdagép operációs rendszere|4
Eredeti méret|8
Aktuális méret|8
Lemez geometriája|4
Lemez típusa|4
Ellenőrzőösszeg|4
Egyedi azonosító|16
Mentett állapot|1
Fenntartva|427
|

### <a name="vhd-specifications"></a>VHD-specifikációk

A zökkenőmentes közzétételi élmény biztosítása érdekében győződjön meg arról, hogy a virtuális merevlemez megfelel a következő feltételeknek:

- A cookie tartalmazza a "conectix" karakterláncot.
- A lemez típusa rögzített.
- A VHD virtuális mérete legalább 20 MB.
- A virtuális merevlemez igazítása megtörténik. A virtuális méretnek 1 MB-nál többnek kell lennie.
- A VHD-blob hossza megegyezik a virtuális mérettel és a VHD-lábléc hosszával (512).

Töltse le a [VHD-specifikációt](https://www.microsoft.com/download/details.aspx?id=23850).

## <a name="software-compliance-for-windows"></a>Windows rendszerhez készült szoftverek megfelelősége

Ha a Windows-rendszerkép iránti kérelmet a szoftver megfelelőségi problémája miatt utasították el, lehetséges, hogy létrehozott egy Windows-rendszerképet egy telepített SQL Server példánnyal. Ehelyett az Azure Marketplace-en kell megtennie a megfelelő SQL Server alaprendszerképet.

Ne hozzon létre saját Windows-rendszerképet a SQL Server telepített példányával. Az Azure Marketplace-ről a jóváhagyott SQL Server alaplemezképek (Enterprise/standard/web) használhatók.

Ha a Visual studiót vagy bármely Office-licenccel rendelkező terméket megpróbál telepíteni, az előzetes jóváhagyáshoz forduljon a támogatási csoporthoz.

A jóváhagyott alap kiválasztásával kapcsolatos további információkért lásd: [virtuális gép létrehozása jóváhagyott alapból](azure-vm-create-using-approved-base.md).

## <a name="toolkit-test-case-execution-failed"></a>Az eszközkészlet tesztelési esetének végrehajtása nem sikerült

A Microsoft minősítési eszközkészlet segítséget nyújt a tesztelési esetek futtatásához, és annak ellenőrzéséhez, hogy a VHD vagy a rendszerkép kompatibilis-e az Azure-környezettel.

Töltse le a [Microsoft minősítési eszközkészletet](azure-vm-image-test.md).

### <a name="linux-test-cases"></a>Linux-tesztelési esetek

A következő táblázat felsorolja az eszközkészlet által futtatott Linux-tesztelési eseteket. A teszt érvényesítése a leírásban van megadva.

|Eset|Teszteset|Leírás|
|---|---|---|
|1|Bash-előzmények|A rendszer a virtuális gép rendszerképének létrehozása előtt törli a bash-előzmények fájljait.|
|2|Linux-ügynök verziója|Telepíteni kell az Azure Linux Agent 2.2.41 vagy újabb verzióját.|
|3|Szükséges kernel-paraméterek|Ellenőrzi, hogy a következő kernel paraméterek vannak-e beállítva: <br>konzol = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300 |
|4|Partíció cseréje az operációsrendszer-lemezen|Ellenőrzi, hogy a swap partíciók nincsenek-e létrehozva az operációsrendszer-lemezen.|
|5|Rendszerlemez gyökérkönyvtára|Hozzon létre egyetlen legfelső szintű partíciót az operációsrendszer-lemez számára.|
|6|OpenSSL-verzió|Az OpenSSL verziójának v 0.9.8 vagy újabb verziójúnak kell lennie.|
|7|Python-verzió|A Python 2,6-es vagy újabb verziója kifejezetten ajánlott.|
|8|Ügyfél élettartamának intervalluma|ClientAliveInterval beállítása 180-re. Az alkalmazásra van szükség, amely 30 és 235 között állítható be. Ha engedélyezi az SSH-t a végfelhasználók számára, ezt az értéket a magyarázatnak megfelelően kell beállítani.|
|9|Operációs rendszer architektúrája|Kizárólag a 64 bites operációs rendszerek támogatottak.|
|10|Automatikus frissítés|Meghatározza, hogy engedélyezve van-e a Linux-ügynök automatikus frissítése.|
|

### <a name="common-test-case-errors"></a>Gyakori tesztelési hibák

Tekintse meg az alábbi táblázatot a tesztelési esetek futtatásakor előforduló gyakori hibákról:

| Eset | Teszteset | Hiba | Megoldás |
| --- | --- | --- | --- |
| 1 | Linux-ügynök verziója – tesztelési eset | A Linux-ügynök minimális verziója 2.2.41 vagy újabb. Ezt a követelményt a 2020. május 1. óta kötelező megadni. | Frissítse a Linux-ügynök verzióját. 2,241 vagy újabb verziónak kell lennie. További információért látogasson el a [Linux-ügynök verziójának frissítése oldalra](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support). |
| 2 | Bash-előzmények tesztelési esete | Hiba történik, ha az elküldött képen a bash-előzmények mérete meghaladja az 1 kilobájtot (KB). A méret 1 KB-ra korlátozódik annak biztosítására, hogy a bash History-fájlja ne tartalmazzon potenciálisan bizalmas adatokat. | Oldja fel a virtuális merevlemezt egy másik működő virtuális gépre, és módosítsa a méretet 1 KB vagy annál kisebb értékre. Törölje például az `.bash` Előzmények fájljait. |
| 3 | Szükséges kernel-paraméterek tesztelési esete | Ez a hibaüzenet akkor jelenik meg, ha a nem értékre van `console` állítva `ttyS0` . A következő parancs futtatásával győződjön meg arról, hogy: <br /> `cat /proc/cmdline` | Állítsa be a értéket a értékre `console` `ttyS0` , majd küldje el újra a kérelmet. |
| 4 | ClientAlive intervalluma – tesztelési eset | Ha az eszközkészlet sikertelen eredményt ad a tesztelési esethez, nem megfelelő érték van a következőhöz: `ClientAliveInterval` . | Állítsa az értéket `ClientAliveInterval` 235-nél kisebb vagy egyenlő értékre, majd küldje el újra a kérelmet. |
|

### <a name="windows-test-cases"></a>Windows-tesztelési esetek

A következő táblázat felsorolja az eszközkészlet által futtatott Windows-tesztelési eseteket, valamint a tesztek ellenőrzésének leírását:

|Eset |Tesztelési esetek|Leírás|
|---|---|---|
|1|Operációs rendszer architektúrája|Az Azure csak a 64 bites operációs rendszereket támogatja.|
|2|Felhasználói fióktól való függőség|Az alkalmazás végrehajtása nem függhet a rendszergazdai fióktól.|
|3|Feladatátvevő fürt|A Windows Server feladatátvételi fürtszolgáltatás még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|4|IPV6|Az IPv6 még nem támogatott az Azure-környezetben. Az alkalmazás nem függ ettől a szolgáltatástól.|
|5|DHCP|Dynamic Host Configuration Protocol kiszolgálói szerepkör még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|6|Hyper-V|A Hyper-V kiszolgálói szerepkör még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|7|Távelérés|A távelérés (közvetlen hozzáférés) kiszolgálói szerepkör még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|8|Rights Management szolgáltatások|Rights Management szolgáltatások. A kiszolgálói szerepkör még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|9|Központi Windows-telepítési szolgáltatások|Központi Windows-telepítési szolgáltatások. A kiszolgálói szerepkör még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|10|BitLocker meghajtótitkosítás|A BitLocker meghajtótitkosítás az operációs rendszer merevlemezén nem támogatott, de adatlemezeken is használható.|
|11|Internet Storage-névkiszolgáló|Az Internet Storage-névkiszolgáló szolgáltatás még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|12|Többutas I/O|Többutas I/O. Ez a kiszolgálói funkció még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|13|Hálózati terheléselosztás|Hálózati terheléselosztás. Ez a kiszolgálói funkció még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|14|Peer Name Resolution Protocol|Peer Name Resolution Protocol. Ez a kiszolgálói funkció még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|15|SNMP-szolgáltatások|A Simple Network Management Protocol (SNMP) szolgáltatások szolgáltatás még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|16|Windows Internet Name Service|Windows Internet Name Service. Ez a kiszolgálói funkció még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|17|Vezeték nélküli helyi hálózat szolgáltatás|Vezeték nélküli LAN szolgáltatás. Ez a kiszolgálói funkció még nem támogatott. Az alkalmazás nem függ ettől a szolgáltatástól.|
|

Ha az előző tesztelési esetekkel kapcsolatos hibákkal találkozik, tekintse meg a megoldás táblázatának **Leírás** oszlopát. További információért forduljon a támogatási csoporthoz.

## <a name="data-disk-size-verification"></a>Adatlemez méretének ellenőrzése

Az 1023 gigabájtnál (GB) nagyobb méretű adatlemez-kérelmeket nem lehet jóváhagyni. Ez a szabály a Linux és a Windows rendszerre egyaránt vonatkozik.

Küldje el újra a kérelmet a 1023 GB-nál kisebb vagy azzal egyenlő mérettel.

## <a name="os-disk-size-validation"></a>OPERÁCIÓSRENDSZER-lemez méretének ellenőrzése

Az operációsrendszer-lemez méretére vonatkozó korlátozásokkal kapcsolatban tekintse meg az alábbi szabályokat. Bármely kérelem elküldésekor ellenőrizze, hogy az operációsrendszer-lemez mérete a Linux vagy a Windows korlátozásán belül van-e.

|Operációs rendszer|Ajánlott VHD-méret|
|---|---|
|Linux|1 GB – 1023 GB|
|Windows|30 GB – 250 GB|
|

Mivel a virtuális gépek engedélyezik a hozzáférést az alapul szolgáló operációs rendszerhez, győződjön meg arról, hogy a VHD-méret elég nagy a VHD-hez. A lemezek leállás nélkül nem bővíthetők. A lemez mérete 30 GB és 50 GB között legyen.

|VHD-méret|Tényleges foglalt méret|Megoldás|
|---|---|---|
|>500 tebibájt (TiB)|n.a.|Kivétel jóváhagyásához forduljon a támogatási csoporthoz.|
|250-500 TiB|>200 gibibájtban értendők (GiB) eltérés a blob méretétől|Kivétel jóváhagyásához forduljon a támogatási csoporthoz.|
|

> [!NOTE]
> A nagyobb méretű lemezek nagyobb költségekkel járnak, és a telepítés és a replikálás során is késést okoznak. A késés és a költséghatékonyság miatt a támogatási csoport indoklást kérhet a kivétel jóváhagyásáról.

## <a name="wannacry-patch-verification-test-for-windows"></a>A Windows WannaCry javításának ellenőrzési tesztje

A WannaCry vírussal kapcsolatos potenciális támadás megelőzése érdekében győződjön meg arról, hogy az összes Windows-lemezképre vonatkozó kérelem frissítve lett a legújabb javítással.

A rendszerkép fájljának verziószámát a vagy a rendszerből is ellenőrizheti `C:\windows\system32\drivers\srv.sys` `srv2.sys` .

A következő táblázat a Windows Server minimális javított verzióját tartalmazza:

|Operációs rendszer|Verzió|
|---|---|
|A Windows a 2008 R2-t szolgálja|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|NA|
|

> [!NOTE]
> A Windows Server 2019 nem rendelkezik a kötelező verzióra vonatkozó követelményekkel.

## <a name="sack-vulnerability-patch-verification"></a>A SACK sebezhetőségi javításának ellenőrzése

Linux-rendszerképek beküldésekor a rendszer a kernel-verzióval kapcsolatos problémák miatt elutasítja a kérést.

Frissítse a kernelt jóváhagyott verzióval, majd küldje el újra a kérelmet. A jóváhagyott kernel-verziót az alábbi táblázatban találja. A verziószámnak az itt felsorolt számmal egyenlőnek vagy annál nagyobbnak kell lennie.

Ha a rendszerkép nincs telepítve a következő kernel-verziók egyikével, frissítse a megfelelő javításokkal. Kérje meg a szükséges jóváhagyást a támogatási csapattól a rendszerkép frissítése után a szükséges javításokkal:

- CVE-2019-11477
- CVE-2019-11478
- CVE-2019-11479

|OPERÁCIÓSRENDSZER-család|Verzió|Kernel|
|---|---|---|
|Ubuntu|14,04 LTS|4.4.0 – 151| 
||14,04 LTS|4.15.0-1049- \* -Azure|
||16,04 LTS|4.15.0 – 1049|
||18,04 LTS|4.18.0 – 1023|
||18,04 LTS|5.0.0 – 1025|
||18,10|4.18.0 – 1023|
||19,04|5.0.0-1010|
||19,04|5.3.0 – 1004|
|RHEL és cent operációs rendszer|6,10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7,4|3.10.0-693.50.3|
||7,5|3.10.0-862.34.2|
||7.6|3.10.0-957.21.3|
||7.7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0 – 147|
||"7 – NYERS" (7,6)||
||"7-LVM" (7,6)|3.10.0-957.21.3|
||RHEL – SAP 7,4|TBD|
||RHEL – SAP 7,5|TBD|
|SLES|SLES11SP4 (beleértve az SAP-t)|3.0.101-108.95.2|
||SLES12SP1 az SAP-hoz|3.12.74-60.64.115.1|
||SLES12SP2 az SAP-hoz|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (kernel-Azure)|
||SLES12SP3 az SAP-hoz|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (kernel-Azure)|
||SLES12SP4 az SAP-hoz|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (kernel-Azure)|
||SLES15 az SAP-hoz|4.12.14-5.30.1 (kernel-Azure)|
||SLES15SP1|4.12.14-5.30.1 (kernel-Azure)|
|Oracle|6,10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0 – 7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>A RHCK a fenti RHEL követi|
||7.6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|CoreOS stabil 2079.6.0|4.19.43\*|
||Bétaverziós 2135.3.1|4.19.50\*|
||Alpha 2163.2.1|4.19.50\*|
|Debian|Jessie (biztonság)|3.16.68 – 2|
||Jessie backports|4.9.168 – 1 + deb9u3|
||stretch (biztonság)|4.9.168 – 1 + deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0-18 + deb9u1|
||Buster, SID (stretch backports)|4.19.37 – 5|
|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>A képméretnek megabájtnál többnek kell lennie

Az Azure-ban az összes virtuális merevlemeznek egy 1 megabájtos (MB) többszörös értékre igazított virtuális mérettel kell rendelkeznie. Ha a VHD-fájl nem felel meg az ajánlott virtuális méretnek, akkor előfordulhat, hogy a rendszer elutasítja a kérést.

Ha nyers lemezről VHD-re konvertál, kövesse az útmutatást. Győződjön meg arról, hogy a nyers lemez mérete több, mint 1 MB. További információ: [nem támogatott disztribúciók adatai](../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>Virtuális gép hozzáférése megtagadva

Előfordulhat, hogy a virtuális gépen a tesztelési eset futtatására vonatkozó _megtagadási_ probléma oka a megfelelő jogosultságok hiánya.

Ellenőrizze, hogy engedélyezte-e a megfelelő hozzáférést ahhoz a fiókhoz, amelyen az önteszt esetek futnak. Engedélyezze a hozzáférést a tesztelési esetek futtatásához, ha az nincs engedélyezve. Ha nem szeretné engedélyezni a hozzáférést, megoszthatja az egyéni tesztelési eset eredményeit a támogatási csapattal.

Ha a kérést SSH-letiltott képpel szeretné elküldeni a minősítési folyamathoz:

1. Futtassa az [Azure-beli virtuális gépek legújabb minősítési teszt eszközét](https://aka.ms/AzureCertificationTestTool) a lemezképen.

2. [Támogatási jegy](https://aka.ms/marketplacepublishersupport)előléptetése. Ügyeljen arra, hogy csatolja az eszközkészlet jelentését, és adja meg az ajánlat részleteit:
   - Ajánlat neve
   - Közzétevő neve
   - Csomag azonosítója/SKU és Version

3. Küldje el újra a minősítési kérelmet.

## <a name="download-failure"></a>Letöltési hiba

A következő táblázat tartalmazza azokat a problémákat, amelyek a virtuálisgép-rendszerkép közös hozzáférési aláírással (SAS) való letöltésekor merülnek fel.

|Hiba|Ok|Megoldás|
|---|---|---|
|A blob nem található|Lehet, hogy a VHD-t törölték vagy áthelyezték a megadott helyről.|| 
|BLOB használatban|A virtuális merevlemezt egy másik belső folyamat használja.|A VHD-nek használatban lévő állapotban kell lennie, amikor letölti egy SAS URL-címmel.|
|Érvénytelen SAS URL-cím|A virtuális merevlemezhez tartozó SAS URL-cím helytelen.|Szerezze be a megfelelő SAS URL-címet.|
|Érvénytelen aláírás|A virtuális merevlemezhez tartozó SAS URL-cím helytelen.|Szerezze be a megfelelő SAS URL-címet.|
|HTTP feltételes fejléc|A SAS URL-címe érvénytelen.|Szerezze be a megfelelő SAS URL-címet.|
|Érvénytelen VHD-név|Ellenőrizze, hogy létezik-e speciális karakter (például egy százalék `%` vagy idézőjel `"` ) a VHD-névben.|Nevezze át a VHD-fájlt a speciális karakterek eltávolításával.|
|

## <a name="first-partition-starts-at-1-mb-2048-sectors"></a>Az első partíció 1 MB-ban kezdődik (2048 szektor)

Ha [saját lemezképet](azure-vm-create-using-own-image.md)készít, győződjön meg arról, hogy az operációsrendszer-lemez első 2048 szektora (1 MB) üres. Ellenkező esetben a közzététel sikertelen lesz. Ez a követelmény csak az operációsrendszer-lemezre vonatkozik (nem adatlemezek). Ha [jóváhagyta](azure-vm-create-using-approved-base.md)a rendszerképét, kihagyhatja ezt a követelményt.

### <a name="create-a-1-mb-2048-sectors-each-sector-of-512-bytes-partition-on-an-empty-vhd"></a>Hozzon létre egy 1 MB-ot (2048 szektor, 512 bájtos szektor) partíciót egy üres VHD-ben

Ezek a lépések csak a Linux rendszerre vonatkoznak.

1. Hozzon létre bármilyen linuxos virtuális gépet, például Ubuntu, cent OS vagy más. Töltse ki a kötelező mezőket, és válassza a **Tovább: lemezek >** elemet.

   ![Képernyőfelvétel: a virtuális gép létrehozása lap a "Next: Disks Command" gomb kiemelve.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Hozzon létre egy nem felügyelt lemezt a virtuális géphez.

   Használja az alapértelmezett értékeket, vagy adjon meg bármilyen értéket a mezőkhöz, például a hálózati adapterhez, a NSG és a nyilvános IP-címhez.

   ![Képernyőkép a virtuális gép létrehozása folyamatának "adatlemezek" oldaláról.](./media/create-vm/vm-certification-issues-solutions-16.png)

1. Miután létrehozta a virtuális gépet, válassza a bal oldali ablaktábla **lemezek** elemét.

   ![A virtuális gép lemezeit kiválasztó képernyőkép.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Csatolja a virtuális merevlemezt adatlemezként a virtuális géphez a partíciós tábla létrehozásához.

   1. Válassza a **adatlemez hozzáadása**  >  **meglévő blob** elemet.

      ![Az adatlemez virtuális merevlemezhez való hozzáadását bemutató képernyőkép.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Keresse meg a VHD Storage-fiókját.
   1. Válassza a **tároló** elemet, majd válassza ki a virtuális merevlemezt.
   1. Válassza az **OK** lehetőséget.

      ![Képernyőkép a nem felügyelt lemez csatolása oldalról.](./media/create-vm/vm-certification-issues-solutions-19.png)

      A virtuális merevlemez 0 adatlemezként lesz hozzáadva.

   1. Indítsa újra a virtuális gépet.

1. A virtuális gép újraindítása után a PuTTY vagy egy másik ügyfél használatával jelentkezzen be a virtuális gépre, és futtassa a `sudo  -i` parancsot a gyökérszintű hozzáférés megszerzéséhez.

   ![A PuTTY ügyféloldali parancssori képernyőképe a sudo-i parancsot mutatja.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Hozzon létre egy partíciót a virtuális merevlemezen.

   1. Adja meg a `fdisk /dev/sdb` parancsot.
   1. Ha meg szeretné tekinteni a meglévő partíciós listát a VHD-ből, írja be a következőt: `p` .
   1. Ide `d` kattintva törölheti a VHD-ben elérhető összes meglévő partíciót. Ha nincs rá szükség, kihagyhatja ezt a lépést.

      ![A PuTTY ügyféloldali parancssori képernyőképe a meglévő partíciók törlésére vonatkozó parancsokat mutatja.](./media/create-vm/vm-certification-issues-solutions-21.png)

   1. Adja meg `n` az új partíció létrehozása elemet, és válassza `p` a (elsődleges partíció) lehetőséget.

   1. Adja meg a 2048 értéket az _első szektor_ értékeként. Az _utolsó szektort_ alapértelmezett értékként is elhagyhatja.

      >[!IMPORTANT]
      >A rendszer a meglévő összes adat törlését 2048 szektorban (512 bájtos szektorban) törli. Új partíció létrehozása előtt készítsen biztonsági másolatot a virtuális merevlemezről.

      ![A PuTTY ügyféloldali parancssori képernyőképe a törölt adatokat tartalmazó parancsokat és kimenetet mutatja.](./media/create-vm/vm-certification-issues-solutions-22.png)

   1. `w`A partíció létrehozásának megerősítéséhez írja be a következőt:.

      ![A PuTTY ügyfél parancssori képernyőképe a partíció létrehozásához szükséges parancsokat mutatja.](./media/create-vm/vm-certification-issues-solutions-23.png)

   1. A partíciós táblát ellenőrizheti a parancs futtatásával `n fdisk /dev/sdb` és a beírásával `p` . Látni fogja, hogy a partíció 2048 eltolási értékkel lett létrehozva.

      ![A PuTTY ügyféloldali parancssori képernyőképe az 2048 eltolás létrehozásához szükséges parancsokat mutatja.](./media/create-vm/vm-certification-issues-solutions-24.png)

1. Válassza le a virtuális MEREVLEMEZt a virtuális gépről, és törölje a virtuális GÉPET.

### <a name="create-a-1-mb-2048-sectors-each-sector-of-512-bytes-partition-by-moving-existing-data-on-vhd"></a>Hozzon létre egy 1 MB-ot (2048 szektort, egyes 512 bájtos szektorokat) a meglévő adat virtuális merevlemezen való áthelyezésével

Ezek a lépések csak a Linux rendszerre vonatkoznak.

1. Hozzon létre bármilyen linuxos virtuális gépet, például Ubuntu, cent OS vagy más. Töltse ki a kötelező mezőket, és válassza a **Tovább: lemezek >** elemet.

   ![Képernyőfelvétel: a virtuális gép létrehozása lap a "Next: Disks Command" gomb kiemelve.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Hozzon létre egy nem felügyelt lemezt a virtuális géphez.

   ![Képernyőkép a virtuális gép létrehozása folyamatának "adatlemezek" oldaláról.](./media/create-vm/vm-certification-issues-solutions-16.png)

   Használja az alapértelmezett értékeket, vagy adjon meg bármilyen értéket a mezőkhöz, például a hálózati adapterhez, a NSG és a nyilvános IP-címhez.

1. Miután létrehozta a virtuális gépet, válassza a bal oldali ablaktábla **lemezek** elemét.

   ![A virtuális gép lemezeit kiválasztó képernyőkép.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Csatolja a virtuális merevlemezt adatlemezként a virtuális géphez a partíciós tábla létrehozásához.

   1. Csatolja a virtuális merevlemezt adatlemezként a virtuális géphez a partíciós tábla létrehozásához.

   1. Válassza a **adatlemez hozzáadása**  >  **meglévő blob** elemet.

      ![Az adatlemez virtuális merevlemezhez való hozzáadását bemutató képernyőkép.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Keresse meg a VHD Storage-fiókját.
   1. Válassza a **tároló** elemet, majd válassza ki a virtuális merevlemezt.
   1. Válassza az **OK** lehetőséget.

      ![Képernyőkép a nem felügyelt lemez csatolása oldalról.](./media/create-vm/vm-certification-issues-solutions-19.png)

      A virtuális merevlemez 0 adatlemezként lesz hozzáadva.

   1. Indítsa újra a virtuális gépet.

1. Jelentkezzen be a virtuális gépre a PuTTY vagy egy másik ügyfél használatával, és futtassa a `sudo  -i` parancsot a gyökérszintű hozzáférés megszerzéséhez.

   ![A PuTTY ügyféloldali parancssori képernyőképe a bejelentkezést és a sudo-i parancsot mutatja be.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Futtassa a következő parancsot: `echo '+1M,' | sfdisk --move-data /dev/sdc -N 1`.

   ![A PuTTY ügyféloldali parancssori képernyőképe a parancsok végrehajtását mutatja.](./media/create-vm/vm-certification-issues-solutions-25.png)

   >[!NOTE]
   >A parancs végrehajtása hosszabb időt is igénybe vehet, mivel a lemez méretétől függ.

1. Válassza le a virtuális MEREVLEMEZt a virtuális gépről, és törölje a virtuális GÉPET.


## <a name="default-credentials"></a>Alapértelmezett hitelesítő adatok

Soha ne küldjön alapértelmezett hitelesítő adatokat a beküldött VHD-vel. Az alapértelmezett hitelesítő adatok hozzáadásával a VHD-k sebezhetővé teszik a biztonsági fenyegetéseket. Ehelyett hozzon létre saját hitelesítő adatokat a virtuális merevlemez elküldésekor.
  
## <a name="datadisk-mapped-incorrectly"></a>Helytelenül megfeleltetett adatlemez

Egy leképezési probléma akkor fordulhat elő, ha a kérést több adatlemez is elküldi, amelyek nincsenek sorban. Például a három adatlemez számozási sorrendjének *0, 1, 2* értéknek kell lennie. Minden más rendelés leképezési problémaként van kezelve.

Küldje el újra a kérést az adatlemezek megfelelő előkészítésével.

## <a name="incorrect-os-mapping"></a>Helytelen operációs rendszer-hozzárendelés

Rendszerkép létrehozásakor előfordulhat, hogy a rendszer leképezi vagy hozzárendelte a helytelen operációsrendszer-címkét. Ha például az operációs rendszer neve részeként kiválasztja a **Windowst** a rendszerkép létrehozása közben, az operációsrendszer-lemezt csak Windows rendszerre kell telepíteni. Ugyanez a követelmény a Linux rendszeren is érvényes.

## <a name="vm-not-generalized"></a>Nem általánosított virtuális gép

Ha az Azure Marketplace-ről származó összes lemezképet újra fel kell használni, általánosítva kell lennie az operációs rendszer virtuális merevlemezének.

- **Linux rendszeren** a következő folyamat általánosítja a Linux rendszerű virtuális gépet, és újratelepíti külön virtuális gépre.

  Az SSH ablakban adja meg a következő parancsot: `sudo waagent -deprovision+user` .

- **Windows** esetén a használatával általánosíthatja a Windows-lemezképeket `sysreptool` .

  További információ az `sysreptool` eszközről: a [rendszer-előkészítés (Sysprep) áttekintése](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>Adatlemez hibák

Az adatlemezzel kapcsolatos hibák megoldásához használja az alábbi táblázatot:

|Hiba|Ok|Megoldás|
|---|---|---|
|`DataDisk- InvalidUrl:`|Ez a hiba akkor fordulhat elő, ha az ajánlat elküldésekor érvénytelen logikai egység (LUN) van.|Ellenőrizze, hogy az adatlemez LUN-számának sorszáma a partner Centerben van-e.|
|`DataDisk- NotFound:`|Ez a hiba akkor fordulhat elő, ha egy adatlemez nem a megadott SAS URL-címen található.|Ellenőrizze, hogy az adatlemez a megadott SAS URL-címen található-e.|

## <a name="remote-access-issue"></a>Távelérési probléma

Ez a hibaüzenet akkor jelenik meg, ha a RDP protokoll (RDP) beállítás nincs engedélyezve a Windows-rendszerképhez.

Az RDP-hozzáférés engedélyezése a Windows-rendszerképekhez a beküldése előtt.

## <a name="bash-history-failed"></a>A bash előzményei nem sikerültek

Ez a hiba akkor jelenik meg, ha az elküldött rendszerképben lévő bash-előzmények mérete meghaladja az 1 kilobájtot (KB). A méret 1 KB-ra van korlátozva, hogy a fájl ne legyen potenciálisan bizalmas adatokat tartalmazni.

A bash-előzmények törlése:

1. Telepítse a virtuális gépet, és válassza a **Futtatás parancs** lehetőséget a Azure Portal.

   ![Képernyőkép a Azure Portalról a bal oldali ablaktábla "Futtatás parancs" lehetőségével.](./media/create-vm/vm-certification-issues-solutions-3.png)

1. Válassza az első beállítás **RunShellScript** lehetőséget, majd futtassa a parancsot: `cat /dev/null > ~/.bash_history && history -c` .

   ![Képernyőkép a Azure Portalon futó parancsfájl futtatása oldalról.](./media/create-vm/vm-certification-issues-solutions-4.png)

1. A parancs sikeres futtatása után indítsa újra a virtuális gépet.

1. Általánosítsa a virtuális gépet, végezze el a rendszerkép virtuális merevlemezét, és állítsa le a virtuális gépet.

1. Küldje el újra az általánosított képet.

## <a name="request-an-exception-on-vm-images-for-select-tests"></a>Kivétel kérése virtuálisgép-lemezképekhez a Select tesztek esetében

A kiadók kivételeket igényelhetnek a virtuális gépek minősítése során végzett tesztek során. A kivételek olyan ritka esetekben vannak megadva, amikor a közzétevő igazolja a kérés támogatását. A minősítési csapat fenntartja a jogot, hogy bármikor megtagadja vagy jóváhagyja a kivételeket.

Ez a szakasz azokat a általános forgatókönyveket ismerteti, amelyekben a kiadók kivételt igényelnek, és hogyan kérhetnek egyet.

### <a name="scenarios-for-exception"></a>Kivételek forgatókönyvei

A kiadók általában a következő esetekben kérnek kivételeket:

- **Kivétel egy vagy több tesztelési esethez**. Forduljon a [partner Center támogatási szolgálatához](https://aka.ms/marketplacepublishersupport) , hogy kivételeket igényeljen a tesztelési esetekhez.

- **Zárolt virtuális gépek/nincs gyökérszintű hozzáférés**. Néhány közzétevőnek van olyan forgatókönyve, amelyben a virtuális gépeket zárolni kell, mert olyan szoftverekkel rendelkeznek, mint például a virtuális gépre telepített tűzfalak. Ebben az esetben töltse le a [Certified test eszközt](https://aka.ms/AzureCertificationTestTool) , és küldje el a jelentést a [partner Center támogatási szolgálatának](https://aka.ms/marketplacepublishersupport).

- **Egyéni sablonok**. Egyes közzétevők olyan virtuálisgép-rendszerképeket tesznek közzé, amelyekhez egyéni Azure Resource Manager (ARM) sablon szükséges a virtuális gépek telepítéséhez. Ebben az esetben küldje el az egyéni sablonokat a [partner Center támogatásában](https://aka.ms/marketplacepublishersupport) , hogy a minősítési csapat felhasználja az ellenőrzést.

### <a name="information-to-provide-for-exception-scenarios"></a>Kivételi forgatókönyvek megadására szolgáló információk

Vegye fel a kapcsolatot a [partner Center támogatási szolgálatával](https://aka.ms/marketplacepublishersupport) , hogy kivételt igényeljen az egyik forgatókönyv esetén, és tartalmazza a következő információkat:

- **KÖZZÉTEVŐ azonosítója**. Írja be a partner központi portál közzétevő-AZONOSÍTÓját.
- **Ajánlat azonosítója/neve**. Adja meg az ajánlat AZONOSÍTÓját vagy nevét.
- **SKU/csomag azonosítója**. Írja be a virtuális gép ajánlati tervének AZONOSÍTÓját vagy SKU-át.
- **Verzió** Adja meg a virtuálisgép-ajánlat azon verzióját, amelyhez kivételt kell megadni.
- **Kivétel típusa** Válasszon a tesztek, a zárolt virtuális gép vagy az egyéni sablonok közül.
- **A kérelem oka**. Adja meg a kivétel okát, valamint a tesztelési kivételekkel kapcsolatos információkat.
- **Idősor**. Adja meg a kivétel befejezési dátumát.
- **Melléklet**. Csatolt fontos igazoló dokumentumok:

  - Zárolt virtuális gépek esetén csatolja a teszt jelentést.
  - Egyéni sablonok esetén adja meg az egyéni ARM-sablont mellékletként.

  Ha nem sikerül belefoglalni ezeket a mellékleteket, a rendszer megtagadja a kérést.

## <a name="address-a-vulnerability-or-an-exploit-in-a-vm-offer"></a>A virtuálisgép-ajánlat biztonsági rése vagy kiaknázása

Ez a szakasz azt ismerteti, hogyan lehet új virtuálisgép-rendszerképeket megadni, ha a biztonsági rést vagy a biztonsági rést felderítik az egyik virtuálisgép-lemezképpel. Ez csak az Azure Marketplace-en közzétett Azure-beli virtuális gépekre vonatkozik.

> [!NOTE]
> Nem távolíthatja el az utolsó virtuálisgép-rendszerképet egy csomagból, vagy leállíthatja az ajánlat utolsó csomagjának értékesítését.

Hajtsa végre az alábbi műveletek egyikét:

- Ha a sebezhető virtuálisgép-rendszerkép cseréjéhez új virtuálisgép-rendszerkép tartozik, tekintse meg [a rögzített virtuálisgép-rendszerkép megadása](#provide-a-fixed-vm-image)című témakört.
- Ha nem rendelkezik új virtuálisgép-lemezképpel a csomagban lévő egyetlen virtuálisgép-rendszerkép lecseréléséhez, vagy ha elkészült a csomaggal, [állítsa le a csomag értékesítését](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).
- Ha nem szeretné lecserélni az ajánlat egyetlen virtuálisgép-rendszerképét, javasoljuk, hogy [állítsa le az ajánlat értékesítését](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="provide-a-fixed-vm-image"></a>Rögzített virtuálisgép-rendszerkép megadása

Rögzített virtuálisgép-rendszerkép megadásával olyan virtuálisgép-rendszerképeket cserélhet le, amelyek biztonsági rése vagy kihasználása a következő:

1. Adjon meg egy új virtuálisgép-rendszerképet a biztonsági sebezhetőség vagy a biztonsági rés kihasználásához.
1. Távolítsa el a virtuálisgép-rendszerképet a biztonsági sebezhetőséggel vagy a biztonsági rés kihasználásával.
1. Az ajánlat ismételt közzététele.

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>Adjon meg egy új virtuálisgép-rendszerképet a biztonsági rések vagy a biztonsági rés megoldásához

A lépések elvégzéséhez készítse elő a technikai eszközöket a hozzáadni kívánt virtuálisgép-rendszerképhez. További információkért lásd: [virtuális gép létrehozása jóváhagyott alap használatával](azure-vm-create-using-approved-base.md) vagy [virtuális gép létrehozása saját rendszerkép használatával](azure-vm-create-using-own-image.md) , valamint [sas URI létrehozása a virtuálisgép-rendszerképhez](azure-vm-get-sas-uri.md).

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
1. A bal oldali panelen válassza a **kereskedelmi piactér**  >  **áttekintése** elemet.
1. Az **ajánlat-alias** oszlopban válassza ki az ajánlatot.
1. A **terv áttekintése** lap **név** oszlopában válassza ki a megfelelő csomagot.
1. A **technikai konfiguráció** lap virtuálisgép- **rendszerképek** területén válassza a **+ virtuálisgép-rendszerkép hozzáadása** elemet.

   > [!NOTE]
   > Egyszerre csak egy virtuálisgép-rendszerképet adhat hozzá. Több virtuálisgép-rendszerkép hozzáadásához tegye közzé az elsőt, mielőtt hozzáadja a következő virtuálisgép-rendszerképet.

1. A megjelenő mezőkben adja meg a lemez új verzióját és a virtuális gép rendszerképét.
1. Válassza a **Piszkozat mentése** lehetőséget.

Ezután távolítsa el a virtuálisgép-rendszerképet a biztonsági sebezhetőséggel.

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>Távolítsa el a virtuálisgép-rendszerképet a biztonsági sebezhetőséggel vagy a kiaknázással

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali panelen válassza a **kereskedelmi piactér**  >  **áttekintése** elemet.
3. Az **ajánlat-alias** oszlopban válassza ki az ajánlatot.
4. A **terv áttekintése** lap **név** oszlopában válassza ki a megfelelő csomagot.
5. A **technikai konfiguráció** lap virtuálisgép- **lemezképek** területén, az eltávolítani kívánt virtuálisgép-rendszerkép mellett válassza a virtuálisgép- **lemezkép eltávolítása** lehetőséget.
6. A párbeszédpanelen válassza a **Folytatás** lehetőséget.
7. Válassza a **Piszkozat mentése** lehetőséget.

Ezután tegye közzé újra az ajánlatot.

#### <a name="republish-the-offer"></a>Az ajánlat ismételt közzététele

1. Válassza **a felülvizsgálat és közzététel** lehetőséget.
2. Ha bármilyen információt meg kell adnia a minősítési csapatnak, adja hozzá a **Megjegyzések a minősítéshez** mezőben.
3. Válassza a **Közzététel** lehetőséget.

A közzétételi folyamat befejezéséhez tekintse meg az [ajánlatok áttekintése és közzététele](review-publish-offer.md)című témakört.

### <a name="vm-images-with-limited-access-or-requiring-custom-templates"></a>Korlátozott hozzáférésű vagy egyéni sablonokat igénylő virtuálisgép-rendszerképek

#### <a name="locked-down-or-ssh-disabled-offer"></a>Zárolt (vagy) SSH-letiltott ajánlat

  Az SSH-val letiltott (Linux esetében) vagy RDP-letiltott (Windows rendszeren) típusú, zárolt virtuális gépekként közzétett lemezképek. Vannak olyan speciális üzleti forgatókönyvek, amelyek miatt a közzétevők csak korlátozott hozzáférést engedélyeznek a nem/a néhány felhasználó számára. Az ellenőrzési ellenőrzések során előfordulhat, hogy a zárolt virtuális gépek nem teszik lehetővé bizonyos hitelesítési parancsok végrehajtását.


#### <a name="custom-templates"></a>Egyéni sablonok

   Általánosságban elmondható, hogy az egyes VM-ajánlatok alatt közzétett összes lemezkép a telepítéshez a standard ARM-sablont követi. Vannak azonban olyan helyzetek, amikor a közzétevőnek testre kell szabnia a virtuális gépek üzembe helyezése során (például több hálózati adaptert kell konfigurálni).
    
   Az alábbi forgatókönyvek (nem teljes) alapján a kiadó egyéni sablonokat használ a virtuális gép üzembe helyezéséhez:

   * A virtuális géphez további hálózati alhálózatok szükségesek.
   * Az ARM-sablonba beszúrandó további metaadatok.
   * Az ARM-sablon végrehajtásának előfeltételeit képező parancsok.

### <a name="vm-extensions"></a>Virtuálisgép-bővítmények   

   Az Azure virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Ha egy virtuális gépen például szoftver telepítésére, vírusvédelemre vagy egy szkript futtatására van szükség, erre felhasználható egy virtuálisgép-bővítmény. 

   A linuxos virtuálisgép-bővítmények érvényesítéséhez a következők szükségesek ahhoz, hogy a rendszerkép része legyen:
* Az Azure Linux-ügynök nagyobb 2.2.41
* Python-verzió 2,8 felett 


További információért látogasson el a [VM-bővítményre](../virtual-machines/extensions/diagnostics-linux.md).
     
## <a name="next-steps"></a>Következő lépések

- [Virtuálisgép-ajánlat tulajdonságainak konfigurálása](azure-vm-create-properties.md)
- [Aktív Piactéri előnyök](partner-center-portal/marketplace-rewards.md)
- Ha kérdése vagy visszajelzése van a fejlesztéssel kapcsolatban, forduljon a [partner Center ügyfélszolgálatához](https://aka.ms/marketplacepublishersupport).
