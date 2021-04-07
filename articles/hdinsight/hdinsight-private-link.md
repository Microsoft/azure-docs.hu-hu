---
title: Azure HDInsight-fürtök biztonságossá tétele és elkülönítése privát kapcsolattal (előzetes verzió)
description: Ismerje meg, hogyan különítheti el az Azure HDInsight-fürtöket egy virtuális hálózaton az Azure Private link használatával.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: bc7834a0f8272da3f8954c7dd9f3e18163795cba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939368"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>Azure HDInsight-fürtök biztonságossá tétele és elkülönítése privát kapcsolattal (előzetes verzió)

Az Azure HDInsight [alapértelmezett virtuális hálózati architektúrájában](./hdinsight-virtual-network-architecture.md)a HDInsight erőforrás-szolgáltató (RP) nyilvános IP-címek használatával kommunikál a fürttel. Egyes esetekben a teljes hálózati elkülönítést a nyilvános IP-címek használata nélkül kell végrehajtani. Ebből a cikkből megtudhatja, milyen speciális vezérlőket használhat a privát HDInsight-fürtök létrehozásához. További információ arról, hogyan korlátozható a fürtre irányuló és onnan érkező forgalom a teljes hálózati elkülönítés nélkül: [a hálózati forgalom szabályozása az Azure HDInsight](./control-network-traffic.md).

Létrehozhat privát HDInsight-fürtöket egy Azure Resource Manager-(ARM-) sablonban megadott hálózati tulajdonságok konfigurálásával. A privát HDInsight-fürtök létrehozásához két tulajdonságot használhat:

* Távolítsa el a nyilvános IP-címeket a kimenő érték beállításával `resourceProviderConnection` .
* Engedélyezze az Azure privát hivatkozását, és használjon [privát végpontokat](../private-link/private-endpoint-overview.md) az engedélyezés beállítással `privateLink` .

## <a name="remove-public-ip-addresses"></a>Nyilvános IP-címek eltávolítása

Alapértelmezés szerint a HDInsight RP a nyilvános IP-címek használatával *bejövő* kapcsolatokat használ a fürthöz. Ha a `resourceProviderConnection` Network tulajdonság *kimenő* értékre van állítva, megfordítja a kapcsolatot a HDInsight RP-vel, hogy a kapcsolatok mindig a fürtön BELÜLről a RP-re legyenek kezdeményezve. Bejövő kapcsolatok nélkül nem szükséges a bejövő szolgáltatáshoz tartozó címkék vagy nyilvános IP-címek használata.

Az alapértelmezett virtuális hálózati architektúrában használt alapszintű terheléselosztó automatikusan nyilvános NAT-t (hálózati címfordítást) biztosít a szükséges kimenő függőségek, például a HDInsight RP eléréséhez. Ha korlátozni szeretné a nyilvános internetre irányuló kimenő kapcsolatot, beállíthatja [a tűzfalat](./hdinsight-restrict-outbound-traffic.md), de ez nem követelmény.

A `resourceProviderConnection` kimenő értékre való konfigurálás lehetővé teszi a fürtre jellemző erőforrások, például a Azure Data Lake Storage Gen2 vagy a külső metaadattárak elérését privát végpontok használatával. Ha saját végpontokat használ ezekre az erőforrásokra vonatkozóan, nem kötelező, de ha saját végpontokat kíván használni ezekhez az erőforrásokhoz, konfigurálnia kell a HDInsight-fürtöt létrehozó privát végpontokat és DNS-bejegyzéseket `before` . Javasoljuk, hogy hozza létre és adja meg az összes szükséges külső SQL-adatbázist, például az Apache Rangert, a Ambari, a Oozie és a kaptár metaadattárak a fürt létrehozási ideje alatt. A követelmény, hogy az összes erőforrásnak elérhetőnek kell lennie a fürt alhálózatán belülről, vagy a saját privát végpontján keresztül vagy más módon.

A Azure Key Vault magánhálózati végpontok használata nem támogatott. Ha Azure Key Vaultt használ a CMK-titkosításhoz, akkor a Azure Key Vault végpontnak elérhetőnek kell lennie a HDInsight alhálózaton belül a privát végpont nélkül.

A következő ábra azt mutatja be, hogy milyen lehetséges HDInsight virtuális hálózati architektúra hasonlít a `resourceProviderConnection` kimenő értékre:

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="HDInsight-architektúra diagramja kimenő erőforrás-szolgáltatói kapcsolatok használatával":::

A fürt létrehozása után megfelelő DNS-feloldást kell beállítania. A rendszer az Azure által felügyelt nyilvános DNS-zónában hozza létre a következő kanonikus név DNS-rekordot (CNAME): `azurehdinsight.net` .

```dns
<clustername>    CNAME    <clustername>-int
```

A fürt teljes tartománynevek használatával való eléréséhez használhatja a belső terheléselosztó magánhálózati IP-címeket közvetlenül, vagy használhatja a saját saját DNS zónáját, hogy az igényeinek megfelelően felülbírálja a fürt végpontját. Rendelkezhet például egy saját DNS zónával `azurehdinsight.net` . és szükség szerint adja hozzá a saját IP-címeit:

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>Privát hivatkozás engedélyezése

