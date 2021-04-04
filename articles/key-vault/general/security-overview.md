---
title: Azure Key Vault biztonsági áttekintés
description: A Azure Key Vault biztonsági funkcióinak és ajánlott eljárásainak áttekintése.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: mbaldwin
ms.openlocfilehash: c7635fdc2012ab404709733d8f5849465c2ee82f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99071565"
---
# <a name="azure-key-vault-security"></a>Az Azure Key Vault biztonsága

A Azure Key Vault használatával védi a felhőben a titkosítási kulcsokat és a titkos kódokat, például a tanúsítványokat, a kapcsolódási karakterláncokat és a jelszavakat. Bizalmas és üzleti szempontból kritikus fontosságú adatok tárolásához lépéseket kell tennie a tárolók és a bennük tárolt adatok biztonságának maximalizálása érdekében.

Ez a cikk a Azure Key Vault biztonsági funkcióinak és ajánlott eljárásainak áttekintését tartalmazza. 

> [!NOTE]
> A Azure Key Vault biztonsági javaslatainak átfogó listáját a [Azure Key Vault biztonsági](security-baseline.md)alapkonfigurációjában tekintheti meg.

## <a name="network-security"></a>Hálózati biztonság

A tárolók expozíciójának csökkentése érdekében megadhatja, hogy mely IP-címek férhessenek hozzájuk. A Azure Key Vault Virtual Network szolgáltatásbeli végpontok lehetővé teszik, hogy korlátozza a hozzáférést egy adott virtuális hálózathoz. A végpontok azt is lehetővé teszik, hogy korlátozza a hozzáférést az IPv4 (Internet Protocol Version 4)-címtartományok listájához. A Key vaulthoz a forrásokon kívülről csatlakozó felhasználók hozzáférése megtagadva.  Részletekért lásd: [virtuális hálózati szolgáltatás végpontjai Azure Key Vault](overview-vnet-service-endpoints.md)

A tűzfalszabályok érvénybe léptetése után a felhasználók csak akkor tudják olvasni az Key Vault adatait, ha a kérésük engedélyezett virtuális hálózatokról vagy IPv4-címtartományok származnak. Ez a Azure Portal Key Vault elérésére is vonatkozik. Bár a felhasználók megkereshetik a kulcstartót a Azure Portalból, előfordulhat, hogy nem tudják listázni a kulcsokat, titkokat vagy tanúsítványokat, ha az ügyfélszámítógépük nem szerepel az engedélyezési listán. Ez hatással van a más Azure-szolgáltatások Key Vault választóra is. Előfordulhat, hogy a felhasználók megtekinthetik a kulcstárolók listáját, de nem listázják a kulcsokat, ha a tűzfalszabályok megakadályozzák az ügyfélszoftvert.  A megvalósítási lépésekért lásd: [Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](network-security.md)

Az Azure Private link Service lehetővé teszi, hogy a virtuális hálózat privát végpontján keresztül hozzáférhessen Azure Key Vault és az Azure által üzemeltetett ügyfél-és partneri szolgáltatásokhoz. Az Azure Private-végpontok olyan hálózati adapterek, amelyek az Azure Private-kapcsolaton keresztül csatlakoznak a szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Kapcsolódhat egy Azure-erőforrás egy példányához, amely a legmagasabb szintű részletességet nyújtja a hozzáférés-vezérlésben.  A megvalósítási lépésekért lásd: [a Key Vault integrálása az Azure Private-hivatkozással](private-link-service.md)

## <a name="tls-and-https"></a>TLS és HTTPS

