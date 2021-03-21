---
title: A privát kapcsolatok konfigurációs problémáinak diagnosztizálása az Azure Key Vaultban
description: A Key Vault és a konfigurációval kapcsolatos gyakori privát hivatkozások megoldása
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 03abe4e4e098d46060e33ba114872905e54a443f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96317061"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>A privát kapcsolatok konfigurációs problémáinak diagnosztizálása az Azure Key Vaultban

## <a name="introduction"></a>Bevezetés

Ez a cikk segítséget nyújt a felhasználóknak a Key Vault és a privát hivatkozások szolgáltatással kapcsolatos problémák diagnosztizálásában és javításában. Ez az útmutató segítséget nyújt a konfigurációs szempontokról, például a privát hivatkozások első alkalommal történő beszerzéséről, vagy egy olyan helyzet kijavításáról, amelyben a magánjellegű hivatkozások valamilyen változás miatt leálltak.

Ha még nem ismeri ezt a funkciót, tekintse meg [a Key Vault integrálása az Azure privát kapcsolattal](private-link-service.md)című témakört.

### <a name="problems-covered-by-this-article"></a>A cikk által tárgyalt problémák

- A DNS-lekérdezések továbbra is egy nyilvános IP-címet adnak vissza a kulcstartó számára, nem pedig egy magánhálózati IP-címet, amelyet a privát hivatkozások funkció használatával elvár.
- Egy, a privát hivatkozást használó ügyfél által kezdeményezett összes kérelem időtúllépésekkel vagy hálózati hibákkal meghiúsul, és a probléma nem időszakos.
- A Key Vault privát IP-címmel rendelkezik, de a kérések továbbra is `403` választ kapnak a `ForbiddenByFirewall` belső hibakódra.
- Privát hivatkozásokat használ, de a Key Vault továbbra is fogadja a nyilvános internetről érkező kéréseket.
- A Key Vault két privát végponttal rendelkezik. Az egyiket használó kérelmek megfelelően működnek, de a másikat használó kérések sikertelenek lesznek.
- Rendelkezik egy másik előfizetéssel, kulcstartóval vagy virtuális hálózattal, amely privát hivatkozásokat használ. Új, hasonló üzembe helyezést szeretne végezni, de nem tud saját hivatkozásokat kapni.

### <a name="problems-not-covered-by-this-article"></a>A cikkben nem szereplő problémák

- Átmeneti kapcsolati probléma van. Egy adott ügyfélnél néhány kérelem működik, és néhány nem működik. *Az időszakos problémákat általában nem a privát hivatkozások konfigurációjában lévő probléma okozza; a hálózat vagy az ügyfél túlterheltségének jele.*
- Olyan Azure-terméket használ, amely támogatja a BYOK (Bring Your Own Key), a CMK (ügyfél által felügyelt kulcsokat), vagy a Key vaultban tárolt titkos kulcsokhoz való hozzáférést. Ha engedélyezi a tűzfalat a Key Vault-beállításokban, a termék nem fér hozzá a kulcstartóhoz. *Tekintse meg a termékre vonatkozó dokumentációt. Győződjön meg arról, hogy kifejezetten kijelenti, hogy a kulcstartók támogatják a tűzfalat. Szükség esetén forduljon az adott termék támogatásához.*

### <a name="how-to-read-this-article"></a>A cikk elolvasása

Ha még nem ismeri a privát hivatkozásokat, vagy egy összetett telepítést értékel, javasoljuk, hogy olvassa el a teljes cikket. Ellenkező esetben nyugodtan kiválaszthatja azt a szakaszt, amely több értelmet jelent a problémával kapcsolatban.

Lássunk is hozzá!

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. Győződjön meg arról, hogy az Ügyfélkapcsolat tulajdonosa

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>Ellenőrizze, hogy az ügyfél a virtuális hálózaton fut-e

Ez az útmutató segítséget nyújt a Key vaulthoz tartozó, az alkalmazás kódjából származó kapcsolatok kijavításához. Ilyenek például az Azure Virtual Machines, az Azure Service Fabric-fürtök, a Azure App Service, az Azure Kubernetes szolgáltatás (ak) és más hasonló alkalmazások. Ez az útmutató arra az esetre is vonatkozik, ha a Azure Portal webbase felhasználói felületén végez hozzáférést, ahol a böngésző közvetlenül fér hozzá a kulcstartóhoz.

A magánhálózati hivatkozások definíciója alapján az alkalmazásnak, a parancsfájlnak vagy a portálnak a számítógép, a fürt vagy a környezethez csatlakoztatott számítógépen kell futnia ahhoz a Virtual Networkhoz, ahol a [magánhálózati végpont erőforrása](../../private-link/private-endpoint-overview.md) telepítve volt.

