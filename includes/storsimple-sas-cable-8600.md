---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 51e1fd18b52d7e215ba43be540156199fb41778e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178125"
---
#### <a name="to-attach-the-sas-cables"></a>So schließen Sie die SAS-Kabel an
1. Identifizieren Sie das primäre Gehäuse und das EBOD-Gehäuse. Die beiden Gehäuse können anhand ihrer Rückwände identifiziert werden. Die folgende Abbildung bietet eine Orientierung. 
   
    ![Rückseite des primären Gehäuses und des EBOD-Gehäuses](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Rückseite des primären Gehäuses und des EBOD-Gehäuses**
   
   | Bezeichnung | BESCHREIBUNG |
   |:--- |:--- |
   | 1 |Primäres Gehäuse |
   | 2 |EBOD-Gehäuse |
2. Suchen Sie am primären und am EBOD-Gehäuse nach der jeweiligen Seriennummer. Der Aufkleber mit der Seriennummer befindet sich jeweils an der hinteren Lasche des Gehäuses. Die Seriennummern beider Gehäuse müssen identisch sein. [nehmen Sie sofort Kontakt mit dem Microsoft-Support auf](../articles/storsimple/storsimple-contact-microsoft-support.md) . Die folgende Abbildung zeigt die Position der Seriennummern.
   
    ![Rückansicht des Gehäuses mit Seriennummer](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Position des Aufklebers mit der Seriennummer**
   
   | Bezeichnung | BESCHREIBUNG |
   |:--- |:--- |
   | 1 |Lasche des Gehäuses |
3. Im nächsten Schritt verwenden Sie die mitgelieferten SAS-Kabel, um das EBOD-Gehäuse folgendermaßen mit dem primären Gehäuse zu verbinden:
   
   1. Im nächsten Schritt identifizieren Sie die vier SAS-Anschlüsse am primären und am EBOD-Gehäuse. Die SAS-Anschlüsse sind am primären Gehäuse mit „EBOD“beschriftet und entsprechen dem Anschluss A am EBOD-Gehäuse, wie unten in der Abbildung zur SAS-Verkabelung dargestellt.
   2. Verwenden Sie die mitgelieferten SAS-Kabel, um den EBOD-Anschluss mit dem Anschluss A zu verbinden.
   3. Der EBOD-Anschluss am Controller 0 sollte mit dem Anschluss A am EBOD-Controller 0 verbunden sein. Der EBOD-Anschluss am Controller 1 sollte mit dem Anschluss A am EBOD-Controller 1 verbunden sein. Der Vorgang wird in der folgenden Abbildung veranschaulicht. 
      
      ![SAS-Verkabelung des Geräts](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS-Verkabelung**
      
      | Bezeichnung | BESCHREIBUNG |
      |:--- |:--- |
      | Eine Datei |Primäres Gehäuse |
      | b |EBOD-Gehäuse |
      | 1 |Controller 0 |
      | 2 |Controller 1 |
      | 3 |EBOD-Controller 0 |
      | 4 |EBOD-Controller 1 |
      | 5, 6 |SAS-Anschlüsse am primären Gehäuse (mit der Beschriftung "EBOD") |
      | 7, 8 |SAS-Anschlüsse am EBOD-Gehäuse (Anschluss A) |

