---
title: Vállalati HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok telepítése és konfigurálása Azure Firewall Premium előzetes verzióhoz
description: Ismerje meg, hogyan telepítheti és konfigurálhatja a vállalati HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat Azure Firewall Premium Preview-hoz.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: fba95214a6bbb0482166eab8f77f30911986fbb7
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102525483"
---
# <a name="deploy-and-configure-enterprise-ca-certificates-for-azure-firewall-preview"></a>Vállalati HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok központi telepítése és konfigurálása Azure Firewall előzetes verzióhoz

> [!IMPORTANT]
> A Azure Firewall Premium szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Azure Firewall Premium Preview tartalmaz egy TLS-ellenőrzési funkciót, amelyhez tanúsítvány-hitelesítési lánc szükséges. Éles üzembe helyezés esetén vállalati PKI-t kell használnia a Azure Firewall Premium szolgáltatással használt tanúsítványok létrehozásához. Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet egy köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt Azure Firewall Premium előzetes verzióhoz.

A Azure Firewall Premium Preview által használt tanúsítványokkal kapcsolatos további információkért lásd: [Azure Firewall Premium Preview-tanúsítványok](premium-certificates.md).

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ha vállalati HITELESÍTÉSSZOLGÁLTATÓT szeretne használni a Azure Firewall Premium Preview-hoz használandó tanúsítvány előállításához, a következő erőforrásokkal kell rendelkeznie: 

- egy Active Directory erdő 
- egy Active Directory hitelesítésszolgáltatói legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ, amelyen engedélyezve van a webes igénylés 
- prémium szintű tűzfal-házirenddel rendelkező Azure Firewall Premium 
- egy Azure Key Vault 
- olyan felügyelt identitás, amely olvasási engedéllyel rendelkezik a Key Vault hozzáférési házirendben meghatározott **tanúsítványokhoz és titkokhoz** 

## <a name="request-and-export-a-certificate"></a>Tanúsítvány igénylése és exportálása

1. Nyissa meg a webes igénylés webhelyét a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓN, `https://<servername>/certsrv` és válassza a **tanúsítvány kérése** lehetőséget.
1. Válassza a **Speciális tanúsítványkérelem** lehetőséget.
1. Válassza a **Létrehozás lehetőséget, és küldje el a kérelmet a hitelesítésszolgáltatónak**.
1. Töltse ki az űrlapot az alárendelt hitelesítésszolgáltató sablonnal az alábbi ábrán látható módon:
1. Küldje el a kérést, és telepítse a tanúsítványt.
1. Ha ezt a kérést egy, az Internet Explorer alkalmazást használó Windows-kiszolgálóról kezdeményezték, nyissa meg az **Internetbeállítások** párbeszédpanelt.
1. Navigáljon a **Content (tartalom** ) lapra, és válassza a **tanúsítványok** lehetőséget.
1. Válassza ki az imént kiadott tanúsítványt, majd válassza az **Exportálás** lehetőséget.
1. A varázsló elindításához kattintson a **tovább** gombra. Válassza **az igen, a titkos kulcs exportálása** lehetőséget, majd kattintson a **tovább** gombra.
1. a. pfx fájl formátuma alapértelmezés szerint ki van választva. **Ha lehetséges, törölje a jelet az összes tanúsítvány belefoglalása a tanúsítási útvonalba** jelölőnégyzetből. Ha a teljes tanúsítványláncot exportálja, a Azure Firewall importálási folyamat sikertelen lesz.
1. Rendeljen hozzá és erősítsen meg egy jelszót a kulcs védeleméhez, majd kattintson a **tovább** gombra.
1. Válassza ki a fájl nevét és az Exportálás helyét, majd kattintson a **tovább** gombra.
1. Válassza a **Befejezés** lehetőséget, és helyezze át az exportált tanúsítványt egy biztonságos helyre.

## <a name="add-the-certificate-to-a-firewall-policy"></a>Tanúsítvány hozzáadása a tűzfal házirendjéhez

1. A Azure Portalban navigáljon a Key Vault tanúsítványok lapjára, és válassza a **Létrehozás/importálás** lehetőséget.
1. Válassza az **Importálás** a létrehozási módszerként lehetőséget, nevezze el a tanúsítványt, válassza ki az exportált. pfx fájlt, adja meg a jelszót, majd válassza a **Létrehozás** lehetőséget.
1. Navigáljon a tűzfal-házirend **TLS-ellenőrzés (előzetes verzió)** lapjára, és válassza ki a felügyelt identitását, Key Vaultét és tanúsítványát. 
1. Kattintson a **Mentés** gombra.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/tls-inspection.png" alt-text="TLS-ellenőrzés":::

## <a name="validate-tls-inspection"></a>TLS-ellenőrzés ellenőrzése

1. Hozzon létre egy szabályt a TLS-ellenőrzés használatával a célként megadott URL-címre vagy az Ön által választott teljes tartománynévre.  Példa: `*bing.com`.
1. A szabály forrástartományban található tartományhoz csatlakoztatott gépről navigáljon a célhelyre, és válassza a böngésző címsora melletti zárolási szimbólumot. A tanúsítványnak bizonyítania kell, hogy a vállalati HITELESÍTÉSSZOLGÁLTATÓ, nem pedig nyilvános HITELESÍTÉSSZOLGÁLTATÓ adta ki.
1. A tanúsítvány megjelenítése további részletek megjelenítéséhez, beleértve a tanúsítvány elérési útját.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/certificate-details.png" alt-text="tanúsítvány részletei":::
1. Log Analytics futtassa a következő KQL-lekérdezést a TLS-ellenőrzés alá eső összes kérelem visszaküldéséhez:
   ```
   AzureDiagnostics 
   | where ResourceType == "AZUREFIREWALLS" 
   | where Category == "AzureFirewallApplicationRule" 
   | where msg_s contains "Url:" 
   | sort by TimeGenerated desc
   ```
   Az eredmény a megvizsgálandó forgalom teljes URL-címét jeleníti meg: :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/kql-query.png" alt-text="KQL-lekérdezés":::

## <a name="next-steps"></a>Következő lépések

[Azure Firewall Premium Preview a Azure Portal](premium-portal.md)
