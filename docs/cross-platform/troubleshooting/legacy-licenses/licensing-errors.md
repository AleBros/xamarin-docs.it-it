---
title: Alcuni errori specifici di gestione delle licenze
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D26BDF2D-923B-4BC1-841A-74583155DB71
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 8592fe5381c974e999477d0ef6ca6ebdd8b38cc4
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2018
---
# <a name="some-specific-licensing-errors"></a>Alcuni errori specifici di gestione delle licenze

> [!IMPORTANT]
> Le informazioni seguenti non si applicano agli utenti MSDN, poiché si tratta di problemi specifici per le licenze Xamarin legacy. Se si è un utente MSDN e vengono visualizzati errori simili a quelle seguenti, provare a [l'aggiornamento alla versione più recente di Xamarin](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/) & fare riferimento a questo [Guida alle opzioni di licenza](~/cross-platform/get-started/requirements.md).



## <a name="invalid-license"></a>"Licenza non valida"

> Licenza non valida. Per riattivare xamarin (XA9999) non è possibile determinare l'edizione di licenza. (XA9010)

Questi messaggi possono essere visualizzati in alcune circostanze diverse.

-   La licenza corrente su disco potrebbe essere di sincronizzazione con le informazioni sull'utente corrente nel computer. [Aggiornare i file di licenza](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md) consentirà di risolvere questo problema in molti casi.

-   Il computer potrebbe avere 2 attivazioni duplicate in conflitto. Questo tipo di licenza non è usato da Xamarin. Se si verificano problemi che sembrano essere correlato a questo errore,. [supporto tramite posta elettronica](https://www.xamarin.com/support).

-   L'account Xamarin potrebbe non essere ancora invitato a una licenza di Xamarin. Vedere anche: [del Team di gestione delle licenze](~/cross-platform/troubleshooting/legacy-licenses/team-management.md).

## <a name="failed-to-load-android-entitlements"></a>"Impossibile caricare i diritti Android"

> Errore Mono.VisualStudio.Shell.ShellPackage: 0: Impossibile caricare i diritti Android: licenza non valida. Per riattivare l'errore Mono.VisualStudio.Shell.ShellPackage xamarin (XA9999): 0: Impossibile aggiornare licenza: licenza non valida. Per riattivare xamarin (XA9999)

Si tratta di una versione precedente del problema "Licenza non valida" sopra. Si applicano gli stessi passaggi di risoluzione dei problemi.

## <a name="this-version-was-released-after-your-subscription-expired"></a>"Questa versione è stata rilasciata dopo la sottoscrizione è scaduta"

> Errore XA9000: Questa versione è stata rilasciata dopo la sottoscrizione è scaduta (11/11/2014 11:41 alle 17.00). (XA9000) (Mobile.Android.Utilities) errore XA9010: Impossibile determinare l'edizione di licenza. (XA9010) (Mobile.Android.Utilities)

In genere, questi messaggi vengono visualizzati in due situazioni:

-   Le licenze sul disco non sono aggiornate. [Aggiornare i file di licenza](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md) consentirà di risolvere questo problema in molti casi.

-   La sottoscrizione di Xamarin non è più attiva e la versione attualmente installata di Xamarin è più recente rispetto alla data di scadenza della sottoscrizione. In questo caso è la soluzione corretta [effettuare il downgrade](http://kb.xamarin.com/customer/portal/articles/1699777) a una versione di Xamarin che è stato rilasciato prima della scadenza della sottoscrizione. Inviare un messaggio di posta [ hello@xamarin.com ](mailto:hello@xamarin.com) per richiedere la versione più recente valida per la sottoscrizione. Se l'errore viene ancora visualizzato dopo il downgrade, assicurarsi di provare ad aggiornare i file di licenza troppo.

## <a name="additional-references"></a>Riferimenti aggiuntivi

-   [Come aggiornare le licenze](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)
-   [Come effettuare il downgrade di Xamarin](http://kb.xamarin.com/customer/portal/articles/1699777-downgrading)
-   [Elenco dei codici di errore di xamarin](~/android/troubleshooting/errors.md)
-   [Elenco dei codici di errore MonoTouch (xamarin)](~/ios/troubleshooting/mtouch-errors.md)

### <a name="next-steps"></a>Passaggi successivi
Per ulteriore assistenza, contattare Microsoft, o se il problema rimane anche dopo l'utilizzo di queste informazioni, vedere [quali opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, i suggerimenti, nonché come Se necessario, archiviare un nuovo bug.
