---
title: Fürt frissítése a tanúsítvány köznapi nevének használatára
description: Megtudhatja, hogyan alakíthatja át az Azure Service Fabric-fürt tanúsítványát ujjlenyomat-alapú deklarációk és köznapi nevek között.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: f719b1eb39da776827c6babec61e9e6701bb4602
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97900790"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Fürtözött tanúsítványok konvertálása az ujjlenyomat-alapú deklarációk és köznapi nevek között

Egy tanúsítvány aláírása (közismert nevén ujjlenyomat) egyedi. Az ujjlenyomattal deklarált fürtözött tanúsítvány egy tanúsítvány egy adott példányára vonatkozik. Ez a sajátosság a tanúsítványok átváltását, valamint az általános, a bonyolult és a explicit felügyeletet teszi lehetővé. Minden változáshoz szükség van a fürt és a mögöttes számítástechnikai gazdagépek frissítésének előkészítésére.

Az Azure Service Fabric-fürt tanúsítvány-deklarációinak ujjlenyomat alapján történő átalakítása a tanúsítvány tulajdonos köznapi neve (CN) alapján leegyszerűsíti a felügyeletet. Különösen, ha a tanúsítvány átadása már nem igényli a fürt frissítését. Ez a cikk azt ismerteti, hogyan alakíthat át egy meglévő fürtöt a CN-alapú deklarációk leállás nélkül.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>Áthelyezés a hitelesítésszolgáltató által aláírt tanúsítványokra

Annak a fürtnek a biztonsága, amelynek a tanúsítványa ujjlenyomattal van ellátva, az a tény, hogy lehetetlen vagy számítási okokból nem valósítható meg, hogy egy másik, ugyanazzal az aláírással rendelkező tanúsítványt bocsásson ki. Ebben az esetben a tanúsítvány bebizonyította kevésbé fontos, ezért az önaláírt tanúsítványok megfelelőek.

Ezzel szemben annak a fürtnek a biztonsága, amelynek a tanúsítványait a CN-folyamatok deklarálják, a fürt tulajdonosa a tanúsítvány szolgáltatójában van. A szolgáltató az a nyilvános kulcsokra épülő infrastruktúra (PKI) szolgáltatás, amely kiállította a tanúsítványt. A megbízhatóság többek között a PKI minősítési eljárásain alapul, függetlenül attól, hogy az üzemeltetési biztonságot a még egy másik megbízható fél végzi-e, és így tovább.

A fürt tulajdonosának részletes ismeretekkel kell rendelkeznie arról, hogy mely hitelesítésszolgáltatók (CA-k) adják ki a tanúsítványokat, mivel ez alapvető szempont a tanúsítványok tárgy szerinti érvényesítéséhez. Ez azt is jelenti, hogy az önaláírt tanúsítványok teljes mértékben nem alkalmasak erre a célra. Szó szerint bárki létrehozhat egy adott tárgyú tanúsítványt.

A CN által deklarált tanúsítvány általában akkor minősül érvényesnek, ha:

* A lánca sikeresen létrehozható.
* A tulajdonos a várt CN elemet tartalmazta.
* Az érvényesítést végző ügynök megbízhatónak tekinti a kiállítóját (a lánc közvetlen vagy magasabb szintjén).

Service Fabric két módon támogatja a tanúsítványok deklarálása a CN-ben:

* *Implicit* kiállítók esetén, ami azt jelenti, hogy a láncnak egy megbízhatósági kapcsolati horgonyban kell végződnie.
* Az ujjlenyomat által deklarált kiállítók, amelyeket kiállítói rögzítésnek nevezünk.

