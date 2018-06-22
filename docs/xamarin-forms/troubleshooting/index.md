---
title: Risoluzione dei problemi
description: Condizioni di errore comuni e come risolverli
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63291951-7375-4CBF-BCC3-2E4AD157A2C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: fbe4fb6fce52636b59a9637ee0150c4c19fcc9da
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30788523"
---
# <a name="troubleshooting"></a>Risoluzione dei problemi

_Condizioni di errore comuni e come risolverli_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>Errore: "Impossibile trovare una versione di xamarin. Forms compatibili con..."

Gli errori seguenti possono essere visualizzati nel **pacchetto Console** finestra durante l'aggiornamento di tutti i pacchetti Nuget in una soluzione xamarin. Forms o in un progetto di app Android di xamarin. Forms:

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>Cosa provoca l'errore?

Visual Studio per Mac (o Visual Studio) potrebbe indicare che sono disponibili aggiornamenti per il packge Nuget di xamarin. Forms *e tutte le relative dipendenze*. In Xamarin Studio, la soluzione **pacchetti** nodo potrebbe essere simile al seguente (i numeri di versione potrebbero essere diversi):

![](images/updates-available.png "Cartella di pacchetti di progetto Android")

Questo errore può verificarsi se si tenta di aggiornare _tutti_ i pacchetti.

Questo avviene perché con Android progetti impostata su una versione di destinazione/compilazione di Android 6.0 (API 23) o inferiore, xamarin. Forms ha una dipendenza rigida *specifico* versioni di Android supportano pacchetti. Sebbene le versioni aggiornate di tali pacchetti potrebbero essere disponibili, xamarin. Forms non è necessariamente compatibile.

In questo caso è necessario aggiornare _solo_ il **xamarin. Forms** modo da garantire che le dipendenze rimangono nelle versioni compatibili del pacchetto. Altri pacchetti che sono stati aggiunti al progetto possono essere aggiornate anche singolarmente, purché non provocano i pacchetti di supporto Android per l'aggiornamento.


> [!NOTE]
> Se si usa xamarin. Forms 2.3.4 o versione successiva **e** versione/compilazione di destinazione del progetto Android è impostata su Android 7.0 (API 24) o versioni successive, quindi si applicano le dipendenze rigide indicate in precedenza non è possibile aggiornare il supporto pacchetti in modo indipendente dal pacchetto di xamarin. Forms.


### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>Correzione: Rimuovere tutti i pacchetti e aggiungere nuovamente xamarin. Forms

Se il **deselezionati** pacchetti sono stati aggiornati a versioni incompatibili, risolvere il problema più semplice:

1. Eliminare manualmente tutti i pacchetti di Nuget nel progetto Android, quindi
2. Aggiungere nuovamente il **xamarin. Forms** pacchetto.

Questo viene scaricato automaticamente il *corretto* versioni gli altri pacchetti.

Per visualizzare un video di questo processo, fare riferimento a questo [post di forum](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012).
