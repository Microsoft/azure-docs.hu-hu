---
title: Az SNMP MIB-monitorozás beállítása
description: Az érzékelő állapotának figyelése az SNMP használatával végezhető el. Az érzékelő válaszol az egy jogosult figyelő kiszolgálóról küldött SNMP-lekérdezésekre.
ms.date: 12/14/2020
ms.topic: how-to
ms.openlocfilehash: 1ba52236f65c6c5daba68c67677cdc6adfb699b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781670"
---
# <a name="set-up-snmp-mib-monitoring"></a>Az SNMP MIB-monitorozás beállítása

Az érzékelő állapotának figyelését Simple Network Management Protocol (SNMP) használatával végezheti el. Az érzékelő válaszol az egy jogosult figyelő kiszolgálóról küldött SNMP-lekérdezésekre. Az SNMP-figyelő rendszeres időközönként lekérdezi az OID-t (akár 50-szor másodpercenként).

Az SNMP által támogatott verziók az SNMP v2 vagy az SNMP v3. Az SNMP UDP protokollt használ a 161-as porttal (SNMP) rendelkező átviteli protokollként.

Az SNMP-figyelés konfigurálásának megkezdése előtt meg kell nyitnia a tűzfalon a 161-es UDP-portot.

## <a name="oids"></a>OID

| Felügyeleti konzol és érzékelő | OID | Formátum | Leírás |
|--|--|--|--|
| Készülék neve | 1.3.6.1.2.1.1.5.0 | DISPLAYSTRING | A helyszíni felügyeleti konzol berendezésének neve |
| Szállító | 1.3.6.1.2.1.1.4.0 | DISPLAYSTRING | Microsoft ügyfélszolgálata (support.microsoft.com) |
| Platform | 1.3.6.1.2.1.1.1.0 | DISPLAYSTRING | Érzékelő vagy helyszíni felügyeleti konzol |
| Sorozatszám | 1.3.6.1.4.1.9.9.53313.1 | DISPLAYSTRING | A licenc által használt karakterlánc |
| Szoftver verziója | 1.3.6.1.4.1.9.9.53313.2 | DISPLAYSTRING | A Xsense teljes verziójának karakterlánca és teljes körű kezelése |
| Processzorhasználat | 1.3.6.1.4.1.9.9.53313.3.1 | GAUGE32 | Nulla és 100 közötti jelzés |
| CPU hőmérséklete | 1.3.6.1.4.1.9.9.53313.3.2 | DISPLAYSTRING | Celsius-jelzés 100-alapú, Linux-bemeneten alapuló nulla értékre |
| Memóriahasználat | 1.3.6.1.4.1.9.9.53313.3.3 | GAUGE32 | Nulla és 100 közötti jelzés |
| Lemezhasználat | 1.3.6.1.4.1.9.9.53313.3.4 | GAUGE32 | Nulla és 100 közötti jelzés |
| Szolgáltatás állapota | 1.3.6.1.4.1.9.9.53313.5 | DISPLAYSTRING | Online vagy offline állapotú, ha a négy kulcsfontosságú összetevő egyike nem működik |
| Sávszélesség | Hatókörön kívüli 2,4 |  | A Xsense minden egyes figyelő felületén fogadott sávszélesség |

   - A nem létező kulcsok a null, a HTTP 200 [stack overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200)alapján válaszolnak.
    
   - A hardverrel kapcsolatos MIB (CPU-használat, CPU-hőmérséklet, memóriahasználat, lemezhasználat) az összes architektúrán és fizikai érzékelőkön tesztelni kell. A virtuális gépeken a CPU-hőmérséklet várhatóan nem alkalmazható.

Letöltheti az érzékelő által fogadott összes SNMP-lekérdezést tartalmazó naplót, beleértve a kapcsolat adatait és a csomagból származó nyers adatforrásokat is.

Az SNMP v2 állapot-figyelésének meghatározása:

1. Az oldalsó menüben válassza a **Rendszerbeállítások** elemet.

2. Az **aktív felderítés** ablaktáblán válassza az **SNMP MIB-figyelés** elemet :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Az SNMP-ablak szerkesztése.":::

3. Az **engedélyezett gazdagépek** szakaszban válassza a **gazdagép hozzáadása** lehetőséget, és adja meg annak a kiszolgálónak az IP-címét, amely a rendszerállapot-figyelést végzi.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Adja meg az engedélyezett gazdagépek IP-címét.":::

4. A **hitelesítés** szakasz **SNMP v2 közösségi karakterlánc** mezőjébe írja be a karakterláncot. Az SNMP logikai karakterlánc legfeljebb 32 karaktert tartalmazhat, és alfanumerikus karakterek (nagybetűk, kisbetűk és számok) tetszőleges kombinációját tartalmazhatja. A szóköz nem engedélyezett.

5. Kattintson a **Mentés** gombra.

Az SNMP v3 állapot figyelésének meghatározása:

1. Az oldalsó menüben válassza a **Rendszerbeállítások** elemet.

2. Az **aktív felderítés** ablaktáblán válassza az **SNMP MIB-figyelés** elemet :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Az SNMP-ablak szerkesztése.":::

3. Az **engedélyezett gazdagépek** szakaszban válassza a **gazdagép hozzáadása** lehetőséget, és adja meg annak a kiszolgálónak az IP-címét, amely a rendszerállapot-figyelést végzi.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Adja meg az engedélyezett gazdagépek IP-címét.":::

4. A **hitelesítés** szakaszban adja meg a következő paramétereket:

    | Paraméter | Leírás |
    |--|--|
    | **Felhasználónév** | Az SNMP-Felhasználónév legfeljebb 32 karaktert tartalmazhat, és tartalmazhat alfanumerikus karakterek (nagybetűk, kisbetűk és számok) tetszőleges kombinációját. A szóköz nem engedélyezett. <br /> <br />Az SNMP v3 hitelesítés felhasználónevét a rendszeren és az SNMP-kiszolgálón kell konfigurálni. |
    | **Jelszó** | Adjon meg egy kis-és nagybetűket megkülönböztető hitelesítési jelszót. A hitelesítési jelszó 8 – 12 karaktert tartalmazhat, és tartalmazhat alfanumerikus karakterek (nagybetűk, kisbetűk és számok) tetszőleges kombinációját. <br /> <br/>Az SNMP v3 hitelesítés felhasználónevét a rendszeren és az SNMP-kiszolgálón kell konfigurálni. |
    | **Hitelesítési típus** | Válassza az MD5 vagy az SHA lehetőséget. |
    | **Titkosítás** | Válassza a DES vagy az AES lehetőséget. |
    | **Titkos kulcs** | A kulcsnak pontosan nyolc karakterből kell állnia, és tartalmaznia kell alfanumerikus karakterek (nagybetűk, kisbetűk és számok) tetszőleges kombinációját. |

5. Kattintson a **Mentés** gombra.

## <a name="see-also"></a>Lásd még

[Hibaelhárítási naplók exportálása](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
