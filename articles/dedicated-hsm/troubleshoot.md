---
title: Dedikált HSM – Azure dedikált HSM – problémamegoldás | Microsoft Docs
description: Az Azure dedikált HSM áttekintése az Azure-ban elérhető főbb tárolási képességeket biztosítja az FIPS 140-2 3. szintű minősítéssel
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 0791f2e8d5119c2087286a24cf83b4259ee9e7af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611650"
---
# <a name="troubleshooting-the-azure-dedicated-hsm-service"></a>Az Azure dedikált HSM szolgáltatás hibaelhárítása

Az Azure dedikált HSM szolgáltatásnak két különböző aspektusa van. Először is a HSM-eszközök regisztrációját és üzembe helyezését a mögöttes hálózati összetevőkkel. Másodszor, a HSM-eszközök konfigurációja az adott számítási feladathoz vagy alkalmazáshoz való használatra/integrációra készül. Bár a [Thales Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) -eszközök ugyanazok az Azure-ban, mint amikor közvetlenül a Thales vásárolnak, az a tény, hogy az Azure-beli erőforrások egyedi szempontokat hoznak létre. Ezeket a szempontokat és az esetlegesen felmerülő hibaelhárítási információkat és ajánlott eljárásokat itt dokumentáljuk, hogy magas szintű láthatóságot és a kritikus információkhoz való hozzáférést lehessen biztosítani. Ha a szolgáltatás használatban van, a végleges információk a Microsoft vagy a Thales támogatási kérésein keresztül érhetők el közvetlenül. 

