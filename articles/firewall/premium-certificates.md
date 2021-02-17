---
title: Prémium előzetes verziójú tanúsítványok Azure Firewall
description: A TLS-ellenőrzésnek a Azure Firewall Premium Preview-on való megfelelő konfigurálásához konfigurálnia és telepítenie kell a köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3914a82903c293cf1a8306b5ecc1f542fef83e72
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549756"
---
# <a name="azure-firewall-premium-preview-certificates"></a>Prémium előzetes verziójú tanúsítványok Azure Firewall 

> [!IMPORTANT]
> A Azure Firewall Premium szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Azure Firewall Premium Preview TLS-vizsgálat megfelelő konfigurálásához meg kell adnia egy érvényes közbenső HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt, és az Azure Key vaultban kell befizetnie.

## <a name="certificates-used-by-azure-firewall-premium-preview"></a>Azure Firewall Premium Preview által használt tanúsítványok

Egy tipikus központi telepítésben három típusú tanúsítvány használatos:

- **Köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány)**

   A hitelesítésszolgáltatók (CA-k) olyan szervezetek, amelyek megbízhatók a digitális tanúsítványok aláírásához. A HITELESÍTÉSSZOLGÁLTATÓ ellenőrzi a vállalat vagy a tanúsítványt kérő személy identitását és törvényességét. Ha az ellenőrzés sikeres, a HITELESÍTÉSSZOLGÁLTATÓ aláírt tanúsítványt bocsát ki. Amikor a kiszolgáló megadja a tanúsítványt az ügyfélnek (például a webböngészőnek) az SSL/TLS-kézfogás során, az ügyfél megkísérli ellenőrizni az aláírást az *ismert jó* aláírók listájával. A webböngészők általában olyan hitelesítésszolgáltatók listájával rendelkeznek, amelyek implicit módon bíznak a gazdagépek azonosításában. Ha a szolgáltató nem szerepel a listában, akárcsak a saját tanúsítványait aláíró webhelyek esetében, a böngésző figyelmezteti a felhasználót, hogy a tanúsítványt nem egy elismert hatóság írta alá, és kéri a felhasználót, hogy folytassa a nem ellenőrzött hellyel való kommunikációt.

- **Kiszolgálótanúsítvány (webhely-tanúsítvány)**

   Adott tartománynévhez társított tanúsítvány. Ha egy webhely rendelkezik érvényes tanúsítvánnyal, az azt jelenti, hogy a hitelesítésszolgáltató lépéseket tett annak ellenőrzéséhez, hogy a webcíme ténylegesen a szervezethez tartozik-e. Amikor begépel egy URL-címet, vagy egy biztonságos webhelyre mutató hivatkozást követ, a böngésző a következő jellemzőkkel ellenőrzi a tanúsítványt:
   - A webhely címe megegyezik a tanúsítványhoz tartozó címtől.
   - A tanúsítványt egy hitelesítésszolgáltató írja alá, hogy a böngésző *megbízható* szolgáltatóként ismeri fel.
   
   Alkalmanként előfordulhat, hogy a felhasználók nem megbízható tanúsítvánnyal rendelkező kiszolgálóhoz csatlakoznak. Azure Firewall el fogja dobni a kapcsolatokat úgy, mintha a kiszolgáló megszakította a kapcsolatokat.

- **Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (főtanúsítvány)**

   A hitelesítésszolgáltató több tanúsítványt is kiadhat egy fastruktúra formájában. A főtanúsítvány a fa legfelső szintű tanúsítványa.

Azure Firewall Premium Preview képes a kimenő HTTP/S forgalom elfogására és a kiszolgálói tanúsítvány automatikus előállítására `www.website.com` . Ez a tanúsítvány az Ön által megadott köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány használatával jön létre. A végfelhasználói böngészőnek és az ügyfélalkalmazásnak megbízhatónak kell lennie a szervezet legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának vagy köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának az eljárás működéséhez. 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="Tanúsítvány feldolgozása":::

## <a name="intermediate-ca-certificate-requirements"></a>A köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra vonatkozó követelmények

Győződjön meg arról, hogy a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megfelel a következő követelményeknek:

- Key Vault titokként való üzembe helyezéskor a jelszó nélküli PFX (Pkcs12/pfx-profil) tanúsítványt és titkos kulcsot kell használnia.

- Egyetlen tanúsítványnak kell lennie, és nem szabad a tanúsítványok teljes láncát tartalmaznia.  

