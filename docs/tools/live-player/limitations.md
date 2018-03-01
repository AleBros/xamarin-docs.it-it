---
title: Limitazioni
description: Alcune limitazioni di Xamarin Player in tempo reale
ms.topic: article
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 08/04/2017
ms.openlocfilehash: d551c0a82fb8f970ce5a00ec9e64ac7f49c81a44
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="limitations"></a>Limitazioni

![Funzionalità di anteprima](~/media/shared/preview.png)

## <a name="device-requirements"></a>Requisiti dei dispositivi
L'app Xamarin Player Live supporta i dispositivi seguenti:

### <a name="ios"></a>iOS
- iOS 9.0 o versioni successive.
- Processore ARM64.
- Controllare il [App Store](https://itunes.apple.com/us/app/xamarin-live-player/id1228841832?mt=8) per un elenco dei dispositivi supportati.

### <a name="android"></a>Android
- Android 4.2 o versioni successive.
- ARM-v7a, ARM v8a, ARM64-v8a, x86 o x86_64 processore.


## <a name="limitations"></a>Limitazioni

Esistono alcune limitazioni sulle cose che eseguire Xamarin Player in tempo reale, inclusi gli elementi seguenti:

### <a name="android"></a>Android
- Le interfacce utente Android progettate con file AXML non sono attualmente supportate.

### <a name="ios"></a>iOS
- Alcune funzionalità di storyboard iOS non sono supportati.
- file di XI iOS non sono supportati.

### <a name="xamarinforms"></a>Xamarin.Forms
- Non sono supportati i renderer personalizzati.
- Effetti non sono supportati.
- Controlli personalizzati con proprietà associabile personalizzate non sono supportati.
- Le risorse incorporate non supportate (ie. incorporamento di immagini o altre risorse in una PCL).

### <a name="misc"></a>Varie
- Un supporto limitato per la reflection (attualmente influisce su alcuni NuGets più diffusi, ad esempio SQLite e Json.NET). Altri NuGets sono ancora supportate.
- Impossibile eseguire l'override di alcune classi di sistema (ad esempio, è Impossibile implementare una sottoclasse).
- Alcune funzionalità di piattaforma che richiedono il provisioning non può funzionare in app Xamarin Player in tempo reale (tuttavia è stato configurato per operazioni comuni, ad esempio l'accesso raccolta foto).
- Destinazioni personalizzate e le istruzioni di compilazione vengono ignorate. Ad esempio, strumenti come Fody non possono essere incorporati.

> [!WARNING]
> **Nota:** Xamarin Player in tempo reale non funziona con Xamarin Studio

Segnalare eventuali problemi aggiuntivi su [bugzilla](https://aka.ms/live-player-report-issue).


## <a name="related-links"></a>Collegamenti correlati

- [Risoluzione dei problemi](~/tools/live-player/troubleshooting.md)
- [Configurazione](~/tools/live-player/install.md)
