---
title: Limitazioni
description: Alcune limitazioni di Xamarin Player in tempo reale
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/29/2018
ms.openlocfilehash: 43699e77bc8b7365c6d5f7fbf27e19a945e69e22
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2018
---
# <a name="limitations"></a>Limitazioni

![Funzionalità di anteprima](~/media/shared/preview.png)

## <a name="device-requirements"></a>Requisiti dei dispositivi
L'app Xamarin Player Live supporta i dispositivi seguenti:

# <a name="androidtabandroid"></a>[Android](#tab/android)

- Android 4.2 o versioni successive.
- ARM-v7a, ARM v8a, ARM64-v8a, x86 o x86_64 processore.

# <a name="iostabios"></a>[iOS](#tab/ios)

- iOS 9.0 o versioni successive.
- Processore ARM64.

-----

## <a name="limitations"></a>Limitazioni

Esistono alcune limitazioni sulle cose che eseguire Xamarin Player in tempo reale, inclusi gli elementi seguenti:

### <a name="xamarinforms"></a>Xamarin.Forms
- Non sono supportati i renderer personalizzati.
- Effetti non sono supportati.
- Controlli personalizzati con proprietà associabile personalizzate non sono supportati.
- Le risorse incorporate non supportate (ie. incorporamento di immagini o altre risorse in una PCL).
- Framework MVVM di terze parti non sono supportati (ie. Prisma, Mvvm Cross, luce Mvvm e così via).
- Cataloghi di asset in iOS non sono supportati.

### <a name="other-project-types"></a>Altri tipi di progetto
- Windows Media Player in tempo reale non è destinata Android native o i progetti iOS (che usano storyboard o Android XML per l'interfaccia utente).

### <a name="misc"></a>Varie
- Un supporto limitato per la reflection (attualmente influisce su alcuni NuGets più diffusi, ad esempio SQLite e Json.NET). Altri NuGets potrebbe ancora essere supportate.
- Impossibile eseguire l'override di alcune classi di sistema (ad esempio, è Impossibile implementare una sottoclasse).
- Alcune funzionalità di piattaforma che richiedono il provisioning non può funzionare in app Xamarin Player in tempo reale (tuttavia è stato configurato per operazioni comuni, ad esempio l'accesso raccolta foto).
- Destinazioni personalizzate e le istruzioni di compilazione vengono ignorate. Ad esempio, strumenti come Fody, Riadatta, AutoFac e AutoMapper non possono essere incorporati.
- Progetti F # non sono supportati in Android e supporto in iOS limitato
- Scenari avanzati con le classi generiche personalizzate e le interfacce potrebbero non essere supportati.

Segnalare eventuali problemi aggiuntivi su [bugzilla](https://aka.ms/live-player-report-issue).


## <a name="related-links"></a>Collegamenti correlati

- [Risoluzione dei problemi](~/tools/live-player/troubleshooting.md)
- [Configurazione](~/tools/live-player/install.md)