- A Key Vault előtér (adatsík) egy több-bérlős kiszolgáló. Ez azt jelenti, hogy a különböző ügyfelektől származó kulcstartók ugyanazt a nyilvános IP-címet tudják megosztani. Az elkülönítés érdekében minden HTTP-kérelem hitelesítése és engedélyezése más kérelmektől függetlenül történik.
- A biztonsági rések jelentésére a TLS régebbi verzióit is meghatározhatja, de mivel a nyilvános IP-cím meg van osztva, nem lehetséges, hogy a Key Vault szolgáltatás csapata letiltsa a TLS régi verzióit az egyes kulcstartók esetében a szállítási szinten.
- A HTTPS protokoll lehetővé teszi, hogy az ügyfél részt vegyen a TLS-egyeztetésben. Az **ügyfelek kihasználhatják a TLS legújabb verzióját**, és amikor az ügyfél ezt teszi, a teljes kapcsolatok a megfelelő szintű védelmet fogják használni. Az a tény, hogy Key Vault továbbra is támogatja a régebbi TLS-verziókat, nem rontja a kapcsolatok biztonságát újabb TLS-verziók használatával.
- A TLS protokoll ismert biztonsági rései ellenére nincs olyan ismert támadás, amely lehetővé tenné egy rosszindulatú ügynök számára, hogy kinyerje a kulcstartóból származó adatokat, amikor a támadó a biztonsági réseket tartalmazó TLS-verzióval létesített kapcsolatokat kezdeményez. A támadónak továbbra is hitelesítenie és hitelesítenie kell magát, és mindaddig, amíg a megbízható ügyfelek mindig csatlakoznak a legutóbbi TLS-verziókhoz, nincs mód arra, hogy a hitelesítő adatokat kiszivárgott a korábbi TLS-verziók biztonsági rései miatt.

## <a name="identity-management"></a>Identitáskezelés

Amikor kulcstartót hoz létre egy Azure-előfizetésben, az automatikusan társítva lesz az előfizetés Azure AD-bérlője számára. Az Azure AD-nek hitelesítenie kell azokat a személyeket, akik tartalmat próbálnak kezelni vagy beolvasni a tárból. Mindkét esetben az alkalmazások háromféle módon férhetnek hozzá a Key Vaulthoz:

- **Csak alkalmazás**: az alkalmazás az egyszerű szolgáltatásnév vagy a felügyelt identitást jelöli. Ez az identitás a leggyakoribb forgatókönyv azon alkalmazások esetében, amelyeknek időnként szükségük van tanúsítványokra, kulcsokra vagy titkos kulcsok elérésére a kulcstartóból. Ahhoz, hogy ez a forgatókönyv működjön, a `objectId` hozzáférési házirendben meg kell adni az alkalmazást, és `applicationId` a _nem_ adható meg vagy kötelező `null` .
- **Csak felhasználó**: a felhasználó hozzáfér a Key vaulthoz a bérlőben regisztrált bármely alkalmazásból. Ilyen típusú hozzáférés például a Azure PowerShell és a Azure Portal. Ahhoz, hogy ez a forgatókönyv működjön, a `objectId` felhasználónak meg kell adni a hozzáférési szabályzatot, és `applicationId` a _nem_ adható meg vagy kötelező `null` .
- **Alkalmazás-Plus-User** (más néven _összetett identitás_): a felhasználónak egy adott alkalmazásból kell hozzáférnie a kulcstartóhoz _, és_ az alkalmazásnak a felhasználó megszemélyesítéséhez a hitelesítési (OBO) folyamatot kell használnia. Ahhoz, hogy ez a forgatókönyv működjön, mindkettőt `applicationId` `objectId` meg kell adni a hozzáférési házirendben. Az `applicationId` azonosítja a szükséges alkalmazást, és `objectId` azonosítja a felhasználót. Ez a lehetőség jelenleg nem érhető el az Azure RBAC (előzetes verzió) adatsíkon.

Az alkalmazás minden típusú hozzáférés esetén az Azure AD-vel hitelesíti magát. Az alkalmazás a [támogatott hitelesítési módszereket](../../active-directory/develop/authentication-vs-authorization.md) használja az alkalmazás típusától függően. Az alkalmazás jogkivonatot ad a síkon lévő erőforráshoz a hozzáférés biztosításához. Az erőforrás az Azure-környezet alapján a felügyelet vagy az adatsík végpontja. Az alkalmazás a jogkivonatot használja, és egy REST API kérelmet küld Key Vaultnak. További információért tekintse át a [teljes hitelesítési folyamatot](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

A részletekért lásd: [Key Vault Authentication alapjai](authentication-fundamentals.md)

## <a name="privileged-access"></a>Emelt szintű hozzáférés

Az engedélyezés meghatározza a hívó által végrehajtható műveleteket. A Key Vaultban történő engedélyezés az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) és a Azure Key Vault hozzáférési házirendek kombinációját használja.