> [!NOTE]
> Meg kell jegyezni, hogy az újonnan telepített HSM-eszközök konfigurációjának megkezdése előtt minden kapcsolódó javítással frissíteni kell azt. A Thales-támogatási portálon egy adott szükséges javítást is [KB0019789](https://supportportal.gemalto.com/csm?id=kb_article_view&sys_kb_id=19a81c8bdb9a1fc8d298728dae96197d&sysparm_article=KB0019789) , amely egy olyan hibával foglalkozik, amelyben a rendszer a rendszerindítás során nem válaszol.

## <a name="hsm-registration"></a>HSM-regisztráció

A dedikált HSM nem szabadon használható, mert hardveres erőforrásokat szolgáltat a felhőben, ezért egy értékes erőforrás, amelynek védelmet kell biztosítania. Ezért a használatával e-mailben használjuk a allowlisiting folyamatot HSMrequest@microsoft.com . 

### <a name="getting-access-to-dedicated-hsm"></a>Hozzáférés a dedikált HSM-hez

Először Kérdezzen rá, hogy milyen használati esetek vannak, amelyeket [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) vagy az [Azure Managed HSM](https://docs.microsoft.com/azure/key-vault/managed-hsm/overview)nem tud kezelni. Ha ezt követően úgy gondolja, hogy csak a dedikált HSM fér hozzá a legfontosabb tárterület-követelményekhez, akkor az e-mail-cím lesz a HSMrequest@microsoft.com hozzáférés Az alkalmazás és a használati esetek körvonalazása, a HSM kívánt régiók és a keresett HSM mennyisége. Ha például Microsoft-képviselővel dolgozik, például egy Account Executive-vagy felhőalapú megoldás-tervezővel, akkor bármely kérelembe belefoglalhatja őket.

## <a name="hsm-provisioning"></a>HSM-kiépítés

A HSM-eszközök Azure-ban való üzembe helyezése a CLI vagy a PowerShell használatával végezhető el. A szolgáltatáshoz való Regisztráláskor meg kell adni egy minta ARM-sablont, és a rendszer segítséget nyújt a kezdeti testreszabáshoz. 

### <a name="hsm-deployment-failure-information"></a>HSM telepítési hiba adatai

A dedikált HSM támogatja a CLI-t és a PowerShellt az üzembe helyezéshez, így a portálon alapuló hibák adatai korlátozottak és nem részletesek A jobb információ a erőforrás-kezelő használatával érhető el. A portál kezdőlapján egy ikon látható, és részletesebb információ érhető el. Ez az információ sokat segít, ha beillesztette az üzembe helyezési problémákkal kapcsolatos támogatási kérelem létrehozásakor.

![Sikertelenség adatai](./media/troubleshoot/failure-information.png)

### <a name="hsm-subnet-delegation"></a>HSM alhálózati delegálás
Az üzembe helyezési hibák számának egyik oka, hogy megfeledkezve a megfelelő delegálás megadásáról az ügyfél által definiált alhálózathoz, amelyen a HSM kiépítve lesz. A delegálás a VNet és az alhálózati előfeltételek részét képezi, és további részleteket az oktatóanyagokban találhat.

![Alhálózat delegálása](./media/troubleshoot/subnet-delegation.png)

### <a name="hsm-deployment-race-condition"></a>HSM üzembe helyezési verseny feltétele

Az üzembe helyezéshez megadott standard ARM-sablon HSM-és ExpressRoute-átjáróval kapcsolatos erőforrásokkal rendelkezik. A hálózati erőforrások függőség a HSM sikeres üzembe helyezéséhez és az időzítéshez elengedhetetlen.  Időnként a függőségi problémákkal és a központi telepítés újrafuttatásával kapcsolatos üzembe helyezési hibák gyakran megoldják a problémát. Ha nem, akkor az erőforrások törlése, majd az újbóli üzembe helyezése gyakran sikeres. Ha ezt megtette, és a probléma továbbra is fennáll, egy támogatási kérést kell létrehoznia a Azure Portal a "az Azure Setup konfigurálása" problémáinak kiválasztásával.

### <a name="hsm-deployment-using-terraform"></a>HSM-telepítés a Terraform használatával

Néhány ügyfél a szolgáltatás regisztrálásakor megadott Terraform helyett Automation-környezetként használta az ARM-sablonokat. A HSM nem telepíthető így, de a függő hálózati erőforrások is. A Terraform egy olyan modult tartalmaz, amely egy minimális ARM-sablont hív meg, amely csak a HSM-telepítést végzi.  Ebben az esetben ügyelni kell arra, hogy a hálózati erőforrások, például a szükséges ExpressRoute-átjárók teljes mértékben telepítve legyenek a HSM telepítése előtt. A következő CLI-parancs használatával tesztelheti a befejezett telepítést, és szükség szerint integrálhatja azokat. Cserélje le a szögletes zárójelet a megadott elnevezési helyére. Meg kell keresnie a "provisioningState sikeres" eredményét.

```azurecli
az resource show --ids /subscriptions/<subid>/resourceGroups/<myresourcegroup>/providers/Microsoft.Network/virtualNetworkGateways/<myergateway>
```

### <a name="deployment-failure-based-on-quota"></a>Telepítési hiba kvóta alapján
A központi telepítések sikertelenek lehetnek, ha a stampnél 2 HSM és régiónként 4 HSM-t használ. Ennek elkerüléséhez győződjön meg arról, hogy a korábban sikertelen központi telepítések erőforrásainak törlése előtt törölte az erőforrásokat. Az erőforrások megtekintéséhez tekintse meg az alábbi "Hogyan lásd a HSM" című szakaszt. Ha úgy véli, hogy meg kell haladnia ezt a kvótát, amely elsősorban védelem alatt áll, akkor kérjük, küldjön e-mailt HSMrequest@microsoft.com a részletekkel.

### <a name="deployment-failure-based-on-capacity"></a>Üzembe helyezési hiba a kapacitás alapján
Ha egy adott bélyegző vagy régió megtelt, azaz szinte minden ingyenes HSM kiépítve, akkor ez üzembe helyezési hibákhoz vezethet. Az egyes bélyegzők esetében 12 HSM érhető el az ügyfelek számára, ami 24 régiónként is jelent. Az egyes bélyegzők 2 tartalékot és 1 tesztelési eszközt is kapnak. Ha úgy gondolja, hogy elérte a határértéket, akkor az HSMrequest@microsoft.com adott bélyegzők kitöltési szintjével kapcsolatos információkat az e-mailben tekintheti meg.

###  <a name="how-do-i-see-hsms-when-provisioned"></a>Hogyan tekintse meg a HSM a kiépítés során?
Mivel a dedikált HSM egy allowlisted szolgáltatás, a Azure Portal rejtett típusnak minősül. A HSM-erőforrások megtekintéséhez a "rejtett típusok megjelenítése" jelölőnégyzetet kell bejelölnie az alább látható módon. A NIC-erőforrás mindig a HSM-et követi, és jó kiindulópont a HSM IP-címének megkereséséhez, mielőtt az SSH-t használja a kapcsolódáshoz.

![Képernyőfelvétel a rejtett típusok megjelenítése](./media/troubleshoot/hsm-provisioned.png)

## <a name="networking-resources"></a>Hálózati erőforrások

A dedikált HSM üzembe helyezése függ a hálózati erőforrásokkal, és bizonyos, a következményekkel kapcsolatos korlátozásokkal.

### <a name="provisioning-expressroute"></a>ExpressRoute kiépítés

A dedikált HSM a ExpressRoute átjárót használja "alagútként" az ügyfél magánhálózati IP-címtartomány és az Azure-adatközpontban található fizikai HSM közötti kommunikációhoz.  Figyelembe kell venni, hogy a vnet egy átjáró korlátozza a helyszíni erőforrásokhoz való kapcsolódást igénylő ügyfeleket a ExpressRoute-on keresztül, majd egy másik vnet kell használnia a kapcsolódáshoz.  

### <a name="hsm-private-ip-address"></a>HSM magánhálózati IP-címe

A dedikált HSM-hez biztosított sablonok azt feltételezik, hogy a HSM IP-címet a rendszer automatikusan egy adott alhálózati tartományból veszi át. Megadhat egy explicit IP-címet a HSM számára az ARM-sablon "NetworkInterfaces" attribútumán keresztül. 

![Képernyőkép, amely a dedikált HSM-sablon mintáját jeleníti meg.](./media/troubleshoot/private-ip-address.png)

## <a name="hsm-initialization"></a>HSM-inicializálás

Az inicializálás egy új HSM használatát készíti elő, vagy egy meglévő HSM-t az újrafelhasználáshoz. A HSM inicializálásának teljesnek kell lennie ahhoz, hogy objektumokat lehessen készíteni vagy tárolni, lehetővé kell tenni az ügyfelek kapcsolódását vagy a titkosítási műveletek végrehajtását.

### <a name="lost-credentials"></a>Elveszett hitelesítő adatok

A rendszerhéj rendszergazdai jelszavának elvesztése a HSM-kulcsok elvesztését eredményezi. A HSM alaphelyzetbe állításához támogatási kérelmet kell készíteni.
A HSM inicializálásakor biztonságosan tárolja a hitelesítő adatokat. A rendszerhéj és a HSM hitelesítő adatait a vállalat szabályzatának megfelelően kell megőrizni.

### <a name="failed-logins"></a>Sikertelen bejelentkezések

A helytelen hitelesítő adatok megadásával a HSM romboló következményei lehetnek. A HSM-szerepkörök alapértelmezett viselkedése a következő:

| Szerepkör | Küszöbérték (a próbálkozások száma) | Túl sok helytelen bejelentkezési kísérlet eredménye | Helyreállítási |
|--|--|--|--|
| HSM ÍGY | 3 |  A HSM nulla (az összes HSM-objektum identitása és az összes partíció el lett mentve)  |  A HSM-t újra kell inicializálni. A tartalmak biztonsági másolatból is visszaállíthatók. | 
| Partíció, így | 10 |  A partíció nullázása. |  A partíciót újra kell inicializálni. A tartalmak biztonsági másolatból is visszaállíthatók. |  
| Naplózás | 10 | Fiókzárolás | 10 perc elteltével automatikusan zárolásra kerül. |  
| Kriptográfiai igazgató | 10 (csökkenthető) | Ha a HSM-házirend 15: engedélyezze, hogy a partíció PIN-kódjának alaphelyzetbe állítása 1 (engedélyezve) értékű legyen, a CO és a CU szerepkörök ki vannak zárva.<br>Ha a HSM-házirend 15: engedélyezze, hogy a partíció PIN-kódjának alaphelyzetbe állítása beállítás értéke 0 (letiltva), a CO-és a CU-szerepkörök véglegesen le lesznek zárva, és a partíció tartalma már nem érhető el. Ez az alapértelmezett beállítás. | A társ-szerepkört fel kell oldani, és a hitelesítő adatokat a partíción keresztül kell visszaállítani `role resetpw -name co` .<br>A partíciót újra kell inicializálni, és a biztonsági mentési eszközről vissza kell állítani a kulcsfontosságú anyagokat. |  

## <a name="hsm-configuration"></a>HSM-konfiguráció 

A következő elemek olyan helyzetek, amikor a konfigurációs hibák vagy gyakoriak, vagy amelyek a felhívásra érdemesek:

### <a name="hsm-documentation-and-software"></a>HSM-dokumentáció és szoftver
A [Thales Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) -eszközök szoftvere és dokumentációja nem érhető el a Microsofttól, és közvetlenül a Thales-ből kell letölteni. A regisztráció a regisztrációs folyamat során kapott Thales ügyfél-azonosító használatával kötelező. A Microsoft által biztosított eszközök rendelkeznek a 7,2-es és a belső vezérlőprogram-verzió 7.0.3. A 2020-es korai Thales dokumentáció nyilvános, és [itt](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm)található.  

### <a name="hsm-networking-configuration"></a>HSM hálózati konfiguráció

Ügyeljen arra, hogy a hálózati adaptert a HSM-en belül konfigurálja.  A HSM a ExpressRoute-átjárón keresztül csatlakozik egy ügyfél magánhálózati IP-címéről, közvetlenül a HSM-hez.  Ez a kommunikációs csatorna csak az ügyfél-kommunikációra szolgál, és a Microsoft nem rendelkezik hozzáféréssel. Ha a HSM úgy van konfigurálva, hogy ez a hálózati elérési út hatással legyen, ez azt jelenti, hogy a HSM-mel folytatott kommunikáció törlődik.  Ebben az esetben az egyetlen lehetőség, ha egy Microsoft-támogatási kérést hoz létre a Azure Portal keresztül az eszköz alaphelyzetbe állításához. Ez az alaphelyzetbe állítási eljárás visszaállítja a HSM-t a kezdeti állapotba, és az összes konfigurációs és kulcsfontosságú anyag elvész.  A konfigurációt újra létre kell hozni, és amikor az eszköz csatlakozik a HA-csoporthoz, a rendszer a fontos anyagokat replikálja.  

### <a name="hsm-device-reboot"></a>HSM-eszköz újraindítása

Néhány konfigurációs módosításhoz a HSM-nek be kell vezetnie vagy újra kell indítania a rendszert. Az Azure-beli HSM Microsoft általi tesztelése azt állapította meg, hogy az újraindítás bizonyos esetekben nem válaszol. Ennek a következménye, hogy egy támogatási kérést kell létrehozni a merevlemez-újraindítás kérésének Azure Portal, amely akár 48 órát is igénybe vehet, figyelembe véve az Azure-adatközpontok manuális folyamatát.  Ennek elkerüléséhez győződjön meg arról, hogy a Thales közvetlenül elérhető az újraindítási javítás. Tekintse meg a [KB0019789](https://supportportal.gemalto.com/csm?sys_kb_id=d66911e2db4ffbc0d298728dae9619b0&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=d568c35bdb9a4850d6b31f3b4b96199e&sysparm_article=KB0019789) -t a Thales Luna 7 HSM 7,2-es verziójában, amely egy olyan hibára vonatkozik, amelyben a rendszer nem válaszol az újraindításkor (Megjegyzés: a letöltéshez regisztrálnia kell a [Thales ügyfél-támogatási portálon](https://supportportal.thalesgroup.com/csm) ).

### <a name="ntls-certificates-out-of-sync"></a>NTLS-tanúsítványok szinkronizálása
Előfordulhat, hogy egy ügyfél elveszti a kapcsolatot egy HSM-kapcsolattal, amikor egy tanúsítvány lejár vagy felül lett írva a konfigurációs frissítésekkel. A tanúsítvány-Exchange-ügyfél konfigurációját újra kell alkalmazni az egyes HSM-sel.
Példa NTLS-naplózásra érvénytelen tanúsítvánnyal:

> NTLS [8508]: info: 0: bejövő kapcsolatfelvételi kérelem...: 192.168.50.2/59415 NTLS [8508]: a SSLAccept hibaüzenete: hiba: 14094418: SSL-rutinok: ssl3_read_bytes: TLSv1 riasztás ismeretlen hitelesítésszolgáltató NTLS [8508]: hiba az SSL-elfogadás során (RC_SSL_ERROR) NTLS [8508]: info: 0xc0000711: nem sikerült biztonságos csatornát létrehozni az ügyféllel: 192.168.50.2/59415: RC_SSL_FAILED_HANDSHAKE NTLS [8508]: info: 0: NTLS ügyfél "ismeretlen állomásnév" a kapcsolat példánya eltávolítva: 192.168.50.2/59415

### <a name="failed-tcp-communication"></a>Sikertelen TCP-kommunikáció

A Luna-ügyfél és a HSM közötti kommunikációhoz legalább 1792-es TCP-portra van szükség. Vegye figyelembe, hogy a környezetben minden hálózati konfiguráció megváltozott.

### <a name="failed-ha-group-member-doesnt-recover"></a>Sikertelen, HA a csoport tagja nem áll helyre

Ha egy sikertelen, HA csoportba tartozó tag nem áll helyre, akkor azt manuálisan kell helyreállítani a Luna-ügyfélről a hagroup helyreállítás parancs használatával.
HA engedélyezni szeretné az automatikus helyreállítást, konfigurálnia kell egy HA-csoport újrapróbálkozási számlálóját. Alapértelmezés szerint a HA-csoport nem próbálkozik egy HA-tag helyreállításával a csoportba.

### <a name="ha-group-doesnt-sync"></a>HA a csoport nem szinkronizál

Abban az esetben, ha a tagok partíciói nem rendelkeznek ugyanazzal a klónozási tartománnyal, a ha szinkronizálási parancs a következőt jeleníti meg: figyelmeztetés: a szinkronizálás sikertelen lehet.  A 0. és 1. bővítőhely tagjai ütköző beállításokkal rendelkeznek a titkos kulcsok klónozásához.
A megfelelő klónozási tartománnyal rendelkező új partíciót fel kell venni a HA csoportba, majd el kell távolítani a helytelenül konfigurált partíciót.

## <a name="hsm-deprovisioning"></a>HSM megszüntetése 

Csak akkor, ha a HSM teljes mértékben elkészült, kiépíthető, majd a Microsoft visszaállítja és visszaadja azt egy ingyenes készletnek. 

### <a name="how-to-delete-an-hsm-resource"></a>HSM-erőforrások törlése

A HSM-hez készült Azure-erőforrás csak akkor törölhető, ha a HSM "zéró" állapotban van.  Ezért az összes fontos anyagot törölni kell, mielőtt erőforrásként törölné. A zeroize leggyorsabb módja, ha a HSM-rendszergazda jelszava 3 alkalommal nem megfelelő (Megjegyzés: Ez a HSM-rendszergazda, és nem a készülék szintjének felügyeletére vonatkozik). A Luna-rendszerhéjnak van egy `hsm -factoryreset` parancsa, amely zeroizes, de csak a soros porton keresztül hajtható végre, és az ügyfeleknek nincs hozzáférésük ehhez.

## <a name="next-steps"></a>Következő lépések

Ez a cikk betekintést nyújt a HSM üzembe helyezési életciklusára, amely problémákba ütközik, vagy hibaelhárítást vagy alapos megfontolást igényelhet. Remélhetőleg ez a cikk segít elkerülni a szükségtelen késéseket és a frusztrációt, és ha releváns kiegészítésekkel vagy módosításokkal rendelkezik, a Microsofttal támogatási kérést emelhet, és tudassa velünk. 