- A kérelemnek egy évig előre kell lennie.  

- Legalább 4096 bájt méretű RSA titkos kulcsnak kell lennie.  

- A `KeyUsage` `KeyCertSign` jelölőnek a jelzővel (RFC 5280; 4.2.1.3 Key-használattal) kritikusnak kell lennie.

- A `BasicContraints` bővítménynek kritikus jelöléssel (RFC 5280; 4.2.1.9 alapvető megkötésekkel) kell rendelkeznie.  

- A `CA` jelzőt True értékre kell állítani.

- Az elérési út hosszának eggyel nagyobbnak vagy azzal egyenlőnek kell lennie.

## <a name="azure-key-vault"></a>Azure Key Vault

A [Azure Key Vault](../key-vault/general/overview.md) egy platform által felügyelt titkos tároló, amely a titkok, a kulcsok és a TLS/SSL-tanúsítványok védelmére használható. A Azure Firewall Premium támogatja a tűzfal-házirendhez csatolt kiszolgálói tanúsítványok Key Vault-integrációját.
 
A Key Vault konfigurálása:

- A Key vaultba egy meglévő tanúsítványt kell importálnia. 
- Azt is megteheti, hogy egy Key Vault-titkot is használ, amely jelszó nélküli, Base-64 kódolású PFX-fájlként van tárolva.  A PFX-fájlok egy titkos kulcsot és egy nyilvános kulcsot tartalmazó digitális tanúsítvány.
- A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány importálását ajánlott használni, mert lehetővé teszi, hogy a tanúsítvány lejárati dátuma alapján konfiguráljon riasztást.
- Miután importált egy tanúsítványt vagy titkos kulcsot, meg kell határoznia a Key Vault hozzáférési szabályzatait, hogy lehetővé váljon a tanúsítvány/titok hozzáférésének engedélyezése az identitás számára.
- Az Azure-beli számítási feladatnak megbízhatónak kell lennie a megadott HITELESÍTÉSSZOLGÁLTATÓI tanúsítványon. Győződjön meg arról, hogy megfelelően vannak-e telepítve.

Létrehozhat vagy felhasználhat egy meglévő, felhasználó által hozzárendelt felügyelt identitást, amelyet a Azure Firewall a tanúsítványok lekéréséhez a Key Vault az Ön nevében. További információ: [Mi az az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md) 

## <a name="configure-a-certificate-in-your-policy"></a>Tanúsítvány konfigurálása a szabályzatban

Ha HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt szeretne konfigurálni a tűzfal prémium házirendjében, válassza ki a szabályzatot, majd válassza a **TLS-vizsgálat (előzetes verzió)** lehetőséget. Válassza az **engedélyezve** lehetőséget a **TLS-ellenőrzési** oldalon. Ezután válassza ki a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt Azure Key Vaultban, ahogy az a következő ábrán látható:

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Azure Firewall Premium – áttekintés diagram":::
 
> [!IMPORTANT]
> Ha szeretné megtekinteni és konfigurálni a tanúsítványokat a Azure Portalból, fel kell vennie az Azure-beli felhasználói fiókját az Key Vault hozzáférési szabályzatba. Adja meg a felhasználói **fiókját** , és **sorolja** fel a **titkos engedélyeket**.
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Hozzáférési szabályzat Azure Key Vault":::


## <a name="troubleshooting"></a>Hibaelhárítás

Ha a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány érvényes, de a TLS-vizsgálat alatt nem fér hozzá a teljes tartománynevek vagy URL-címekhez, ellenőrizze a következő elemeket:

- Győződjön meg arról, hogy a webkiszolgáló-tanúsítvány érvényes.  

- Győződjön meg arról, hogy a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány telepítve van az ügyfél operációs rendszerén.  

- Győződjön meg arról, hogy a böngésző vagy a HTTPS-ügyfél tartalmaz egy érvényes főtanúsítványt. A Firefox és más böngészők speciális minősítési házirendekkel rendelkezhetnek.  

- Győződjön meg arról, hogy az alkalmazási szabályban szereplő URL-célcím tartalmazza a helyes elérési utat és a célként megadott HTML-lapon beágyazott összes hivatkozást. Használhat helyettesítő karaktereket is a teljes szükséges URL-cím elérési útjának egyszerű biztosításához.  


## <a name="next-steps"></a>Következő lépések

- [További információ a Azure Firewall Premium funkcióival kapcsolatban](premium-features.md)
