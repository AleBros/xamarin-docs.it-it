---
title: Errore di iOS Designer con RegisterServicePort
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: fc4c143d6b5f7c211d24e6e3ed2ed3bb8d264410
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104727"
---
# <a name="ios-designer-error-with-registerserviceport"></a>Errore di iOS Designer con RegisterServicePort

## <a name="sample-error"></a>Errore di esempio
> System. AggregateException: Uno o più errori si sono verificati---> System. SystemException: RegisterServicePort (com.xamarin.MTHosting.2a0b1, com.apple.PowerManagement.control): Kernel restituito: -308 (-308): server (ipc/saldatrice) è stata interrotta

## <a name="explanation"></a>Descrizione
Gli errori con `RegisterServicePort` e messaggi di errore simile come in precedenza sono in genere un problema con spyware/malware nel computer. Prendere in considerazione la [commento su questo report sui bug](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4) per altre informazioni, insieme al collegamento per il [Apple forum di discussione](https://discussions.apple.com/thread/5596008) su come rimuovere un'infezione dovuta a possibili. 

Per facilitare la diagnosi del problema, aprire l'applicazione di macOS **Console** ed eliminare tutti i file all'interno di **i report di diagnostica utente** sezione [ http://screencast.com/t/y9i3NKcuMy ](http://screencast.com/t/y9i3NKcuMy). Quindi avviare Visual Studio per Mac e provare a usare la finestra di progettazione. Se eventuali nuovi file di log visualizzato in questa sezione dopo che la finestra di progettazione non è riuscito a inizializzare, per salvare queste informazioni a Microsoft per analizzare.  

Tenere presente questo file è più importante per verificare la presenza di: 
> /usr/lib/libimckit.dylib

Indipendentemente dai risultati sopra riportati, se il file esiste, il problema di malware/spyware menzionati in precedenza è presente nel computer.  

Il collegamento seguente include i passaggi per rimuovere malware/spyware. [http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  

