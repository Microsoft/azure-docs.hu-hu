---
title: Microsoft-partneri házirend
titleSuffix: Azure
description: Microsoft-partneri házirend
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bee41bb8e5beb4df3086ab50499cb185a83e4efe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592329"
---
# <a name="peering-policy"></a>Társviszony-létesítési szabályzat
A Microsoft egy szelektív, az iparági szabványoknak és az ajánlott eljárásoknak megfelelő felhasználói élményt biztosít, amely a jövőbeli igények és a társítások stratégiai elhelyezésének biztosítására szolgál. Ennek megfelelően a Microsoft fenntartja a jogot arra, hogy a szükségesnek ítélt kivételeket hozzon a szabályzathoz. A Microsoft általános követelményeit a hálózatról az alábbi részben ismertetjük. Ezek a közvetlen társ-és Exchange-kérelmekre egyaránt érvényesek. 

## <a name="technical-requirements"></a>Technikai követelmények

* Egy teljesen redundáns hálózat, amely elegendő kapacitással rendelkezik a forgalom zsúfoltság nélküli cseréjéhez.
* A társ nyilvánosan irányítható autonóm rendszerszámmal (ASN) fog rendelkezni.
* Mind az IPv4-, mind az IPv6-támogatás támogatott, és a Microsoft mindkét típusú munkamenetet arra vár, hogy minden egyes egyenrangú helyen.
* Az MD5 nem támogatott.
* **ASN-részletek:**

    * A Microsoft a következő ASN együtt kezeli a AS8075: AS8068, AS8069, AS12076. A AS8075-társítással rendelkező ASN teljes listájáért TEKINTse meg a MICROSOFTra vonatkozó hivatkozást.
    * A Microsofttal folytatott összes fél egyetért azzal, hogy nem fogad el útvonalakat a AS12076 (Express Route)-től semmilyen körülmények között, és minden társon ki kell szűrnie a AS12076.

* **Útválasztási házirend:**
    * A társnak legalább egy nyilvánosan irányítható/24 szintűnek kell lennie.
    * A Microsoft felülírja a beérkezett többszörös kilépéseket (MED).
    * A Microsoft szívesebben kap BGP-közösségi címkéket a társaitól, hogy jelezze az útvonal eredetét.
    * Azt javasoljuk, hogy a partnereknek a Microsofttal való összekapcsolási munkamenetek esetén a 2000 (IPv4) és a 500 (IPv6) útvonalak maximális előtagját állítsa be.
    * Ha előzetesen nem határoz meg külön megállapodást, a partnereknek minden olyan helyen be kell jelenteniük a konzisztens útvonalakat, ahol a Microsofttal foglalkoznak.
    * Általánosságban elmondható, hogy a AS8075-vel folytatott társ-munkamenetek az összes MICROSOFT-útvonalat hirdetik. A Microsoft bizonyos regionális sajátosságokat jelenthet.
    * Egyik fél sem hoz létre statikus útvonalat, a Last Resort útvonalát, vagy más fél számára a BGP-n keresztül nem bejelentett útvonalon továbbítja a forgalmat.
    * A partnernek regisztrálnia kell az útvonalait egy nyilvános internetes útválasztási jegyzék-(IRR-) adatbázisban a szűrés céljából, és naprakészen fogja tartani ezeket az adatokat.      
    * A társai a MANRS iparági szabványoknak is eleget tesznek az útválasztás biztonsága érdekében.  Saját belátása szerint a Microsoft dönthet úgy, hogy: 1.) nem hoz létre olyan vállalatokat, amelyek nem rendelkeznek aláírt és regisztrált útvonalakkal; 2.) az érvénytelen RPKI útvonalak eltávolításához; 3.) nem fogad el útvonalakat olyan létrehozott partnerekről, amelyek nincsenek regisztrálva és aláírva. 

## <a name="operational-requirements"></a>Üzemeltetési követelmények
* Egy teljes körűen felhasználható nonstop hálózati operatív központ (NOC), amely segítséget nyújt az összes műszaki és teljesítménnyel kapcsolatos probléma, a biztonsági szabálysértések, a szolgáltatásmegtagadási támadások elutasítása vagy a társon vagy az ügyfeleken kívüli visszaélések megoldásában.
* A partnereknek a [PeeringDB](https://www.peeringdb.com) teljes és naprakész profillal kell rendelkezniük, beleértve a vállalati tartományból és a telefonszámból származó nonstop Noc-e-maileket. Ezen információk alapján érvényesítjük a partner adatait, például a NOC-információkat, a technikai kapcsolattartási adatokat és azok jelenlétét a társas létesítményekben stb. A személyes Yahoo-, Gmail-és Hotmail-fiókok nem fogadhatók el.

## <a name="physical-connection-requirements"></a>Fizikai kapcsolatok követelményei
* A [PeeringDB](https://www.peeringdb.com/net/694) -ben a Microsofttal a közvetlen társ-vagy Exchange-kapcsolattal való kommunikációhoz használható webhelyek listája szerepel.

* **Exchange-társ:**
    * A partnereknek várhatóan legalább 10 GB-os kapcsolatot kell létesíteniük az Exchange-kiszolgálóval.
    * A társak a portok frissítéséhez szükségesek, amikor a csúcsérték kihasználtsága meghaladja a 50%-ot.
    * A Microsoft arra biztatja a partnereket, hogy a feladatátvételi forgatókönyvek támogatása érdekében az Exchange-hez való különböző kapcsolatok maradjanak.

* **Közvetlen társítás:**
    * Az összekapcsolásnak 100 GB/s-os optika használatával egy egymódusú szálon kell lennie.
    * A Microsoft csak az INTERNETSZOLGÁLTATÓval vagy a hálózati szolgáltatókkal hoz létre közvetlen kapcsolatot.
    * A főkiszolgálók a portok frissítését várhatóan akkor is meghaladják, ha a maximális kihasználtság meghaladja a 50%-ot, és az egyes metrókban a különböző kapacitásokat, akár egy helyen, akár a metró több pontján belül megtart
    * Az egyes közvetlen társítások két, két Microsoft Edge-útválasztóból álló kapcsolattal rendelkeznek a társának peremhálózati útválasztói között. A Microsoft kettős BGP-munkameneteket igényel ezeken a kapcsolatokon keresztül. A társ dönthet úgy, hogy nem helyez üzembe redundáns eszközöket a végén.


## <a name="traffic-requirements"></a>Forgalmi követelmények

* Az Exchange-kapcsolaton keresztüli társak esetében legalább 500 MB-os forgalomnak és 2 GB-nál kisebbnek kell lennie. A 2 GB-os közvetlen társítást meghaladó forgalom esetében meg kell határozni a forgalmat.
* A Microsoft számára legalább 2 GB szükséges a közvetlen társításhoz. A egymással kölcsönösen megállapodott helyeknek támogatniuk kell a feladatátvételt, amely biztosítja, hogy a társítás a feladatátvételi forgatókönyv során is honosítva maradjon. 

## <a name="next-steps"></a>Következő lépések

* Ha szeretne többet megtudni a Microsofttal való közvetlen együttműködés beállításához szükséges lépésekről, kövesse a közvetlen összevonási [útmutató](walkthrough-direct-all.md)utasításait.
* A Microsofttal való Exchange-társítás beállításával kapcsolatos lépésekért kövesse az [Exchange-partneri útmutató](walkthrough-exchange-all.md)című témakört.