Ha az alkalmazás, a parancsfájl vagy a portál egy tetszőleges internetkapcsolattal rendelkező hálózaton fut, ez az útmutató nem alkalmazható, és valószínűleg privát hivatkozások nem használhatók. Ez a korlátozás a Azure Cloud Shell futtatott parancsokra is vonatkozik, mivel azok egy távoli Azure-gépen futnak, a felhasználói böngésző helyett igény szerint.

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>Ha felügyelt megoldást használ, tekintse meg az adott dokumentációt

Ez az útmutató nem alkalmazható a Microsoft által felügyelt megoldásokra, ahol a Key vaultot egy olyan Azure-termék éri el, amely az ügyfél Virtual Networktól függetlenül létezik. Ilyen forgatókönyvek például az Azure Storage vagy az Azure SQL az inaktív titkosításhoz konfigurálva, az Azure Event hub az adatokat az ügyfél által biztosított kulcsokkal titkosítja, Azure Data Factory a Key vaultban tárolt hitelesítő adatok elérését, az Azure-folyamatokat, amelyekkel a Key vaultból lekérdezi a titkos kulcsokat, valamint más hasonló forgatókönyveket Ezekben az esetekben *ellenőriznie kell, hogy a termék támogatja-e a kulcstárolókat a tűzfalon engedélyezve*. Ez a támogatás általában a Key Vault tűzfal [megbízható szolgáltatások](overview-vnet-service-endpoints.md#trusted-services) szolgáltatásával történik. Számos termék azonban nem szerepel a megbízható szolgáltatások listáján, számos okból. Ebben az esetben elérheti a termékspecifikus támogatást.

Néhány Azure-termék támogatja a *vnet injekció* fogalmát. Egyszerűen fogalmazva a termék egy hálózati eszközt helyez el az ügyfél Virtual Networkba, amely lehetővé teszi a kérelmek küldését úgy, mintha a Virtual Network üzembe helyezte. Jelentős példa [Azure Databricks](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Az ehhez hasonló termékek a privát hivatkozások használatával kérhetnek a Key vaultba, és ez a hibaelhárítási útmutató segítséget nyújthat.

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. Ellenőrizze, hogy a kapcsolatok jóváhagyva és sikeresek-e

A következő lépésekkel ellenőrizheti, hogy a magánhálózati végponti kapcsolatok jóváhagyva és sikeresek-e:

1. Nyissa meg a Azure Portal, és nyissa meg a Key Vault-erőforrást.
2. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget.
3. Kattintson a **privát végpont kapcsolatai** lapra. Ekkor megjelenik az összes privát végponti kapcsolat és a hozzájuk tartozó állapotok. Ha nincsenek kapcsolatok, vagy ha a Virtual Network kapcsolata hiányzik, létre kell hoznia egy új privát végpontot. Ezt később is tárgyaljuk.
4. Továbbra is a **privát végponti kapcsolatok** esetében keresse meg azt, amelyet Ön diagnosztizál, és ellenőrizze, hogy a "kapcsolat állapota" **jóváhagyásra** került-e, és hogy a "kiépítési állapot" **sikeres** volt-e.
    - Ha a kapcsolódás "függőben" állapotú, akkor lehet, hogy csak jóváhagyja.
    - Ha a "visszautasítva", a "sikertelen", a "hiba", a "leválasztott" vagy más állapotú kapcsolat nem érvényes, akkor minden esetben létre kell hoznia egy új privát végpont-erőforrást.

Érdemes törölni a nem hatékony kapcsolatokat, hogy a dolgok tisztán maradjanak.

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. Ellenőrizze, hogy a Key Vault-tűzfal megfelelően van-e konfigurálva

>[!IMPORTANT]
> A tűzfalbeállítások módosításával eltávolíthat olyan legitim ügyfelek hozzáférését, amelyek még nem használnak privát hivatkozásokat. Győződjön meg arról, hogy tisztában van a tűzfal konfigurációjának egyes változásainak következményeivel.

Fontos szempont, hogy a privát hivatkozások *funkció csak olyan Virtual Network biztosít hozzáférést* a kulcstartóhoz, amely az adatkiszűrése megakadályozása érdekében be van zárva. Nem *távolítja el* a meglévő hozzáférést. Ahhoz, hogy hatékonyan le lehessen tiltani a hozzáférést a nyilvános internetről, explicit módon engedélyeznie kell a Key Vault-tűzfalat:

1. Nyissa meg a Azure Portal, és nyissa meg a Key Vault-erőforrást.
2. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget.
3. Győződjön meg arról, hogy a **tűzfalak és a virtuális hálózatok** lap felül van jelölve.
4. Győződjön meg arról, hogy a **privát végpont és a kiválasztott hálózatok** lehetőség van kiválasztva. Ha az **összes hálózat** lehetőséget választja, azzal megmagyarázza, hogy a külső ügyfelek miért is képesek hozzáférni a kulcstartóhoz.

A következő utasítások a tűzfalbeállítások esetében is érvényesek:

- A Private Links szolgáltatáshoz nem szükséges "Virtual Network" érték megadása a Key Vault-tűzfal beállításaiban. A Key Vault magánhálózati IP-címét (lásd a következő szakaszt) használó összes kérésnek működnie kell, még akkor is, ha nincs virtuális hálózat megadva a Key Vault tűzfal beállításai között.
- A Private Links szolgáltatáshoz nincs szükség IP-cím megadására a Key Vault-tűzfal beállításaiban. A Key Vault magánhálózati IP-címét használó összes kérelemnek újra működnie kell, még akkor is, ha nincs megadva IP-cím a tűzfal beállításai között.

Ha privát hivatkozásokat használ, a Key Vault-tűzfalon a virtuális hálózat vagy az IP-cím megadásának egyetlen indítéka a következő:

- Van egy hibrid rendszer, ahol egyes ügyfelek privát hivatkozásokat használnak, néhány pedig szolgáltatási végpontokat használnak, némelyikük nyilvános IP-címet használ.
- Privát hivatkozásokra vált. Ebben az esetben, ha az összes ügyfél privát hivatkozásokat használ, akkor a Key Vault tűzfal beállításaiból távolítsa el a virtuális hálózatokat és az IP-címeket.

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. Győződjön meg arról, hogy a Key Vault magánhálózati IP-címmel rendelkezik

### <a name="difference-between-private-and-public-ip-addresses"></a>A magán-és a nyilvános IP-címek közötti különbség

A magánhálózati IP-cím mindig a következő formátumok egyike:

- 10. x. x. x: példák: `10.1.2.3` , `10.56.34.12` .
- 172.16. x. x – 172.32. x. x: példák: `172.20.1.1` , `172.31.67.89` .
- 192.168. x. x: példák: `192.168.0.1` , `192.168.100.7`

Bizonyos IP-címek és tartományok le vannak foglalva:

- 224. x. x. x: csoportos küldés
- 255.255.255.255: szórás
- 127. x. x. x: visszacsatolás
- 169.254. x. x: kapcsolat – helyi
- 168.63.129.16: belső DNS

Minden más IP-cím nyilvános.

Amikor megkeresi a portált, vagy futtat egy olyan parancsot, amely megjeleníti az IP-címet, ellenőrizze, hogy az IP-cím magán, nyilvános vagy fenntartott-e. A privát hivatkozások működéséhez a Key Vault-állomásnévnek a Virtual Network címtartomány alá tartozó magánhálózati IP-címekre kell feloldania.

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>A Key Vault magánhálózati IP-címének megkeresése a virtuális hálózaton

Szüksége lesz az állomásnév feloldásának diagnosztizálására, és ahhoz, hogy tudnia kell a kulcstartó pontos magánhálózati IP-címét, amelyeken engedélyezve vannak a privát hivatkozások. Ennek a címnek a megkereséséhez kövesse az alábbi eljárást:

1. Nyissa meg a Azure Portal, és nyissa meg a Key Vault-erőforrást.
2. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget.
3. Kattintson a **privát végpont kapcsolatai** lapra. Ekkor megjelenik az összes privát végponti kapcsolat és a hozzájuk tartozó állapotok.
4. Keresse meg az Ön által diagnosztizált, és győződjön meg arról, hogy a "kapcsolatok állapota" **jóváhagyásra** került, és a kiépítési állapot **sikeres**. Ha ezt nem látja, térjen vissza a dokumentum előző részeire.
5. Ha megtalálta a megfelelő elemeket, kattintson a **privát végpont** oszlopban található hivatkozásra. Ekkor megnyílik a magánhálózati végpont erőforrása.
6. Az Áttekintés oldalon egy **Egyéni DNS-beállítások** nevű szakasz jelenhet meg. Győződjön meg arról, hogy csak egy olyan bejegyzés van, amely megfelel a Key Vault állomásnévnek. Ez a bejegyzés a Key Vault magánhálózati IP-címét jeleníti meg.
7. A **hálózati adapteren** található hivatkozásra kattintva ellenőrizheti, hogy a magánhálózati IP-cím megegyezik-e az előző lépésben láthatóval. A hálózati adapter a Key vaultot képviselő virtuális eszköz.

Az IP-cím az a számítógép, amelyet a virtuális gépek és más *, ugyanazon Virtual Network futtató* eszközök fognak használni a kulcstartóhoz való kapcsolódáshoz. Jegyezze fel az IP-címet, vagy hagyja nyitva a böngésző fület, és ne érintse meg a további vizsgálatok során.

>[!NOTE]
> Ha a Key Vault több privát végponttal rendelkezik, akkor több magánhálózati IP-címmel rendelkezik. Ez csak akkor hasznos, ha több virtuális hálózattal rendelkezik ugyanahhoz a kulcstartóhoz, amelyek mindegyike saját privát végponton keresztül (a privát végpont egyetlen Virtual Networkhoz tartozik). Győződjön meg arról, hogy a megfelelő Virtual Network diagnosztizálja a problémát, és a fenti eljárásban kiválasztja a megfelelő privát végponti kapcsolatokat. Emellett ne **hozzon** létre több privát végpontot ugyanahhoz a Key Vaulthoz ugyanabban a Virtual Network. Ez nem szükséges, és a zűrzavar forrása.

## <a name="5-validate-the-dns-resolution"></a>5. a DNS-feloldás ellenőrzése

A DNS-feloldás a Key Vault-állomásnév (például:) IP-címmé való lefordításának folyamata `fabrikam.vault.azure.net` (például: `10.1.2.3` ). Az alábbi alszakaszok az egyes forgatókönyvekben a DNS-feloldás várt eredményeit mutatják be.

### <a name="key-vaults-without-private-link"></a>Privát hivatkozás nélküli kulcstartók

Ez a szakasz tanulási célokra szolgál. Ha a Key Vault nem rendelkezik jóváhagyott állapotú magánhálózati végponti kapcsolatban, az állomásnév feloldása a következőhöz hasonló eredményt ad:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

Láthatja, hogy a név feloldása egy nyilvános IP-címre történik, és nincs `privatelink` alias. Az aliast később ismertetjük, ezért ne aggódjon.

A fenti eredmény attól függetlenül várható, hogy a gép csatlakoztatva van-e a Virtual Networkhoz, vagy egy internetkapcsolattal rendelkező, tetszőleges gép. Ez azért történik, mert a Key Vault nem rendelkezik jóváhagyott állapotú magánhálózati végponti kapcsolattal, ezért nem szükséges, hogy a Key Vault támogassa a privát hivatkozásokat.

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>A Key Vault és a privát kapcsolat feloldása tetszőleges internetes gépről

Ha a kulcstartó egy vagy több magánhálózati végponti kapcsolattal rendelkezik, és a gazdagépet egy tetszőleges, az internethez csatlakozó számítógépről oldja fel (olyan gép, amely *nem* csatlakozik a privát végpontot tároló Virtual Networkhoz), a következőt kell megtalálnia:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

Az előző forgatókönyvből származó jelentős különbség az, hogy van egy új alias az értékkel `{vaultname}.privatelink.vaultcore.azure.net` . Ez azt jelenti, hogy a Key Vault-adatsík készen áll arra, hogy fogadja a privát hivatkozások kéréseit.

Ez nem jelenti azt, hogy a Virtual Networkon *kívüli* gépekről érkező kérések (például az imént használtak) privát hivatkozásokat fognak használni – nem. Láthatja, hogy az állomásnév továbbra is egy nyilvános IP-címhez van feloldva. Csak *a Virtual Networkhoz csatlakoztatott* számítógépek használhatnak privát hivatkozásokat. További tudnivalókat a következő témakörben talál:.

Ha nem látja az `privatelink` aliast, az azt jelenti, hogy a Key vaultban nulla a privát végpont kapcsolatainak `Approved` állapota. Az újrapróbálkozás előtt térjen vissza [ehhez a szakaszhoz](#2-confirm-that-the-connection-is-approved-and-succeeded) .

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>Key Vault Virtual Network

Ha a kulcstartó egy vagy több magánhálózati végponti kapcsolattal rendelkezik jóváhagyott állapotban, és feloldja az állomásnevet egy olyan gépről, amely a privát végpont létrehozásához Virtual Network csatlakozik, akkor ez a várt válasz:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  10.1.2.3
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3
```

Két jelentős különbség van. Először a név feloldja a magánhálózati IP-címet. Ennek a cikknek a [megfelelő szakaszában](#find-the-key-vault-private-ip-address-in-the-virtual-network) található IP-címnek kell lennie. Másodszor, nincs más alias a következő után `privatelink` . Ez azért történik, mert a Virtual Network DNS-kiszolgálók *feltartóztatják* az aliasok láncát, és közvetlenül a név alapján adják vissza a magánhálózati IP-címet `fabrikam.privatelink.vaultcore.azure.net` . Ez a bejegyzés tulajdonképpen egy `A` saját DNS zónában lévő rekord. További tudnivalókat a következő témakörben talál:.

>[!NOTE]
> A fenti eredmény csak olyan virtuális gépen történik, amely ahhoz a Virtual Network csatlakozik, ahol a magánhálózati végpont létrejött. Ha nincs olyan virtuális gép telepítve a Virtual Networkban, amely a privát végpontot tartalmazza, telepítsen egyet, és távolról kapcsolódjon hozzá, majd hajtsa végre a fenti parancsot ( `nslookup` Windows) vagy a `host` (Linux) parancsot.

Ha ezeket a parancsokat egy olyan virtuális gépre futtatja, amely a privát végpont létrehozásához használt Virtual Networkhoz csatlakozik, és **nem** a Key Vault magánhálózati IP-címét jeleníti meg, a következő szakasz segíthet a probléma megoldásában.

## <a name="6-validate-the-private-dns-zone"></a>6. a saját DNS zóna ellenőrzése

Ha a DNS-feloldás nem működik az előző szakaszban leírtak szerint, előfordulhat, hogy probléma van a saját DNS zónával, és ez a szakasz segíthet. Ha a DNS-feloldás a megfelelő Key Vault magánhálózati IP-címet jeleníti meg, akkor a saját DNS zóna valószínűleg helyes. Ezt a teljes szakaszt kihagyhatja.

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Győződjön meg arról, hogy a szükséges saját DNS zóna-erőforrás létezik

Az Azure-előfizetéshez a következő pontos névvel rendelkező [saját DNS zóna](../../dns/private-dns-privatednszone.md) -erőforrásnak kell tartoznia:

`privatelink.vaultcore.azure.net`

Az erőforrás jelenlétének ellenőrzéséhez nyissa meg a portál előfizetés lapját, és a bal oldali menüben válassza az "erőforrások" lehetőséget. Az erőforrás nevének kötelezőnek kell lennie `privatelink.vaultcore.azure.net` , és az erőforrástípus **saját DNS zónának** kell lennie.

Általában ez az erőforrás automatikusan jön létre, amikor közös eljárással hoz létre egy privát végpontot. Vannak azonban olyan esetek, amikor az erőforrás nem jön létre automatikusan, és manuálisan kell elvégeznie. Lehet, hogy az erőforrás véletlenül törölve lett.

Ha nem rendelkezik ezzel az erőforrással, hozzon létre egy új saját DNS Zone-erőforrást az előfizetésében. Ne feledje, hogy a névnek pontosan `privatelink.vaultcore.azure.net` , szóközök vagy további pontok nélkül kell lennie. Ha helytelen nevet ad meg, a cikkben ismertetett névfeloldás nem fog működni. Az erőforrás létrehozásával kapcsolatos további információkért lásd: [Azure Private DNS-zóna létrehozása a Azure Portal használatával](../../dns/private-dns-getstarted-portal.md). Ha ezt az oldalt követi, kihagyhatja Virtual Network létrehozását, mert ezen a ponton már rendelkeznie kell egy már meglévővel. Az érvényesítési eljárásokat Virtual Machines használatával is kihagyhatja.

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Győződjön meg arról, hogy a saját DNS zóna csatolva van a Virtual Network

Nem elegendő saját DNS zónához. Emellett a magánhálózati végpontot tartalmazó Virtual Networkhoz is csatolni kell. Ha a saját DNS zóna nem a megfelelő Virtual Network van csatolva, akkor az adott Virtual Network DNS-feloldása figyelmen kívül hagyja a saját DNS zónát.

Nyissa meg a saját DNS zóna erőforrást, és kattintson a bal oldali menüben a **virtuális hálózati kapcsolatok** lehetőségre. Ekkor megjelenik a hivatkozások listája, amelyek mindegyike az előfizetéshez tartozó Virtual Network nevével jelenik meg. A privát végpont erőforrást tartalmazó Virtual Network itt kell szerepelnie. Ha nincs ott, vegye fel. A részletes lépésekért lásd: [a virtuális hálózat összekapcsolása](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network). Ha nem jelölte be a "automatikus regisztráció engedélyezése" lehetőséget, akkor a szolgáltatás nem kapcsolódik a privát hivatkozásokhoz.

Ha a saját DNS zóna a Virtual Networkhoz van csatolva, a Virtual Network származó DNS-kérések a saját DNS zónában fognak megjelenni a nevek. Erre azért van szükség, hogy a Virtual Machineshoz kapcsolódó helyes névfeloldást a magánhálózati végpont létrejöttének Virtual Network.

>[!NOTE]
> Ha most mentette a hivatkozást, akkor is eltarthat egy ideig, amíg a portálon azt is elvégezte, hogy a művelet befejeződik. Előfordulhat, hogy újra kell indítania a virtuális gépet, amelyet a DNS-feloldás tesztelésére használ.

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>Ellenőrizze, hogy a saját DNS zóna tartalmazza-e a megfelelő rekordot

A portál használatával nyissa meg a saját DNS zónát a névvel `privatelink.vaultcore.azure.net` . Az Áttekintés oldalon az összes rekord látható. Alapértelmezés szerint egy olyan rekord lesz, amelynek a neve `@` és típusa `SOA` , azaz a szolgáltató kezdete. Ne érintse meg.

Ahhoz, hogy a Key Vault névfeloldása működjön, `A` utótag vagy pontok nélkül rekordnak kell lennie az egyszerű tároló nevével. Ha például az állomásnév `fabrikam.vault.azure.net` , akkor `A` a névvel ellátott rekordnak `fabrikam` utótag vagy pont nélkül kell lennie.

Emellett a rekord értékének `A` (az IP-címnek) [a Key Vault magánhálózati IP-címének](#find-the-key-vault-private-ip-address-in-the-virtual-network)kell lennie. Ha megtalálta a `A` rekordot, de nem megfelelő IP-címet tartalmaz, akkor el kell távolítania a helytelen IP-címet, és hozzá kell adnia egy újat. Javasoljuk, hogy távolítsa el a teljes `A` rekordot, és vegyen fel egy újat.

>[!NOTE]
> Amikor eltávolít vagy módosít egy `A` rekordot, a gép továbbra is feloldható a régi IP-címhez, mert az élettartam (élettartam) értéke még nem jár le. Azt javasoljuk, hogy mindig az 60 másodpercnél (egy percnél) kisebb TTL-értéket válasszon, és ne legyen nagyobb 600 másodpercnél (10 perc). Ha olyan értéket ad meg, amely túl nagy, előfordulhat, hogy az ügyfelek túl sokáig tarthatnak az kimaradások helyreállítása érdekében.

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>Egynél több Virtual Network DNS-feloldása

Ha több virtuális hálózat van, és mindegyik saját privát végponti erőforrással hivatkozik ugyanarra a kulcstartóra, akkor a Key Vault-állomásnévnek a hálózattól függően egy másik magánhálózati IP-címhez kell feloldania. Ez azt jelenti, hogy több saját DNS zóna is szükséges, amelyek mindegyike egy másik Virtual Networkhoz kapcsolódik, és a rekordban eltérő IP-címet használ `A` .

A fejlettebb forgatókönyvek esetében előfordulhat, hogy a virtuális hálózatok esetében engedélyezve van a társítás. Ebben az esetben csak egy Virtual Networkra van szükség a privát végponti erőforráshoz, de előfordulhat, hogy mindkettőnek kapcsolódnia kell az saját DNS Zone-erőforráshoz. Ez a forgatókönyv nem vonatkozik közvetlenül a dokumentumra.

### <a name="understand-that-you-have-control-over-dns-resolution"></a>A DNS-feloldás vezérlésének megismerése

Ahogy az [előző szakaszban](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine)is látható, a privát hivatkozásokkal rendelkező kulcstartóban szerepel az alias a `{vaultname}.privatelink.vaultcore.azure.net` *nyilvános* regisztrációban. A Virtual Network által használt DNS-kiszolgáló a nyilvános regisztrációt használja, de ellenőrzi, hogy van-e minden alias a *privát* regisztrációhoz, és ha talál ilyet, a nyilvános regisztráció során megadott aliasokat fogja leállítani.

Ez a logika azt jelenti, hogy ha a Virtual Network egy nevű saját DNS zónához van csatolva `privatelink.vaultcore.azure.net` , és a Key Vault nyilvános DNS-regisztrációja az aliassal rendelkezik `fabrikam.privatelink.vaultcore.azure.net` (vegye figyelembe, hogy a Key Vault hostname utótagja pontosan megegyezik a saját DNS zóna nevével), majd a DNS-lekérdezés egy olyan rekordot keres, `A` amelynek neve a `fabrikam` *saját DNS zónában* van. Ha a `A` rekord megtalálható, a rendszer az IP-címet adja vissza a DNS-lekérdezésben, és a nyilvános DNS-regisztráció során nem végez további keresést.

Amint láthatja, a névfeloldás a vezérlő alatt található. Ennek a kialakításnak a logikája a következő:

- Lehet, hogy olyan összetett forgatókönyvvel rendelkezik, amely egyéni DNS-kiszolgálókat és helyszíni hálózatokkal való integrációt is magában foglal. Ebben az esetben meg kell határoznia, hogy a nevek hogyan legyenek lefordítva az IP-címekre.
- Előfordulhat, hogy privát hivatkozások nélkül kell hozzáférnie a kulcstartóhoz. Ebben az esetben az állomásnév feloldása a Virtual Networkból a nyilvános IP-címet kell visszaadnia, és ez azért történik, mert a privát hivatkozások nélküli kulcstartók nem rendelkeznek az `privatelink` aliassal a név regisztrálásakor.

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. Ellenőrizze, hogy a Key vaultra érkező kérések privát hivatkozást használnak-e

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>`/healthstatus`A Key Vault végpontjának lekérdezése

A Key Vault biztosítja a `/healthstatus` végpontot, amely a diagnosztika használatával használható. A válasz fejlécei tartalmazzák a forrás IP-címét, ahogy azt a Key Vault szolgáltatás is látja. A végpontot a következő paranccsal hívhatja meg (**ne felejtse el használni a Key Vault hostname-t**):

Windows (PowerShell):

```powershell
PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers
```

```output
Key                           Value
---                           -----
Pragma                        no-cache
x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
x-ms-keyvault-service-version 1.2.27.0
x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security     max-age=31536000;includeSubDomains
Content-Length                4
Cache-Control                 no-cache
Content-Type                  application/json; charset=utf-8
```

A Linux vagy a Windows 10-es legújabb verziója, amely a következőket tartalmazza `curl` :

```console
joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
```

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: application/json; charset=utf-8
x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
x-ms-keyvault-service-version: 1.2.27.0
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security: max-age=31536000;includeSubDomains
Content-Length: 4
```

Ha nem az ehhez hasonló kimenetet kap, vagy ha hálózati hibát észlel, az azt jelenti, hogy a kulcstartó nem érhető el a megadott állomásnévn keresztül ( `fabrikam.vault.azure.net` a példában). Az állomásnév nem oldja meg a megfelelő IP-címet, vagy kapcsolódási problémája van a szállítási rétegben. Ennek oka lehet az útválasztási problémák, a csomagok leállítása és egyéb okok. További vizsgálatot kell végeznie.

A válasznak tartalmaznia kell a fejlécet `x-ms-keyvault-network-info` :

```console
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
```

A `addr` `x-ms-keyvault-network-info` fejlécben lévő mező a kérelem FORRÁSÁNAK IP-címét jeleníti meg. Ez az IP-cím a következők egyike lehet:

- A kérést végző gép magánhálózati IP-címe. Ez azt jelzi, hogy a kérelem privát hivatkozásokat vagy szolgáltatási végpontokat használ. Ez a privát hivatkozások várható eredménye.
- Egy másik magánhálózati IP-cím, a kérést *nem* a gépről. Ez azt jelzi, hogy néhány egyéni útválasztás érvényes. Azt is jelzi, hogy a kérelem privát hivatkozásokat vagy szolgáltatási végpontokat használ.
- Néhány nyilvános IP-cím. Ez azt jelzi, hogy a rendszer átjáró (NAT) eszközön keresztül továbbítja a kérést az internethez. Ez azt jelzi, hogy a kérés nem használ privát hivatkozásokat, és némi problémát meg kell oldani. Ennek gyakori okai 1) a privát végpont nem jóváhagyott és sikeres állapotú. és 2) az állomásnév nem oldható fel a Key Vault magánhálózati IP-címére. Ez a cikk mindkét esetben hibaelhárítási műveleteket tartalmaz.

>[!NOTE]
> Ha a kérelem úgy `/healthstatus` működik, hogy a `x-ms-keyvault-network-info` fejléc hiányzik, akkor a rendszer valószínűleg nem fogja kiszolgálni a kulcstárolót. Mivel a fenti parancsok a HTTPS-tanúsítványt is érvényesítik, előfordulhat, hogy a hiányzó fejléc az illetéktelen módosítás jele lehet.

### <a name="query-the-key-vault-ip-address-directly"></a>A Key Vault IP-címének lekérdezése közvetlenül

>[!IMPORTANT]
> A Key Vault a HTTPS-tanúsítvány ellenőrzése nélkül való elérése veszélyes, és csak tanulási célokra használható. Az üzemi kód nem fér hozzá a Key vaulthoz az ügyféloldali ellenőrzés nélkül. Ha csak a problémák diagnosztizálására van szükség, előfordulhat, hogy a rendszer nem fogja feltárni a sikertelen kísérleteket, ha gyakran letiltja a HTTPS-tanúsítvány érvényesítését a Key vaultba érkező kérésekben.

Ha telepítette a PowerShell legújabb verzióját, `-SkipCertificateCheck` a használatával kihagyhatja a https-tanúsítványok ellenőrzését, majd közvetlenül a [kulcstartó IP-címét](#find-the-key-vault-private-ip-address-in-the-virtual-network) is megcélozhatja:

```powershell
PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers
```

Ha használja `curl` , ugyanezt az argumentummal is megteheti `-k` :

```console
joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus
```

A válaszoknak meg kell egyezniük az előző szakasztal, ami azt jelenti, hogy tartalmaznia kell a `x-ms-keyvault-network-info` fejlécet ugyanazzal az értékkel. A `/healthstatus` végpont nem érdekli, ha a Key Vault-állomásnevet vagy IP-címet használja.

Ha úgy látja, hogy `x-ms-keyvault-network-info` egy értéket ad vissza a kérelemhez a Key Vault hostname használatával, és egy másik értéket a kérelemhez az IP-cím használatával, akkor minden kérelem egy másik végpontot céloz meg. Tekintse át a mező magyarázatát az `addr` `x-ms-keyvault-network-info` előző szakaszban, hogy eldöntse, melyik eset nem megfelelő, és hogy rögzíteni kell-e.

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. a hatást kiváltó egyéb változások és testreszabások

A következő elemek nem kimerítő vizsgálati műveletek. Megtudhatják, hová kell keresniük a további problémákat, de speciális hálózati ismeretekkel kell rendelkezniük az ilyen helyzetekben felmerülő problémák megoldásához.

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>Egyéni DNS-kiszolgálók diagnosztizálása a következő helyen: Virtual Network

A portálon nyissa meg a Virtual Network erőforrást. A bal oldali menüben nyissa meg a **DNS-kiszolgálókat**. Ha az "egyéni" lehetőséget használja, akkor előfordulhat, hogy a DNS-feloldás nem a jelen dokumentumban ismertetett módon történik. Meg kell állapítania, hogy a DNS-kiszolgálók hogyan oldják fel a Key Vault-gazdagépet.

Ha az alapértelmezett Azure által biztosított DNS-kiszolgálókat használja, ez a teljes dokumentum alkalmazható.

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>Gazdagépek felülbírálása vagy egyéni DNS-kiszolgálók diagnosztizálása a virtuális gépen

Számos operációs rendszer engedélyezi egy explicit rögzített IP-cím beállítását egy állomásnévre, jellemzően a fájl módosításával `hosts` . Emellett lehetővé tehetik a DNS-kiszolgálók felülbírálását is. Ha ezen forgatókönyvek valamelyikét használja, folytassa a rendszerspecifikus diagnosztikai lehetőségekkel.

### <a name="promiscuous-proxies-fiddler-etc"></a>Kevert proxyk (Hegedűs, stb.)

Ha explicit módon megjegyezte, a jelen cikkben található diagnosztikai beállítások csak akkor működnek, ha nincs kevert proxy a környezetben. Habár ezek a proxyk általában kizárólag a diagnosztizált gépen vannak telepítve (a Hegedűs a leggyakoribb példa), a speciális rendszergazdák felülírhatják a főtanúsítvány-hatóságokat (CAs), és egy kevert proxyt telepíthetnek a hálózatban több gépet kiszolgáló átjáró-eszközökön. Ezek a proxyk jelentősen befolyásolhatják a biztonságot és a megbízhatóságot is. A Microsoft nem támogatja az ilyen termékeket használó konfigurációkat.

### <a name="other-things-that-may-affect-connectivity"></a>Egyéb dolgok, amelyek hatással lehetnek a kapcsolatra

Ez az olyan elemek nem teljes listája, amelyek a speciális vagy összetett forgatókönyvekben találhatók:

- A tűzfal beállításai, vagy a Virtual Networkhoz csatlakoztatott Azure Firewall vagy a Virtual Network vagy a gépen üzembe helyezett egyéni tűzfal megoldás.
- Hálózati társítás, amely hatással lehet a DNS-kiszolgálók használatára és a forgalom irányítására.
- Egyéni átjáró (NAT) megoldások, amelyek hatással lehetnek a forgalom irányítására, beleértve a DNS-lekérdezésektől érkező forgalmat is.