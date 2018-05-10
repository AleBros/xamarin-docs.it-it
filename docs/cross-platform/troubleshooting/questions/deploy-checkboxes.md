---
title: Distribuire le caselle di controllo disabilitata in Configuration Manager
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: ab825ba4d28ca8768e5c633fc3779828638a498d
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Distribuire le caselle di controllo disabilitata in Configuration Manager

Poiché Xamarin 3.5, i progetti di xamarin. IOS distribuiti automaticamente ogni volta che si preme il **avviare** pulsante della barra degli strumenti o scegliere il **Debug > Avvia debug** voce di menu. Sarà comunque necessario impostare il progetto di app xamarin. IOS desiderato come la **progetto di avvio** prima prima dell'esecuzione di questi comandi.

Per questo motivo, il **Distribuisci** caselle di controllo vengono disabilitate intenzionalmente in Gestione configurazione di Visual Studio per progetti di xamarin. ios:

![](deploy-checkboxes-images/configuration.png "Gestione configurazione di Visual Studio che mostra la casella di controllo 'Distribuisci' disabilitata per un progetto xamarin. IOS in 3.5 Xamarin")

Questa modifica elimina un errore che può essere presenti nelle versioni precedenti di Xamarin (versione 3.3 e versioni precedenti) quando il progetto dell'app xamarin. IOS non è stato impostato per la distribuzione:

![](deploy-checkboxes-images/error.png "Finestra di dialogo di errore: iPhoneApp1 il progetto deve essere distribuito prima di poter essere avviato. Verificare che il progetto sia selezionato per essere distribuito in Gestione configurazione di soluzione.")