A tárolók elérése két felületen vagy síkon történik. Ezek a síkok a felügyeleti sík és az adatsík.

- A *felügyeleti síkon* a Key Vault felügyeli, és a tárolók létrehozásához és törléséhez használt felület. A Key Vault tulajdonságait is beolvashatja, és kezelheti a hozzáférési házirendeket.
- Az *adatsíkok* lehetővé teszik a kulcstartóban tárolt adatmennyiség használatát. Kulcsok, titkos kódok és tanúsítványok hozzáadására, törlésére és módosítására is lehetőség van.

Az alkalmazások végpontokon keresztül férnek hozzá a síkokhoz. A két sík hozzáférés-vezérlése egymástól függetlenül működik. Ahhoz, hogy egy alkalmazás hozzáférjen a kulcsok használatához a kulcstartóban, Key Vault hozzáférési szabályzattal vagy Azure RBAC (előzetes verzió) biztosíthatja az adatsíkok elérését. Ahhoz, hogy a felhasználó olvasási hozzáférést biztosítson Key Vault tulajdonságokhoz és címkékhez, de nem fér hozzá az adatokhoz (kulcsokhoz, titkokhoz vagy tanúsítványokhoz), a felügyeleti sík hozzáférést biztosít az Azure RBAC.

A következő táblázat a felügyeleti és adatsíkok végpontját mutatja be.

| Hozzáférési &nbsp; sík | Hozzáférés végpontjai | Üzemeltetés | Hozzáférés- &nbsp; vezérlési mechanizmus |
| --- | --- | --- | --- |
| Felügyeleti sík | **Globális**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 | Kulcstartók létrehozása, olvasása, frissítése és törlése<br><br>Key Vault hozzáférési szabályzatok beállítása<br><br>Key Vault címkék beállítása | Azure RBAC-vel |
| Adatsík | **Globális**<br> &lt;tároló-neve&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;tároló-neve&gt;.vault.azure.cn:443<br><br> **Azure US government:**<br> &lt;tároló-neve&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;tároló-neve&gt;.vault.microsoftazure.de:443 | Kulcsok: titkosítás, visszafejtés, wrapKey, unwrapKey, aláírás, ellenőrzés, beolvasás, Listázás, létrehozás, frissítés, importálás, törlés, helyreállítás, biztonsági mentés, visszaállítás, kiürítés<br><br> Tanúsítványok: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, beolvasás, Listázás, létrehozás, importálás, frissítés, törlés, helyreállítás, biztonsági mentés, visszaállítás, kiürítés<br><br>  Titkok: beolvasás, Listázás, beállítás, törlés, helyreállítás, biztonsági mentés, visszaállítás, kiürítés | Key Vault hozzáférési szabályzat vagy Azure RBAC (előzetes verzió)|

### <a name="managing-administrative-access-to-key-vault"></a>A Key Vault rendszergazdai hozzáférésének kezelése

Amikor létrehoz egy kulcstartót egy erőforráscsoporthoz, az Azure AD használatával kezelheti a hozzáférést. A felhasználók vagy csoportok számára engedélyezheti az erőforráscsoport kulcstárolóinak kezelését. A megfelelő Azure-szerepkörök hozzárendelésével megadhatja a hozzáférést egy adott hatóköri szinten. Ahhoz, hogy hozzáférést biztosítson egy felhasználónak a kulcstartók kezeléséhez, egy előre meghatározott `key vault Contributor` szerepkört kell hozzárendelni a felhasználóhoz egy adott hatókörben. Az Azure-szerepkörökhöz a következő hatóköröket lehet hozzárendelni:

