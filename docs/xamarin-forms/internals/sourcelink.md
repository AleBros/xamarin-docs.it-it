---
title: Collegamento all'origine con Xamarin.Forms
description: Questo articolo illustra come usare il collegamento di origine per eseguire il debug in Xamarin.Forms.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetId: 1E13FCD9-5607-46E8-80E4-87A58B389BEB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/26/2019
ms.openlocfilehash: 7a3fe70c8ac29f9e84b5d071a0ba1ef73afbbe11
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697500"
---
# <a name="source-link-with-xamarinforms"></a>Collegamento all'origine con Xamarin.Forms

I pacchetti NuGet di Xamarin.Forms includono i mapping dei collegamenti di origine. Il collegamento all'origine esegue il mapping delle librerie compilate, contenute in un pacchetto NuGet, a un repository di codice sorgente. Visual Studio scaricherà i file del codice sorgente durante il debug e consentirà agli sviluppatori di eseguire il codice istruzione per istruzione, abilitando il debug dei pacchetti senza compilazione dall'origine.

Per ulteriori informazioni sull'utilizzo del collegamento all'origine, vedere la [documentazione sul collegamento all'origine](/dotnet/standard/library-guidance/sourcelink).

::: zone pivot="windows"

> [!WARNING]
> Visual Studio 2019 supporta il collegamento all'origine per il **debugger .NET** , ma attualmente non supporta il collegamento all'origine per il **debugger mono**. È quindi possibile usare il collegamento di origine per eseguire il debug di app UWP, ma non per app Android o iOS. Quando si esegue il debug delle app UWP, è necessario assicurarsi che i file PDB per le librerie di cui si vuole eseguire il debug vengano copiati nella cartella **appx** della directory **bin** in cui viene compilata l'app.

## <a name="enable-source-link"></a>Abilita collegamento all'origine

Per usare il collegamento all'origine, è necessario abilitare il debug per il codice esterno. in caso contrario, il debugger eseguirà le chiamate precedenti al codice non contenuto nella soluzione corrente. In Visual Studio 2019 è disponibile nel menu **Opzioni** della sezione **debug** :

[Collegamento all'origine ![Enable in Visual Studio 2019](sourcelink-images/sourcelink-enable-pc-cropped.png)](sourcelink-images/sourcelink-enable-pc.png#lightbox)

Assicurarsi che **Abilita Just My Code** sia disabilitata e che l' **Abilitazione del supporto** per il collegamento all'origine sia abilitata.

::: zone-end
::: zone pivot="macos"

## <a name="enable-source-link"></a>Abilita collegamento all'origine

Per usare il collegamento all'origine, è necessario abilitare il debug per il codice esterno. in caso contrario, il debugger eseguirà le chiamate precedenti al codice non contenuto nella soluzione corrente. Questa opzione è disponibile nella finestra **Preferenze** della sezione **debugger** :

[Collegamento all'origine ![Enable in Visual Studio per Mac](sourcelink-images/sourcelink-enable-mac-cropped.png)](sourcelink-images/sourcelink-enable-mac.png#lightbox)

Assicurarsi che l'istruzione per il **codice esterno** sia abilitata.

::: zone-end

## <a name="debug-xamarinforms-using-source-link"></a>Debug di Xamarin.Forms tramite il collegamento all'origine

Se il debug di pacchetti esterni è abilitato, Visual Studio userà i mapping dei collegamenti di origine contenuti nel pacchetto NuGet per scaricare ed eseguire il codice sorgente esterno. Questo può essere testato impostando un punto di interruzione in una chiamata a un metodo fornito da Xamarin.Forms:

[![Breakpoint impostato sul metodo Xamarin.Forms](sourcelink-images/breakpoint-cropped.png)](sourcelink-images/external-code-available.png#lightbox)

A seconda delle impostazioni specificate nelle opzioni del **debugger** , Visual Studio avvisa l'utente che sta scaricando i file di origine:

[Avviso del codice esterno di ![Visual Studio](sourcelink-images/external-code-cropped.png)](sourcelink-images/external-code-available.png#lightbox)

Quando si consente a Visual Studio di scaricare i file, il debugger eseguirà l'istruzione nel codice esterno.

::: zone pivot="windows"

## <a name="source-link-caching"></a>Caching del collegamento di origine

Il collegamento all'origine usa la memorizzazione nella cache per le prestazioni. La directory di memorizzazione nella cache per il collegamento all'origine è definita nel menu **Opzioni** sotto **debug** nella sezione **simboli** :

[Caching del collegamento di origine di ![Visual Studio](sourcelink-images/sourcelink-caching-pc-cropped.png)](sourcelink-images/sourcelink-caching-pc.png#lightbox)

Questo menu consente di specificare la directory di memorizzazione nella cache per tutti i simboli di debug, nonché di cancellare la cache se si verificano problemi con i simboli memorizzati nella cache.

::: zone-end
::: zone pivot="macos"

## <a name="source-link-caching"></a>Caching del collegamento di origine

Il collegamento all'origine usa la memorizzazione nella cache per le prestazioni. La directory di memorizzazione nella cache per il collegamento all'origine in MacOS è `/Users/<username>/Library/Caches/VisualStudio/8.0/Symbols`. Questa cartella contiene le sottocartelle in cui è archiviato il repository utilizzato per scaricare i file di origine. Se il repository di backup per un pacchetto NuGet è stato modificato, potrebbe essere necessario eliminare manualmente queste cartelle per aggiornare la cache.

::: zone-end

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione sul collegamento all'origine](/dotnet/standard/library-guidance/sourcelink)
- [Collegamento all'origine in GitHub](https://github.com/dotnet/sourcelink)
