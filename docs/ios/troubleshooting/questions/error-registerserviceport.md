---
title: Errore di iOS Designer con RegisterServicePort
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 815eb638b3e394dcfe4b60d716820c610b01f8a1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769329"
---
# <a name="ios-designer-error-with-registerserviceport"></a>Errore di iOS Designer con RegisterServicePort

## <a name="sample-error"></a>Errore di esempio
> System.AggregateException: Si sono verificati uno o più errori---> System. SystemException: RegisterServicePort (com. Novell. MTHosting. 2a0b1, com. Apple. PowerManagement. Control): Kernel restituito:-308 (-308): (IPC/MIG) Server è morto

## <a name="explanation"></a>Spiegazione
Gli errori `RegisterServicePort` con e messaggi di errore simili, come sopra, rappresentano in genere un problema con spyware/malware nel computer. Per ulteriori informazioni, prendere in considerazione il [commento sul report sui bug](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4) , insieme al collegamento alla [discussione sul forum Apple](https://discussions.apple.com/thread/5596008) su come rimuovere una possibile infezione. 

Per facilitare la diagnosi del problema, aprire la **console** dell'applicazione MacOS ed eliminare tutti i file nella [http://screencast.com/t/y9i3NKcuMy](http://screencast.com/t/y9i3NKcuMy)sezione **report di diagnostica utente** . Quindi avviare Visual Studio per Mac e provare a usare la finestra di progettazione. Se in questa sezione sono presenti nuovi file di log che non sono stati inizializzati dalla finestra di progettazione, salvarli per l'analisi.  

Si noti che la cosa più importante da verificare è questo file: 
> /usr/lib/libimckit.dylib

Indipendentemente dai risultati precedenti, se il file esiste, il problema relativo a spyware/malware è presente nel computer.  

Il collegamento seguente include i passaggi per rimuovere questo spyware/malware:[http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  
