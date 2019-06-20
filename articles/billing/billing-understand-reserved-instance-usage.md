---
title: Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie die Azure-Reservierung für Ihr Abonnement mit nutzungsbasierter Bezahlung angewendet wird.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: banders
ms.openlocfilehash: 14bd7b61038bf938f7d370eaf6e16d71b5da43ca
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370355"
---
# <a name="understand-azure-reservation-usage-for-your-pay-as-you-go-subscription"></a>Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung

Werten Sie Ihre Nutzung von Reservierungen aus, indem Sie die ReservationId von der [Reservierungsseite](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) und die Nutzungsdatei aus dem [Azure-Kontoportal](https://account.azure.com) verwenden.

Wenn Sie ein Enterprise Agreement-Kunde sind, lesen Sie [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](billing-understand-reserved-instance-usage-ea.md).

In diesem Artikel wird davon ausgegangen, dass die Reservierung auf ein einzelnes Abonnement angewendet wird. Wenn die Reservierung auf mehrere Abonnements angewendet wird, kann sich Ihr Reservierungsvorteil über mehrere CSV-Nutzungsdateien erstrecken.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Nutzung von reservierten VM-Instanzen

In den folgenden Abschnitten wird davon ausgegangen, dass Sie eine Windows-VM des Typs „Standard_DS1_v2“ in der Region „USA, Osten“ ausführen und die Informationen Ihrer reservierten VM-Instanz mit den Angaben in der folgenden Tabelle übereinstimmen:

| Feld | Wert |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Menge |1|
|SKU | Standard_DS1_v2|
|Region | eastus |

Der Hardwarebestandteil des virtuellen Computers ist abgedeckt, da der bereitgestellte virtuelle Computer den Reservierungsattributen entspricht. Wenn Sie ermitteln möchten, welche Windows-Software nicht von der reservierten VM-Instanz abgedeckt wird, lesen Sie [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](billing-reserved-instance-windows-software-costs.md).

### <a name="statement-section-of-csv-file-for-vms"></a>Kostenaufstellungsabschnitt der CSV-Datei für VMs

Dieser Abschnitt Ihrer CSV-Datei zeigt die Gesamtnutzung für Ihre Reservierung. Wenden Sie den Filter auf das Feld **Unterkategorie der Verbrauchseinheit** an, das **"Reservation-"** enthält. Die Anzeige sollte in etwa wie im folgenden Screenshot aussehen:

![Screenshot der gefilterten Details zur Reservierungsnutzung und den Gebühren](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Die Zeile **Reservierte Basis-VM-Instanzen** enthält die Gesamtzahl der Stunden, die durch die Reservierung abgedeckt werden. Diese Zeile enthält den Wert 0,00 USD, weil diese Zeit durch die Reservierung abgedeckt wird. Die Zeile **Reservierte Windows Server-Instanzen (1 Kern)** deckt die Kosten der Windows-Software ab.

### <a name="daily-usage-section-of-csv-file"></a>Abschnitt zur täglichen Nutzung der CSV-Datei

Filtern Sie nach **Zusätzliche Informationen**, und geben Sie Ihre **Reservierungs-ID** ein. Der folgende Screenshot zeigt die Felder, die im Zusammenhang mit der Reservierung stehen.

![Screenshot der Details zu täglicher Nutzung und Kosten](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. Die **ReservationId** im Feld **Zusätzliche Informationen** ist die Reservierung, die auf die VM angewendet wurde.
2. **ConsumptionMeter** ist die Verbrauchseinheit-ID für die VM.
3. Die Zeile **Unterkategorie der Verbrauchseinheit** mit dem Wert **Reservierte Basis-VM-Instanzen** stellt die Kosten von 0,00 USD im Abschnitt „Kostenaufstellung“ dar. Die Kosten für die Ausführung dieser VM werden bereits durch die Reservierung bezahlt.
4. Die **Verbrauchseinheit-ID** ist die Verbrauchseinheit-ID für die Reservierung. Die Kosten für diese Verbrauchseinheit betragen 0,00 USD. Diese Verbrauchseinheit-ID wird für beliebige VMs angezeigt, die für den Reservierungsrabatt qualifiziert sind.
5. Standard_DS1_v2 ist eine vCPU-VM, und der virtuelle Computer wird ohne Azure-Hybridvorteil bereitgestellt. Diese Verbrauchseinheit deckt somit die zusätzlichen Kosten der Windows-Software ab. Die Verbrauchseinheit, die einer VM der D-Serie mit einem Kern entspricht, finden Sie unter [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](billing-reserved-instance-windows-software-costs.md). Wenn Sie Anspruch auf den Azure-Hybridvorteil haben, werden diese zusätzlichen Kosten nicht angewendet.

## <a name="usage-for-sql-database--cosmos-db-reserved-capacity-reservations"></a>Nutzung von Reservierungen von SQL-Datenbank- und Cosmos DB-Kapazitäten

In den folgenden Abschnitten wird der Nutzungsbericht anhand von Azure SQL-Datenbank beschrieben. Mit denselben Schritten können Sie auch die Nutzung für Azure Cosmos DB abrufen.

Es wird davon ausgegangen, dass Sie eine SQL-Datenbank-Gen 4-Instanz in der Region „USA, Osten“ ausführen und Ihre Reservierungsinformationen mit den Angaben in der folgenden Tabelle übereinstimmen:

| Feld | Wert |
|---| --- |
|ReservationId |446ec809-423d-467c-8c5c-bbd5d22906b1|
|Menge |2|
|Produkt| SQL-Datenbank Gen 4 (2 Kerne)|
|Region | eastus |

### <a name="statement-section-of-csv-file"></a>Kostenaufstellungsabschnitt der CSV-Datei

Filtern Sie nach der Messung **Nutzung reservierter Instanzen**. Wählen Sie dann die entsprechende **Kategorie für Messung** aus: Azure SQL-Datenbank oder Azure Cosmos DB. Die Anzeige sollte in etwa wie im folgenden Screenshot aussehen:

![CSV-Datei zu reservierten SQL-Datenbank-Kapazitäten](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

Die Zeile **Nutzung reservierter Instanzen** enthält die Gesamtanzahl der Kernstunden, die durch die Reservierung abgedeckt werden. Der Preis für diese Zeile beträgt 0,00 USD, da die Kosten durch die Reservierung abgedeckt sind.

### <a name="detail-section-of-csv-file"></a>Detailabschnitt der CSV-Datei

Filtern Sie nach **Zusätzliche Informationen**, und geben Sie Ihre **Reservierungs-ID** ein. Der folgende Screenshot zeigt die Felder, die im Zusammenhang mit der Reservierung von SQL-Datenbank-Kapazitäten stehen.

![CSV-Datei zu reservierten SQL-Datenbank-Kapazitäten](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. Die **ReservationId** im Feld **Zusätzliche Informationen** ist die Reservierung von SQL-Datenbank-Kapazitäten, die auf die SQL-Datenbank-Ressource angewendet werden.
2. **ConsumptionMeter** ist die Verbrauchseinheit-ID für die SQL-Datenbank-Ressource.
3. Die **Verbrauchseinheit-ID** ist die Reservierungsverbrauchseinheit. Die Kosten für diese Verbrauchseinheit betragen 0,00 USD. Bei SQL-Datenbank-Ressourcen, die für den Reservierungsrabatt qualifiziert sind, wird diese Verbrauchseinheit-ID in der CSV-Datei angezeigt.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](billing-save-compute-costs-reservations.md)
- [Vorauszahlen für virtuelle Computer mit Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Vorauszahlen von SQL-Datenbank-Computeressourcen mit reservierter Azure SQL-Datenbank-Kapazität](../sql-database/sql-database-reserved-capacity.md)
- [Verwalten von Azure-Reservierungen](billing-manage-reserved-vm-instance.md)
- [Grundlegendes zur Anwendung des Rabatts für Azure-Reservierungen auf virtuelle Computer](billing-understand-vm-reservation-charges.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](billing-understand-reserved-instance-usage-ea.md)
- [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](billing-reserved-instance-windows-software-costs.md)