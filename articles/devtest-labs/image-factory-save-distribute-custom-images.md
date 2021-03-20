---
title: Képek mentése és terjesztése a Azure DevTest Labsban | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan mentheti az egyéni lemezképeket a már létrehozott virtuális gépekről Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5278626f8cdd4299912f3c952786422436fe916
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85476240"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Egyéni rendszerképek mentése és továbbítása több tesztkörnyezetbe
Ez a cikk a már létrehozott virtuális gépekről (VM) származó egyéni lemezképek mentésének lépéseit ismerteti. Azt is ismerteti, hogyan terjesztheti ezeket az egyéni rendszerképeket a szervezet más DevTest Labs szolgáltatásában.

## <a name="prerequisites"></a>Előfeltételek
A következő elemeket már meg kell adni:

- A Azure DevTest Labsban található rendszerkép-előállító laborja.
- Egy Azure DevOps-projekt, amely a rendszerkép-előállító automatizálására szolgál.
- A szkripteket és a konfigurációt tartalmazó forráskód helye (példánkban az előző lépésben említett DevOps-projektben).
- Hozzon létre definíciót az Azure PowerShell-feladatok előkészítéséhez.

Ha szükséges, kövesse az [Azure DevOps a rendszerkép-előállító futtatása](image-factory-set-up-devops-lab.md) az alábbi elemek létrehozásához vagy beállításához című témakör lépéseit. 

## <a name="save-vms-as-generalized-vhds"></a>Virtuális gépek mentése általánosított virtuális merevlemezként
Mentse a meglévő virtuális gépeket általánosított virtuális merevlemezként.  Létezik egy PowerShell-parancsfájl, amely a meglévő virtuális gépeket általánosított virtuális merevlemezként menti. Először vegyen fel egy másik **Azure PowerShell** -feladatot a Build-definícióba az alábbi ábrán látható módon:

![Azure PowerShell lépés hozzáadása](./media/save-distribute-custom-images/powershell-step.png)

Ha az új feladat szerepel a listában, válassza ki az elemet, hogy az alábbi képen látható módon kitöltse az összes adatot: 

![PowerShell-beállítások](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Általánosított és speciális Egyéni rendszerképek
Ha egy virtuális gépről egyéni rendszerképet hoz létre, akkor a [Azure Portal](https://portal.azure.com)általánosított vagy speciális egyéni rendszerképet választhat.

- **Speciális egyéni rendszerkép:** A Sysprep/megszüntetés nem fut a gépen. Ez azt jelenti, hogy a rendszerkép az operációsrendszer-lemez pontos másolata a meglévő virtuális gépen (egy pillanatkép).  Ugyanezek a fájlok, alkalmazások, felhasználói fiókok, számítógépnév stb. mind jelennek meg, amikor új gépet hozunk létre ebből az egyéni rendszerképből.
- **Általánosított egyéni rendszerkép:** A Sysprep/megszüntetés a gépen futott.  A folyamat futtatásakor eltávolítja a felhasználói fiókokat, eltávolítja a számítógép nevét, kiszűri a felhasználói beállításjegyzék-struktúrákat stb., a rendszerkép általánosításának céljával, hogy testre lehessen szabni egy másik virtuális gép létrehozásakor.  Amikor általánosít egy virtuális gépet (a Sysprep futtatásával), a folyamat megsemmisíti az aktuális virtuális gépet – a továbbiakban nem lesz működőképes.

Az Egyéni rendszerképek a rendszerkép-Előállítóban való illesztésére szolgáló parancsfájl az előző lépésben létrehozott virtuális gépek számára menti a VHD-ket (az Azure-beli erőforrás címkéje alapján azonosítva).

## <a name="update-configuration-for-distributing-images"></a>Lemezképek terjesztésére szolgáló konfiguráció frissítése
A folyamat következő lépéseként le kell küldenie az egyéni rendszerképeket a rendszerkép-előállító laborból minden más olyan laborba, amelyre szükségük van. Ennek a folyamatnak a fő része a konfigurációs fájl **labs.js** . Ezt a fájlt a rendszerkép-előállítóban található **konfigurációs** mappában találja.

A konfigurációs fájl labs.jsjában két fő dolog szerepel:

- Egyedi módon azonosít egy adott cél labort az előfizetés-azonosító és a labor neve alapján.
- Azoknak a lemezképeknek a készletét, amelyeket a rendszer a konfiguráció gyökeréhez viszonyított elérési utakként küld a laborba. Teljes mappát is megadhat (az adott mappában található összes rendszerkép lekéréséhez).

Íme egy példa labs.jsa két Labs-listával rendelkező fájlra. Ebben az esetben két különböző laborba terjeszti a lemezképeket.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Összeállítási feladat létrehozása
A cikkben korábban látott lépéseket követve hozzáadhat egy további **Azure PowerShell** -felépítési feladatot a definíció létrehozásához. Adja meg a részleteket az alábbi képen látható módon: 

![Lemezképek terjesztésére szolgáló feladat létrehozása](./media/save-distribute-custom-images/second-build-task-powershell.png)

A paraméterek a következők: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Ez a feladat a rendszerkép-előállítóban található összes egyéni rendszerképet elküldi a fájl Labs.jsjában meghatározott laboroknak.

## <a name="queue-the-build"></a>A Build várólistája
A terjesztési felépítési feladat befejezése után egy új buildet állít be, amely gondoskodik arról, hogy minden működik. A létrehozás sikeres befejezése után az új egyéni lemezképek megjelennek a cél laborban, amely be lett írva a Labs.jsa konfigurációs fájlban.

## <a name="next-steps"></a>Következő lépések
Az adatsorozat következő cikkében a rendszerkép-előállítót a megőrzési szabályzattal és a karbantartási lépésekkel frissítheti: az [adatmegőrzési szabályzat beállítása és a karbantartási parancsfájlok futtatása](image-factory-set-retention-policy-cleanup.md).
