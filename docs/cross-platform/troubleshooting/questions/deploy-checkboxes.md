---
title: Caselle di controllo Distribuisci disabilitate in Configuration Manager
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: 35efb00a721062ad3217300f7e3a5430b1bd1560
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357752"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Caselle di controllo Distribuisci disabilitate in Configuration Manager

Poiché Xamarin 3.5, i progetti xamarin. IOS distribuiti automaticamente ogni volta che si preme il **avviare** pulsante sulla barra degli strumenti o scegliere il **Debug > Avvia debug** voce di menu. È necessario impostare il progetto di app xamarin. IOS desiderato come la **progetto di avvio** prima di eseguire uno di tali comandi.

Per questo motivo, il **Distribuisci** caselle di controllo vengono disabilitate intenzionalmente in Gestione configurazione di Visual Studio per progetti xamarin. ios:

![](deploy-checkboxes-images/configuration.png "Gestione configurazione di Visual Studio che mostra la casella di controllo 'Distribuisci' disabilitato per un progetto xamarin. IOS in Xamarin 3.5")

Questa modifica consente di eliminare un errore che può essere presenti nelle versioni precedenti di Xamarin (versione 3.3 e versioni precedenti) quando il progetto di app xamarin. IOS non è stato impostato per la distribuzione:

![](deploy-checkboxes-images/error.png "Finestra di dialogo di errore: IPhoneApp1 il progetto deve essere distribuita prima di poter essere avviato. Verificare che il progetto sia selezionato per la distribuzione in Gestione configurazione di soluzione.")
