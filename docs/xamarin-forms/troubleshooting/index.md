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
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850440"
---
# <a name="troubleshooting"></a>Risoluzione dei problemi

_Condizioni di errore comuni e come risolverli_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>Errore: "Impossibile trovare una versione di xamarin. Forms compatibile con..."

Gli errori seguenti possono essere visualizzati nei **Console pacchetti** finestra durante l'aggiornamento di tutti i pacchetti Nuget in una soluzione xamarin. Forms o in un progetto di app Android di xamarin. Forms:

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>Che cosa causa l'errore?

Visual Studio per Mac (o Visual Studio) può indicare che gli aggiornamenti disponibili per il pacchetto Nuget di xamarin. Forms *e tutte le relative dipendenze*. In Xamarin Studio, la soluzione **pacchetti** nodo potrebbe essere simile al seguente (i numeri di versione potrebbero essere diversi):

![](images/updates-available.png "Cartella dei pacchetti di progetto Android")

Questo errore può verificarsi se si tenta di aggiornare _tutti_ i pacchetti.

Questo avviene perché con Android progetti impostato su una versione di destinazione/compilazione di Android 6.0 (API 23) o di seguito, xamarin. Forms ha una dipendenza rigida *specifici* versioni di Android che supportano i pacchetti. Anche se le versioni aggiornate di questi pacchetti possono essere disponibili, xamarin. Forms non è necessariamente compatibile con loro.

In questo caso è necessario aggiornare _soltanto_ le **xamarin. Forms** del pacchetto, per assicurare che le dipendenze rimangono nelle versioni compatibili. Altri pacchetti che sono stati aggiunti al progetto possono anche essere aggiornati singolarmente, purché non causano i pacchetti di supporto per Android da aggiornare.


> [!NOTE]
> Se si usa xamarin. Forms 2.3.4 o versione successiva **e** versione di destinazione/compilazione del progetto Android è impostata su Android 7.0 (API 24) o superiore, quindi le dipendenze rigide indicato in precedenza non saranno più applicano e puoi aggiornare il supporto pacchetti indipendentemente dal pacchetto di xamarin. Forms.


### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>Fix: Rimuovere tutti i pacchetti, quindi aggiungere nuovamente xamarin. Forms

Se il **deselezionati** i pacchetti sono stati aggiornati per le versioni incompatibili, risolvere il problema più semplice:

1. Eliminare manualmente tutti i pacchetti Nuget nel progetto Android, quindi
2. Aggiungere nuovamente il **xamarin. Forms** pacchetto.

Questo scaricherà automaticamente la *corretto* versioni di altri pacchetti.

Per visualizzare un video di questo processo, fare riferimento a questo [post di forum](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012).
