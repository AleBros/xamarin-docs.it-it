---
title: Errore di progettazione con RegisterServicePort iOS
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 62d4a06c62bffb23566f4f59f42a0c980f417c45
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776716"
---
# <a name="ios-designer-error-with-registerserviceport"></a>Errore di progettazione con RegisterServicePort iOS

## <a name="sample-error"></a>Errore di esempio
> System. AggregateException: Uno o più errori---> System. SystemException: RegisterServicePort (com.xamarin.MTHosting.2a0b1, com.apple.PowerManagement.control): Kernel restituito: -308 (-308): (ipc/MIG relativo) server è stata interrotta

## <a name="explanation"></a>Descrizione
Gli errori con `RegisterServicePort` e messaggi di errore simili come sopra sono in genere un problema con spyware/malware nel computer. Provare il [commento in questo report di bug](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4) per ulteriori informazioni, insieme al collegamento al [forum di discussione Apple](https://discussions.apple.com/thread/5596008) su come rimuovere un'infezione dovuta a possibili. 

Per facilitare la diagnosi del problema, aprire l'applicazione macOS **della Console** ed eliminare tutti i file all'interno di **i rapporti di diagnostica di utente** sezione [ http://screencast.com/t/y9i3NKcuMy ](http://screencast.com/t/y9i3NKcuMy). Quindi avviare Visual Studio per Mac e provare a utilizzare la finestra di progettazione. Se i nuovi file di log vengono visualizzate in questa sezione dopo che la finestra di progettazione non è riuscito a inizializzare, salvare questi a Microsoft per l'analisi.  

Si noti l'aspetto più importante da cercare è il file: 
> /usr/lib/libimckit.dylib

Indipendentemente da questi risultati, se il file esiste, il problema di spyware/malware menzionati in precedenza è presente nel computer in uso.  

Il collegamento seguente include i passaggi per rimuovere questo spyware/malware: [http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  

