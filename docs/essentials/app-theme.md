---
title: "Novell. Essentials: tema dell'app"
description: Questo documento descrive l'API del tema app richiesta in Novell. Essentials, che fornisce informazioni sullo stile del tema richiesto per l'app in esecuzione.
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: e31cae6ff639dbe261599a7cf78ae31fc09318b3
ms.sourcegitcommit: c83b55f60ece20e9163b3e587130250fdf113a16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79190321"
---
# <a name="xamarinessentials-app-theme"></a>Novell. Essentials: tema dell'app

L'API **RequestedTheme** fa parte della classe [`AppInfo`](app-information.md) e fornisce informazioni sul tema richiesto per l'app in esecuzione dal sistema.

## <a name="get-started"></a>Attività iniziali

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>Uso di RequestedTheme

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>Recupero delle informazioni sul tema

Il tema dell'applicazione richiesto può essere rilevato con l'API seguente:

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

Questo fornirà il tema corrente richiesto dal sistema per l'applicazione. Il valore restituito sarà uno dei seguenti:

* Non specificato
* Light
* Scuro

Il valore non specificato verrà restituito quando il sistema operativo non dispone di uno stile di interfaccia utente specifico da richiedere. Un esempio è nei dispositivi che eseguono versioni di iOS precedenti a 13,0.


## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="android"></a>[Android](#tab/android)

Android usa le modalità di configurazione per specificare il tipo di tema da richiedere all'utente. In base alla versione di Android, può essere modificata dall'utente o modificata quando è abilitata la modalità di risparmio di batteria.

Per altre informazioni, vedere la [documentazione ufficiale di Android per il tema scuro](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme).


# <a name="ios"></a>[iOS](#tab/ios)

Non specificato verrà sempre restituito nelle versioni di iOS precedenti alla 13,0 


# <a name="uwp"></a>[UWP](#tab/uwp)

Per impostazione predefinita, l'app viene eseguita con il tema impostato dall'utente in impostazioni di Windows (**impostazioni > personalizzazione > colori > scegliere la modalità app predefinita**). È possibile impostare la proprietà RequestedTheme dell'app in modo da eseguire l'override dell'impostazione predefinita dell'utente e specificare quale tema verrà usato.

Per altre informazioni, vedere la [documentazione del tema richiesta da UWP](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme).

--------------

## <a name="api"></a>API

- [Codice sorgente di AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentazione dell'API AppInfo](xref:Xamarin.Essentials.AppInfo)
