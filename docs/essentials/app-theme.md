---
title: "Xamarin.Essentials: Tema dell'app"
description: Questo documento descrive l'API Del tema dell'app richiesto in Xamarin.Essentials, che fornisce informazioni sullo stile del tema richiesto per l'app in esecuzione.
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: f322855f26d7a57acc06e97e0c97ab201c3fa586
ms.sourcegitcommit: a9280318bf7bb69e4e5744ee739e76a9cba36b28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82047400"
---
# <a name="xamarinessentials-app-theme"></a>Xamarin.Essentials: Tema dell'app

L'API **RequestedTheme** fa [`AppInfo`](app-information.md) parte della classe e fornisce informazioni sul tema richiesto dal sistema per l'app in esecuzione.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>Utilizzo di RequestedThemeUsing RequestedTheme

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>Ottenere informazioni sul tema

Il tema dell'applicazione richiesto può essere rilevato con la seguente API:

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

Questo fornirà il tema corrente richiesto dal sistema per l'applicazione. Il valore restituito sarà uno dei seguenti:

* Non specificata
* Light
* Dark (Scuro)

Non specificato verrà restituito quando il sistema operativo non dispone di uno stile di interfaccia utente specifico da richiedere. Un esempio è sui dispositivi che eseguono versioni di iOS precedenti alla 13.0.


## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="android"></a>[Android](#tab/android)

Android utilizza le modalità di configurazione per specificare il tipo di tema da richiedere all'utente. In base alla versione di Android, può essere modificato dall'utente o viene modificato quando è attivata la modalità risparmio batteria.

Puoi leggere di più sulla documentazione ufficiale di [Android per Dark Theme](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme).


# <a name="ios"></a>[iOS](#tab/ios)

Non specificato verrà sempre restituito nelle versioni di iOS precedenti alla 13.0 


# <a name="uwp"></a>[UWP](#tab/uwp)

Le applicazioni UWP rispetteranno l'impostazione nel file Xaml UWP in **RequestedTheme**. Se è impostato su un tema specifico, Xamarin.Essentials restituirà sempre questa impostazione. Per usare il tema dinamico del sistema operativo, rimuovi questo nodo dall'applicazione e quando viene eseguita l'app restituirà il tema impostato dall'utente in Impostazioni di Windows (**Impostazioni > Personalizzazione > colori > Scegli la modalità app predefinita**).

Puoi leggere di più sulla documentazione del [tema richiesto UWP](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme).

--------------

## <a name="api"></a>API

- [Codice sorgente di AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentazione dell'API AppInfo](xref:Xamarin.Essentials.AppInfo)
