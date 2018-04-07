---
title: Distribuire le caselle di controllo disabilitata in Configuration Manager
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: c0f116565a2741c62a00ed2a255cfde8c57b8569
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Distribuire le caselle di controllo disabilitata in Configuration Manager

Poiché Xamarin 3.5, i progetti di xamarin. IOS distribuiti automaticamente ogni volta che si preme il **avviare** pulsante della barra degli strumenti o scegliere il **Debug > Avvia debug** voce di menu. Sarà comunque necessario impostare il progetto di app xamarin. IOS desiderato come la **progetto di avvio** prima prima dell'esecuzione di questi comandi.

Per questo motivo, il **Distribuisci** caselle di controllo vengono disabilitate intenzionalmente in Gestione configurazione di Visual Studio per progetti di xamarin. ios:

![](deploy-checkboxes-images/configuration.png "Gestione configurazione di Visual Studio che mostra la casella di controllo 'Distribuisci' disabilitata per un progetto xamarin. IOS in 3.5 Xamarin")

Questa modifica elimina un errore che può essere presenti nelle versioni precedenti di Xamarin (versione 3.3 e versioni precedenti) quando il progetto dell'app xamarin. IOS non è stato impostato per la distribuzione:

![](deploy-checkboxes-images/error.png "Finestra di dialogo di errore: iPhoneApp1 il progetto deve essere distribuito prima di poter essere avviato. Verificare che il progetto sia selezionato per essere distribuito in Gestione configurazione di soluzione.")
