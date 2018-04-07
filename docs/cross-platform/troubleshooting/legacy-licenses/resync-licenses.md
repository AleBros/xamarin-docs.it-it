---
title: Come è risincronizzare manualmente le licenze Xamarin?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D0BD93E9-3A1F-4E5B-8EE8-36ADC33DCFE4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: b06a1a7d525c91d7c3973b2b02d3d2835ce482f9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-manually-resynchronize-xamarin-licenses"></a>Come è risincronizzare manualmente le licenze Xamarin?

> [!IMPORTANT]
> Questa Guida non è supportata per la maggior parte degli utenti MSDN poiché non sono necessari per il proprietario o di log in account Xamarin non usa il [memorizzare i componenti di Xamarin](https://components.xamarin.com/) o [Visual Studio per Mac (Mac)](~/cross-platform/get-started/requirements.md).




## <a name="overview"></a>Panoramica

In genere le informazioni di licenza verranno risincronizzate con il server licenze Xamarin automaticamente quando si avvia l'IDE. Ad esempio, rinnovi downgrade e gli aggiornamenti di licenza verranno visualizzati in genere automaticamente dopo il riavvio dell'IDE. Le informazioni sulla licenza visualizzati nell'IDE deve corrispondere le informazioni mostrate nella [pagina account](https://store.xamarin.com/account/my/subscription/computers). Se le informazioni sono ancora non corrispondenti, è possibile verificare innanzitutto che le informazioni sull'account di archiviazione siano corrette e quindi risincronizzare manualmente le licenze la disconnessione, eliminando i file di licenza sul disco e quindi accedere nuovamente.

### <a name="note-for-ios-developers-on-windows"></a>Nota per gli sviluppatori iOS in Windows

gli sviluppatori iOS in Windows potrebbe essere inoltre necessario eseguire questi passaggi per Visual Studio per Mac; anche se è non sviluppare direttamente nell'host di compilazione Mac associata.

## <a name="quick-manual-refresh-steps"></a>Passaggi di aggiornamento manuale rapido

Questo processo rapido ignora il passaggio dell'eliminazione del file di licenza sul disco che potrebbero essere sufficienti, in alcuni casi. 

1.  Disconnettersi da account Xamarin tramite l'IDE:
    -   Visual Studio per Mac
        -   Angolo superiore destro della schermata iniziale
        -   **Visual Studio per Mac > Account** (Mac)
        -   **Strumenti > Account** (Windows)
    -   Visual Studio
        -   **Strumenti > Account Xamarin**
2.  Accedere di nuovo l'account Xamarin nell'IDE.

## <a name="extended-manual-license-refresh-steps"></a>Passaggi di aggiornamento manuale licenza estesi

1.  Disconnettersi da account Xamarin tramite l'IDE. 
2.  Chiudere l'IDE.
3.  Verificare che le informazioni sull'account di archiviazione sono corrette. In particolare controllare per i nomi di computer duplicato di [pagina computer](https://store.xamarin.com/account/my/subscription/computers).

4.  Se viene visualizzata una coppia di nomi di computer duplicati, usare il **disattiva** voce di menu di riepilogo a discesa per rimuovere _entrambi_ i membri della coppia:
    
    ![Licenza -> Disattiva attiva https://store.xamarin.com/account/my/subscription/computers ] (resync-licenses-images/deactivate.png "utilizzare la voce di menu elenco a discesa Disattiva per rimuovere entrambi i membri della coppia")

5.  Eliminare le restanti copie dei file di licenza ancora presente sul disco.
    -   WINDOWS

        Eliminare tutte le cartelle seguenti:
        -   `%PROGRAMDATA%\Mono for Android`
        -   `%PROGRAMDATA%\MonoTouch`
        -   `%LOCALAPPDATA%\VirtualStore\ProgramData\Mono for Android`
        -   `%LOCALAPPDATA%\VirtualStore\ProgramData\MonoTouch`

        Nelle installazioni predefinite di Windows:
        -   `%PROGRAMDATA%` si espanderà `C:\ProgramData`
        -   `%LOCALAPPDATA%` si espanderà `C:\Users\%USERNAME%\AppData\Local`

        Il `VirtualStore` copie delle licenze vengono create automaticamente da Windows in determinati scenari. Se le copie VirtualStore esistono, verranno lette _invece_ delle licenze in `%PROGRAMDATA%`.

    -   Mac

        Eliminare tutte le cartelle seguenti:

        -   `~/Library/MonoAndroid`
        -   `~/Library/MonoTouch`
        -   `~/Library/Xamarin.Mac`

        È possibile accedere a questi percorsi in **Finder** selezionando il **passare > passare alla cartella** menu e incollarla nella finestra di dialogo. Finder sostituisce automaticamente il ~ carattere tilde con la cartella dell'utente.

6.  Aprire Visual Studio per Mac o Visual Studio e log riaggiungere l'account Xamarin.

## <a name="supplementary-information-individual-license-file-locations"></a>Informazioni supplementari: percorsi dei file di licenza

### <a name="windows"></a>WINDOWS

In Windows vengono archiviati i file di licenza singoli nei percorsi seguenti:

-   Xamarin.Android:  
     `%PROGRAMDATA%\Mono for Android\License\monoandroid.licx`
-   Xamarin.iOS:  
     `%PROGRAMDATA%\MonoTouch\License\monotouch.licx`

Le licenze per *valutazione* le sottoscrizioni vengono denominate in modo diverso:

-   Xamarin.Android:  
     `%PROGRAMDATA%\Mono for Android\License\monoandroid.trial.licx`
-   Xamarin.iOS:  
     `%PROGRAMDATA%\MonoTouch\License\monotouch.trial.licx`

### <a name="mac"></a>Mac

In Mac vengono archiviati i file di licenza singoli nei percorsi seguenti:

-   Xamarin.Android:  
     `~/Library/MonoAndroid/License`
-   Xamarin.iOS:  
     `~/Library/MonoTouch/License.v2`
-   Xamarin.Mac:  
     `~/Library/Xamarin.Mac/License`

Le licenze per *valutazione* le sottoscrizioni vengono denominate in modo diverso:

-   Xamarin.Android:  
     `~/Library/MonoAndroid/License.trial`
-   Xamarin.iOS:  
     `~/Library/MonoTouch/License.trial`
-   Xamarin.Mac:  
     `~/Library/Xamarin.Mac/License.trial`

## <a name="additional-troubleshooting-steps"></a>Ulteriori passaggi di risoluzione dei problemi

-   Verificare la presenza caratteri non ASCII per il proprio nome utente, nome del computer o in uno dei percorsi dei file di licenza completamente espansi.

-   [Contattare il supporto per gli sviluppatori di Xamarin](http://xamarin.com/support)