A privát hivatkozás, amely alapértelmezés szerint le van tiltva, széles körű hálózatkezelési ismereteket igényel a felhasználó által megadott útvonalak (UDR) és a tűzfalszabályok megfelelő beállításához a fürt létrehozása előtt. Ha ezt a beállítást nem kötelező használni, de csak akkor érhető el, ha a `resourceProviderConnection` Network tulajdonság az előző szakaszban leírtak szerint a *kimenő* értékre van állítva.

Ha `privateLink` az *engedélyezve* értékre van állítva, a rendszer belső [standard Load balancereket](../load-balancer/load-balancer-overview.md) (SLB-ket) hoz létre, és minden egyes SLB kiépít egy Azure Private link Service-t. A Private link Service lehetővé teszi, hogy a HDInsight-fürtöt privát végpontokból elérje.

A standard Load balancerek nem biztosítják automatikusan a [nyilvános kimenő NAT](../load-balancer/load-balancer-outbound-connections.md) -t, például az alapszintű Load balancert. A kimenő függőségekhez meg kell adnia a saját NAT-megoldását, például [Virtual Network NAT](../virtual-network/nat-overview.md) -t vagy [tűzfalat](./hdinsight-restrict-outbound-traffic.md). A HDInsight-fürtnek továbbra is hozzá kell férnie a kimenő függőségeihez. Ha ezek a kimenő függőségek nem engedélyezettek, a fürt létrehozása sikertelen lehet.

### <a name="prepare-your-environment"></a>A környezet előkészítése

A Private link Services sikeres létrehozásához explicit módon [le kell tiltania a magánhálózati kapcsolati szolgáltatáshoz tartozó hálózati házirendeket](../private-link/disable-private-link-service-network-policy.md).

A következő ábrán egy példa látható a fürt létrehozása előtt szükséges hálózati konfigurációra. Ebben a példában az összes kimenő forgalom Azure Firewall UDR használatával van [kényszerítve](../firewall/forced-tunneling.md) , és a szükséges kimenő függőségeknek "engedélyezett"nek kell lenniük a tűzfalon a fürt létrehozása előtt. Enterprise Security Package-fürtök esetében a VNet-társítással a Azure Active Directory Domain Services hálózati kapcsolata is elérhető.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Privát kapcsolati környezet diagramja a fürt létrehozása előtt":::

A hálózatkezelés beállítása után létrehozhat egy olyan fürtöt, amelyen engedélyezve van a kimenő erőforrás-szolgáltatói kapcsolat és a privát hivatkozás, ahogy az az alábbi ábrán is látható. Ebben a konfigurációban nincsenek nyilvános IP-címek és magánhálózati kapcsolati szolgáltatás az egyes standard Load balancerekhez.

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="Privát kapcsolati környezet ábrája a fürt létrehozása után":::

### <a name="access-a-private-cluster"></a>Hozzáférés egy privát fürthöz

A privát fürtök eléréséhez használhatja a belső terheléselosztó magánhálózati IP-címeit közvetlenül, vagy használhat magánhálózati DNS-bővítményeket és magánhálózati végpontokat is. Ha a `privateLink` beállítás engedélyezve értékre van állítva, létrehozhat saját privát végpontokat, és konfigurálhatja a DNS-feloldást MAGÁNHÁLÓZATI DNS-zónákon keresztül.

Az Azure által felügyelt nyilvános DNS-zónában létrehozott privát hivatkozás bejegyzései a `azurehdinsight.net` következők:

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

Az alábbi képen egy példa látható a fürt olyan virtuális hálózatról való eléréséhez szükséges magánhálózati DNS-bejegyzésekre, amely nem független, vagy nem rendelkezik közvetlen vonallal a fürt terheléselosztó számára. Az Azure Private Zone használatával felülbírálhatja a `*.privatelink.azurehdinsight.net` teljes tartományneveket, és feloldható a saját privát végpontok IP-címeire.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Privát kapcsolati architektúra ábrája":::

## <a name="how-to-create-clusters"></a>Fürtök létrehozása
### <a name="use-arm-template-properties"></a>ARM-sablon tulajdonságainak használata

A következő JSON-kódrészlet tartalmazza az ARM-sablonban konfigurálni kívánt két hálózati tulajdonságot egy privát HDInsight-fürt létrehozásához.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

A HDInsight nagyvállalati biztonsági funkcióival, beleértve a privát hivatkozásokat is, a [HDInsight Enterprise biztonsági sablon](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)című témakörben talál teljes sablont.

### <a name="use-azure-powershell"></a>Azure PowerShell használatával

A PowerShell használatához tekintse meg a [következő példát.](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)

### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával
Az Azure CLI használatához tekintse meg a [következő példát:](/cli/azure/hdinsight#az_hdinsight_create-examples).

## <a name="next-steps"></a>Következő lépések

* [Enterprise Security Package az Azure HDInsight](enterprise-security-package.md)
* [A vállalati biztonsági általános információk és irányelvek az Azure HDInsight](./domain-joined/general-guidelines.md)
