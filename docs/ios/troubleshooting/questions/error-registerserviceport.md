---
title: Errore di iOS Designer con RegisterServicePort
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 07b7e47ac448fdb9a4297fe03e8f4ea16295fddd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031130"
---
# <a name="ios-designer-error-with-registerserviceport"></a>Errore di iOS Designer con RegisterServicePort

## <a name="sample-error"></a>Errore di esempio
> System. AggregateException: si sono verificati uno o più errori---> System. SystemException: RegisterServicePort (com. Xamarin.MTHosting. 2a0b1, com. Apple. PowerManagement. Control): kernel restituito:-308 (-308): (IPC/MIG) Server è morto

## <a name="explanation"></a>Descrizione
Gli errori con `RegisterServicePort` e messaggi di errore simili, come sopra, rappresentano in genere un problema con spyware/malware nel computer. Per ulteriori informazioni, prendere in considerazione il [commento sul report sui bug](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4) , insieme al collegamento alla [discussione sul forum Apple](https://discussions.apple.com/thread/5596008) su come rimuovere una possibile infezione. 

Per facilitare la diagnosi del problema, aprire la **console** dell'applicazione MacOS ed eliminare tutti i file nella sezione **report di diagnostica utente** [https://screencast.com/t/y9i3NKcuMy](https://screencast.com/t/y9i3NKcuMy). Quindi avviare Visual Studio per Mac e provare a usare la finestra di progettazione. Se in questa sezione sono presenti nuovi file di log che non sono stati inizializzati dalla finestra di progettazione, salvarli per l'analisi.  

Si noti che la cosa più importante da verificare è questo file: 
> /usr/lib/libimckit.dylib

Indipendentemente dai risultati precedenti, se il file esiste, il problema relativo a spyware/malware è presente nel computer.  

Il collegamento seguente include i passaggi per rimuovere questo spyware/malware: [https://www.thesafemac.com/arg-genieo/](https://www.thesafemac.com/arg-genieo/)  
