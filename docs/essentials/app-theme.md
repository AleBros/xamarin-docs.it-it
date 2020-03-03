---
title: "Xamarin.Essentials: Tema dell'app"
description: Questo documento descrive l'API del tema dell'app richiesto in Xamarin.Essentials che offre informazioni sullo stile del tema richiesto per l'app in esecuzione.
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 39f650a73f03888e50c6259c1052e24dbea0a4f5
ms.sourcegitcommit: ec62c7f28abc8e121656f1b93146657d90a4cab4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77555669"
---
# <a name="xamarinessentials-app-theme"></a>Xamarin.Essentials: Tema dell'app

L'API **RequestedTheme** è inclusa nella classe [AppInfo](/app-information.md) e offre informazioni sul tema richiesto per l'app in esecuzione da parte del sistema.

## <a name="get-started"></a>Introduzione

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

Ciò specificherà il tema corrente richiesto dal sistema per l'applicazione. Il valore restituito può essere uno dei seguenti:

* Non specificato
* Chiaro
* Scuro

Il valore non specificato verrà restituito quando il sistema operativo non ha uno stile di interfaccia utente specifico da richiedere. Un esempio è rappresentato dai dispositivi che eseguono versioni di iOS precedenti alla 13.0.


## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="android"></a>[Android](#tab/android)

Android usa le modalità di configurazione per specificare il tipo di tema da richiedere all'utente. In base alla versione di Android, può essere modificato dall'utente o modificato quando è abilitata la modalità di risparmio della batteria.

Altre informazioni sono disponibili nella [documentazione di Android relativa al tema scuro](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme).


# <a name="ios"></a>[iOS](#tab/ios)

Non specificato verrà sempre restituito nelle versioni di iOS precedenti alla versione 13.0 


# <a name="uwp"></a>[UWP](#tab/uwp)

Per impostazione predefinita, l'app viene eseguita con il tema impostato dall'utente nelle impostazioni di Windows (**Impostazioni > Personalizzazione > Colori > Scegli la modalità predefinita dell'app**). È possibile impostare la proprietà RequestedTheme dell'app in modo da eseguire l'override dell'impostazione predefinita dell'utente e specificare il tema usato.

Altre informazioni sono disponibili nella [documentazione relativa al tema richiesto UWP](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme).

--------------

## <a name="api"></a>API

- [Codice sorgente di AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentazione dell'API AppInfo](xref:Xamarin.Essentials.AppInfo)