- **Előfizetés**: az előfizetés szintjén hozzárendelt Azure-szerepkörök az adott előfizetésen belüli összes erőforráscsoport és erőforrásra érvényesek.
- **Erőforráscsoport**: az erőforráscsoport szintjén hozzárendelt Azure-szerepkör az adott erőforráscsoport összes erőforrására vonatkozik.
- **Adott** erőforrás: egy adott erőforráshoz hozzárendelt Azure-szerepkör az adott erőforrásra vonatkozik. Ebben az esetben az erőforrás egy adott kulcstartó.

Számos előre definiált szerepkör létezik. Ha egy előre meghatározott szerepkör nem felel meg az igényeinek, megadhatja saját szerepkörét. További információt az [Azure RBAC: beépített szerepkörök](../../role-based-access-control/built-in-roles.md)című témakörben talál.

> [!IMPORTANT]
> Ha a felhasználó rendelkezik `Contributor` engedéllyel egy kulcstartó felügyeleti síkon, a felhasználó hozzáférést biztosíthat az adatsíkon az Key Vault hozzáférési szabályzat beállításával. Szigorúan szabályozhatja, `Contributor` hogy kik rendelkeznek szerepkör-hozzáféréssel a kulcstartóhoz. Győződjön meg arról, hogy csak a jogosult személyek férhetnek hozzá és kezelhetik a kulcstartókat, kulcsokat, titkos kulcsokat és tanúsítványokat.

### <a name="controlling-access-to-key-vault-data"></a>Key Vault-adatelérés szabályozása

Key Vault hozzáférési házirendek külön engedélyeket biztosítanak a kulcsokhoz, titkokhoz vagy tanúsítványokhoz. A felhasználók csak a kulcsokhoz és a titkokhoz való hozzáférést adhatnak meg. A kulcsok, titkos kódok és tanúsítványok hozzáférési engedélyei a tároló szintjén kezelhetők.

> [!IMPORTANT]
> Key Vault hozzáférési házirendek nem támogatják a szemcsés, az objektum szintű engedélyeket, például egy adott kulcsot, titkos kulcsot vagy tanúsítványt. Ha a felhasználó engedélyt kap a kulcsok létrehozására és törlésére, akkor ezeket a műveleteket a kulcstartó összes kulcsán végrehajthatja.

A kulcstartó hozzáférési szabályzatait a [Azure Portal](assign-access-policy-portal.md), az [Azure CLI](assign-access-policy-cli.md), az [Azure POWERSHELL](assign-access-policy-powershell.md)vagy a [Key Vault felügyeleti REST API](/rest/api/keyvault/)-k használatával állíthatja be.

Az adatsíkok hozzáférését korlátozhatja a [virtuális hálózati szolgáltatás végpontjának használatával Azure Key Vault](overview-vnet-service-endpoints.md)). A [tűzfalat és a virtuális hálózati szabályokat](network-security.md) egy további biztonsági réteghez is konfigurálhatja.

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

Key Vault naplózás a tárolón végrehajtott tevékenységekre vonatkozó adatokat takarít meg. Részletekért lásd: [Key Vault naplózása](logging.md).

Ha a Key vaultban tárolt kulcs, tanúsítvány vagy titok állapota módosult, akkor a Key Vault integrálása Event Grid használatával. Részletekért lásd: [Key Vault figyelése Azure Event Grid](event-grid-overview.md)

Fontos a kulcstartó állapotának figyelése is, hogy a szolgáltatás a kívánt módon működjön. Ennek módjáról a [Azure Key Vault figyelése és riasztása](alert.md)című témakörben olvashat bővebben.

## <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás

Azure Key Vault a Soft-delete és a Purge Protection lehetővé teszi a törölt tárolók és tároló objektumok helyreállítását. További részletekért lásd: [Azure Key Vault-törlés – áttekintés](soft-delete-overview.md).

A tárolóban lévő objektumok frissítését, törlését és létrehozását is rendszeresen vissza kell vennie a tárolóba.  

## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault biztonsági alapterv](security-baseline.md)
- [Azure Key Vault ajánlott eljárások](security-baseline.md)
- [Virtuális hálózati szolgáltatásvégpontok az Azure Key Vaulthoz](overview-vnet-service-endpoints.md)
- [Azure RBAC: beépített szerepkörök](../../role-based-access-control/built-in-roles.md)
