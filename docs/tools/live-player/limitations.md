---
title: Limitazioni di Xamarin Live Player
description: Questo documento descrive le limitazioni di Xamarin Live Player. Vengono illustrati i requisiti del dispositivo e così le funzionalità funziona con i tipi di progetto e altri argomenti esterni.
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
author: topgenorth
ms.author: toopge
ms.date: 03/29/2018
ms.openlocfilehash: ea71391382f9e1ecb80cbf5f2d5bf127e0d6d1be
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815282"
---
# <a name="limitations-of-xamarin-live-player"></a>Limitazioni di Xamarin Live Player

![Funzionalità di anteprima](~/media/shared/preview.png)

## <a name="device-requirements"></a>Requisiti del dispositivo
L'app Xamarin Live Player supporta i dispositivi seguenti:

# <a name="androidtabandroid"></a>[Android](#tab/android)

- Android 4.2 o versione successiva.
- ARM-v7a, v8a a ARM, ARM64-v8a, x86 o processore x86_64.

# <a name="iostabios"></a>[iOS](#tab/ios)

- iOS 9.0 o versioni successive.
- Processore ARM64.

-----

## <a name="limitations"></a>Limitazioni

Esistono alcune limitazioni sulle cose che eseguire Xamarin Live Player, inclusi gli elementi seguenti:

### <a name="xamarinforms"></a>Xamarin.Forms

- Renderer personalizzati non sono supportati.
- Gli effetti non sono supportati.
- Controlli personalizzati con proprietà associabili personalizzati non sono supportati.
- Le risorse incorporate non supportate (ie. l'incorporamento di immagini o altre risorse in una libreria di classi Portabile).
- Framework MVVM di terze parti non sono supportati (ad esempio, Prism, Mvvm Cross, Mvvm Light e così via).
- Cataloghi asset su iOS non sono supportati.

### <a name="other-project-types"></a>Altri tipi di progetto

- Live Player non è per Android native o i progetti iOS (che usano Android XML o gli storyboard per l'interfaccia utente).

### <a name="misc"></a>Varie

- Supporto limitato per la reflection (attualmente influisce su alcuni pacchetti NuGet più diffusi, come Json.NET e SQLite). Altri pacchetti NuGet potrebbe ancora essere supportata.
- Impossibile eseguire l'override di alcune classi di sistema (ad esempio, è Impossibile implementare una sottoclasse).
- Non è possibile usare alcune funzionalità della piattaforma che richiedono il provisioning nell'app Xamarin Live Player (tuttavia è stato configurato per le operazioni comuni quale l'accesso alla raccolta di foto).
- Istruzioni di compilazione e destinazioni personalizzate vengono ignorate. Ad esempio, non sarà possibile incorporarle strumenti come Fody, Riadatta, AutoFac e AutoMapper.
- I progetti F # non sono supportati in Android e IOS supporto limitato
- Scenari avanzati con le interfacce e classi generiche personalizzate potrebbero non essere supportati.

Segnalare eventuali problemi aggiuntivi sul [bugzilla](https://aka.ms/live-player-report-issue).

## <a name="related-links"></a>Collegamenti correlati

- [Risoluzione dei problemi](~/tools/live-player/troubleshooting.md)
- [Configurazione](~/tools/live-player/install.md)