További információ: [common-name-based Certificate Validation deklarációs](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Ahhoz, hogy egy fürtöt egy, az ujjlenyomat által deklarált önaláírt tanúsítvány használatával alakítsa át a CN-be, a cél, a HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítványnak először ujjlenyomattal kell rendelkeznie a fürtben. Ezt követően az ujjlenyomatról a CN-re való átalakítás lehetséges.

Tesztelési célból az önaláírt *tanúsítványt a* CN deklarálhatja, de csak akkor, ha a kibocsátó a saját ujjlenyomatára van rögzítve. Biztonsági szempontból ez a művelet majdnem egyenértékű a tanúsítvány ujjlenyomattal való deklarálása során. Az ilyen típusú sikeres átalakítás nem garantálja, hogy az ujjlenyomatról a CA által aláírt tanúsítvánnyal történő sikeres átalakítást a CN-be. Javasoljuk, hogy tesztelje a konverziót egy megfelelő, HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítvánnyal. A teszteléshez ingyenes lehetőségek állnak rendelkezésre.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Töltse fel a tanúsítványt, és telepítse a méretezési csoportba

Az Azure-ban a tanúsítványok beszerzéséhez és üzembe helyezéséhez ajánlott módszer Azure Key Vault és annak eszközei. A fürtből álló virtuálisgép-méretezési csoportok minden csomópontján ki kell építeni egy tanúsítványnak megfelelő tanúsítványt. További információ: [Titkok a virtuálisgép-méretezési csoportokban](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm).

Fontos, hogy a fürt tanúsítvány-deklarációjának módosítása előtt telepítse a jelenlegi és a célként megadott fürtöt a fürt minden csomópont-típusának virtuális gépei számára. A tanúsítvány kiállításáról egy Service Fabric csomópontra való kiépítés menetét a [tanúsítvány menete](cluster-security-certificate-management.md#the-journey-of-a-certificate)részletesen tárgyalja.

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>A fürt optimális indítási állapotba hozása

Tanúsítvány-nyilatkozat átalakítása ujjlenyomat alapján – CN-alapú hatások:

- A fürt egyes csomópontjai megkeresik és bemutatják a hitelesítő adataikat más csomópontoknak.
- Hogyan érvényesíti az egyes csomópontok hitelesítő adatait a biztonságos kapcsolat létesítése után.

A folytatás előtt tekintse át [mindkét konfiguráció megjelenítési és ellenőrzési szabályait](cluster-security-certificates.md#certificate-configuration-rules) . A legfontosabb szempont, ha ujjlenyomatok közötti átalakítást végez, és a frissített és még nem frissített csomópontok (azaz a különböző frissítési tartományokhoz tartozó csomópontok) esetében a frissítés során bármikor végre kell hajtani a sikeres kölcsönös hitelesítést. A viselkedés elérésének ajánlott módja, ha a cél vagy a cél tanúsítványát ujjlenyomattal deklarálja egy kezdeti frissítés során. Ezután fejezze be a CN-re való áttérést egy későbbi verzióban. Ha a fürt már egy javasolt indítási állapotban van, akkor kihagyhatja ezt a szakaszt.

A konverziónak több érvényes indítási állapota is van. A Invariant azt mutatja, hogy a fürt már használja a cél tanúsítványt (az ujjlenyomattal deklarálva) a frissítés elején a CN-ra. `GoalCert` `OldCert1` Ezt a cikket figyelembe vesszük, és ebben `OldCert2` a cikkben.

#### <a name="valid-starting-states"></a>Érvényes indítási állapotok

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, ahol `GoalCert` a későbbi, `NotBefore` mint `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, ahol `GoalCert` a későbbi, `NotBefore` mint `OldCert1`

> [!NOTE]
> A 7.2.445 (7,2 CU4) verzió előtt Service Fabric kiválasztotta a legtávolabbi lejáró tanúsítványt (a legtávolabbi "a" detafter "tulajdonsággal rendelkező tanúsítványt), így a fenti kiinduló állapotok 7,2 CU4 megkövetelik, hogy a GoalCert későbbi `NotAfter` dátummal rendelkezzen `OldCert1`

Ha a fürt nem szerepel a korábban leírt érvényes állapotok valamelyikében, tekintse meg a cikk végén található, az állapot elérését ismertető szakaszt.

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>Válassza ki a kívánt CN-alapú tanúsítvány-ellenőrzési sémát

Az előzőekben leírtak szerint Service Fabric támogatja a tanúsítványok deklarálása a CN-ban implicit megbízhatósági kapcsolattal vagy explicit módon a kiállítói ujjlenyomatai megfelelnek. További információ: [common-name-based Certificate Validation deklarációs](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Győződjön meg arról, hogy jól ismeri a különbségeket, és hogy milyen hatással van a mechanizmusok kiválasztására. Ez a különbség vagy a választás a paraméter értéke alapján van meghatározva `certificateIssuerThumbprintList` . Az üres érték azt jelenti, hogy a megbízható legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ (megbízhatósági kapcsolat), míg a ujjlenyomatai megfelelnek-készlet korlátozza a fürtözött tanúsítványok engedélyezett közvetlen kibocsátóit.

   > [!NOTE]
   > A `certificateIssuerThumbprint` mező segítségével megadhatja a tulajdonos CN által deklarált tanúsítványok várt közvetlen kibocsátóit. Az elfogadható értékek egy vagy több vesszővel tagolt SHA1-ujjlenyomatai megfelelnek. Ez a művelet erősíti a tanúsítvány érvényesítését.
   >
   > Ha nem ad meg kiállítót, vagy a lista üres, akkor a rendszer elfogadja a tanúsítványt a hitelesítéshez, ha a lánca felépíthető. A tanúsítvány ezután egy, az érvényesítő által megbízhatónak tartott gyökérben végződik. Ha meg van adva egy vagy több kiállító ujjlenyomatai megfelelnek, a rendszer elfogadja a tanúsítványt, ha a közvetlen kiállítójának a láncból kinyert ujjlenyomata megegyezik az ebben a mezőben megadott értékekkel. A tanúsítvány el lesz fogadva, hogy a gyökér megbízható-e.
   >
   > A nyilvános kulcsokra épülő infrastruktúra különböző hitelesítésszolgáltatók (más *néven kiállítók) használatával* aláírja a tanúsítványokat egy adott tárgyhoz. Ezért fontos megadnia a tárgyhoz tartozó összes várható kiállítói ujjlenyomatai megfelelnek. Más szóval a tanúsítvány megújítása nem garantált, hogy ugyanazzal a kibocsátóval aláírja a megújítani kívánt tanúsítványt.
   >
   > A kibocsátó meghatározása ajánlott eljárásnak minősül. Ha kihagyja a kiállítót, továbbra is működni fog a megbízható legfelső szintű tanúsítványok esetében, de ez a viselkedés korlátozásokat tartalmaz, és a közeljövőben fokozatosan elvégezhető. Az Azure-ban üzembe helyezett fürtök, amelyek a privát PKI által kiadott X509-tanúsítványokkal vannak védve, és előfordulhat, hogy a tulajdonos nem tudja érvényesíteni a Service Fabric (a fürtök és a szolgáltatások közötti kommunikáció esetében). Az érvényesítéshez a PKI tanúsítvány-szabályzatának felderíthetőnek, elérhetőnek és elérhetőnek kell lennie.

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>A fürt Azure Resource Manager sablonjának frissítése és üzembe helyezése

A Service Fabric-fürtöket Azure Resource Manager (ARM) sablonokkal kezelheti. Egy alternatív megoldás, amely JSON-összetevőket is használ, a [Azure erőforrás-kezelő (előzetes verzió)](https://resources.azure.com). Jelenleg nem érhető el ezzel egyenértékű felület a Azure Portal.

Ha egy meglévő fürthöz tartozó eredeti sablon nem érhető el, akkor a Azure Portalban egy egyenértékű sablon szerezhető be. Nyissa meg a fürtöt tartalmazó erőforráscsoportot, és válassza a bal oldali **Automation** menüjének **sablon exportálása** elemét. Ezután válassza ki a kívánt erőforrásokat. A virtuálisgép-méretezési csoportnak és a fürt erőforrásainak legalább a exportálását el kell vinni. A létrehozott sablon is letölthető. Ennek a sablonnak a teljes üzembe helyezése előtt módosítania kell a módosításokat. Előfordulhat, hogy a sablon nem egyezik pontosan az eredetivel. Ez tükrözi a fürterőforrás aktuális állapotát.

A szükséges módosítások a következők:

- A Service Fabric csomópont-bővítmény definíciójának frissítése (a virtuális gép erőforrása alatt). Ha a fürt több csomópont-típust határoz meg, akkor frissítenie kell a megfelelő virtuálisgép-méretezési csoport definícióját.
- A fürt erőforrás-definíciójának frissítése.

Itt részletes példákat talál.

### <a name="update-the-virtual-machine-scale-set-resources"></a>Virtuálisgép-méretezési csoport erőforrásainak frissítése
Forrás:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
Címzett:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="update-the-cluster-resource"></a>A fürterőforrás frissítése

A **Microsoft. ServiceFabric/Clusters** erőforrásban adjon hozzá egy **certificateCommonNames** tulajdonságot egy **commonNames** -beállítással, és távolítsa el a **tanúsítvány** tulajdonságot (az összes beállítását).

Forrás:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
Címzett:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

További információ: Service Fabric- [fürt üzembe helyezése, amely az ujjlenyomat helyett a tanúsítvány köznapi nevét használja](./service-fabric-create-cluster-using-cert-cn.md).

## <a name="deploy-the-updated-template"></a>A frissített sablon üzembe helyezése

A módosítások elvégzése után telepítse újra a frissített sablont.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>A fürtnek a CN-alapú tanúsítványok deklarációjában való átalakításához érvényes indítási állapot elérése

| Indítás állapota | 1. frissítés | 2. frissítés |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` és `GoalCert` egy későbbi `NotBefore` dátummal rendelkezik, mint `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` és `OldCert1` egy későbbi `NotBefore` dátummal rendelkezik, mint `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, ahol `OldCert1` egy későbbi `NotBefore` dátummal rendelkezik, mint `GoalCert` | Frissítés erre `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, ahol `OldCert1` egy későbbi `NotBefore` dátummal rendelkezik, mint `GoalCert` | Frissítés erre `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Az egyik `OldCert1` vagy az `OldCert2` állapot eltávolítása `Thumbprint: OldCertx, ThumbprintSecondary: None` | Folytatás az új kezdő állapotból |

> [!NOTE]
> A 7.2.445 (7,2 CU4) verzió előtti verziókban lévő fürtök esetében cserélje le a `NotBefore` `NotAfter` -t a fenti állapotokra.

A frissítések bármelyikének végrehajtásával kapcsolatos útmutatásért lásd: [tanúsítványok kezelése Azure Service Fabric-fürtben](service-fabric-cluster-security-update-certs-azure.md).

## <a name="next-steps"></a>Következő lépések

* További információ a [fürt biztonságáról](service-fabric-cluster-security.md).
* Megtudhatja, hogyan lehet áttekinteni [a fürt tanúsítványát köznapi név szerint](service-fabric-cluster-rollover-cert-cn.md).
* Megtudhatja, hogyan [konfigurálhat egy fürtöt az érintéses autorolloverhez](cluster-security-certificate-management.md).

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
