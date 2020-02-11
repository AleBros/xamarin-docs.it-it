---
title: Risoluzione dei problemi
description: Condizioni di errore comuni e come risolverle
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63291951-7375-4CBF-BCC3-2E4AD157A2C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: b38e33e05b0bb9d40582611857671d6617023b35
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728317"
---
# <a name="troubleshooting"></a>Risoluzione dei problemi

_Condizioni di errore comuni e come risolverle_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>Errore: "Impossibile trovare una versione di Xamarin.Forms compatibile con..."

Gli errori seguenti possono essere visualizzati nella finestra della **console del pacchetto** quando si aggiornano tutti i pacchetti NuGet in una soluzione Xamarin.Forms o in un progetto di app Android Xamarin.Forms:

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>Causa dell'errore

Visual Studio per Mac (o Visual Studio) può indicare che sono disponibili aggiornamenti per Xamarin.Forms NuGet packge *e tutte le relative dipendenze*. In Xamarin Studio il nodo **pacchetti** della soluzione potrebbe avere un aspetto simile al seguente (i numeri di versione potrebbero essere diversi):

![](images/updates-available.png "Android Project Packages Folder")

Questo errore può verificarsi se si tenta di aggiornare _tutti_ i pacchetti.

Questo perché con i progetti Android impostati su una versione di destinazione/compilazione di Android 6,0 (API 23) o di seguito, Xamarin.Forms ha una dipendenza rigida da versioni *specifiche* dei pacchetti di supporto per Android. Sebbene le versioni aggiornate di questi pacchetti siano disponibili, Xamarin.Forms non è necessariamente compatibile con essi.

In questo caso è necessario aggiornare _solo_ il pacchetto **Xamarin.Forms** in quanto in questo modo si garantisce che le dipendenze rimangano in versioni compatibili. Gli altri pacchetti aggiunti al progetto possono anche essere aggiornati singolarmente purché non causino l'aggiornamento dei pacchetti di supporto per Android.

> [!NOTE]
> Se si usa Xamarin.Forms 2.3.4 o versione successiva **e** la versione di destinazione/compilazione del progetto Android è impostata su Android 7,0 (API 24) o versione successiva, le dipendenze rigide indicate in precedenza non sono più valide ed è possibile aggiornare i pacchetti di supporto indipendentemente dal pacchetto Xamarin.Forms.

### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>Correzione: rimuovere tutti i pacchetti e aggiungere nuovamente Xamarin.Forms

Se i pacchetti **Xamarin.Android. support** sono stati aggiornati a versioni incompatibili, la correzione più semplice consiste nell'eseguire le operazioni seguenti:

1. Eliminare manualmente tutti i pacchetti NuGet nel progetto Android, quindi
2. Aggiungere nuovamente il pacchetto **Xamarin.Forms** .

Verranno automaticamente scaricate le versioni *corrette* degli altri pacchetti.

Per visualizzare un video di questo processo, fare riferimento a questo [post sui forum](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012).
