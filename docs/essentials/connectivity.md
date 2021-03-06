---
title: 'Xamarin.Essentials: Connettività'
description: La classe Connectivity in Xamarin.Essentials consente di monitorare le variazioni nelle condizioni di rete del dispositivo, controllare l'accesso alla rete corrente e il modo in cui è attualmente connesso.
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 01/08/2019
ms.custom: video
ms.openlocfilehash: c70510f7b47f93c6119532b6a1c06f6c2e9e56ea
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "67855763"
---
# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials: Connettività

La classe **Connectivity** consente di monitorare le variazioni nelle condizioni di rete del dispositivo, controllare l'accesso alla rete corrente e il modo in cui è attualmente connesso.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

Per accedere alla funzionalità **Connectivity**, è necessaria la configurazione seguente specifica della piattaforma.

# <a name="android"></a>[Android](#tab/android)

L'autorizzazione `AccessNetworkState` è obbligatoria e deve essere configurata nel progetto Android. È possibile aggiungerla nei modi seguenti:

Aprire il file **AssemblyInfo.cs** nella cartella **Proprietà** e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

OPPURE aggiornare il manifesto di Android:

Aprire il file **AndroidManifest.xml** nella cartella **Properties** e aggiungere quanto segue all'interno del nodo del **manifesto.**

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Oppure fare clic con il pulsante destro del mouse sul progetto Android e aprire le proprietà del progetto. In **Manifesto Android** trovare l'area **Autorizzazioni necessarie** e selezionare l'autorizzazione **Access Network State** (Accesso stato di rete). Il file **AndroidManifest.xml** verrà aggiornato automaticamente.

# <a name="ios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwp"></a>[UWP](#tab/uwp)

Non è necessaria alcuna configurazione aggiuntiva.

-----

## <a name="using-connectivity"></a>Uso di Connectivity

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Controllare l'accesso alla rete corrente:

```csharp
var current = Connectivity.NetworkAccess;

if (current == NetworkAccess.Internet)
{
    // Connection to internet is available
}
```

Le categorie disponibili per [NetworkAccess](xref:Xamarin.Essentials.NetworkAccess) sono le seguenti:

* **Internet** - Accesso locale e a Internet.
* **ConstrainedInternet** - Accesso a Internet limitato. Indica connettività captive portal, in cui è disponibile l'accesso locale a un portale Web, ma per l'accesso a Internet sono richieste credenziali specifiche specificate tramite un portale.
* **Local** - Solo accesso alla rete locale.
* **None** - Connettività non disponibile.
* **Unknown** - Impossibile determinare la connettività Internet.

È possibile controllare il tipo di [profilo di connessione](xref:Xamarin.Essentials.ConnectionProfile) in uso nel dispositivo:

```csharp
var profiles = Connectivity.ConnectionProfiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

Per ogni variazione del profilo di connessione o dell'accesso alla rete è possibile ricevere un evento quando attivato:

```csharp
public class ConnectivityTest
{
    public ConnectivityTest()
    {
        // Register for connectivity changes, be sure to unsubscribe when finished
        Connectivity.ConnectivityChanged += Connectivity_ConnectivityChanged;
    }

    void Connectivity_ConnectivityChanged(object sender, ConnectivityChangedEventArgs e)
    {
        var access = e.NetworkAccess;
        var profiles = e.ConnectionProfiles;
    }
}
```

## <a name="limitations"></a>Limitazioni

È importante notare che è possibile che `NetworkAccess` indichi `Internet`, ma che l'accesso completo al Web non sia disponibile. In considerazione di come funziona la connettività in ogni piattaforma, è possibile garantire solo che sia disponibile una connessione. Ad esempio, il dispositivo potrebbe essere collegato a una rete Wi-Fi, ma il router potrebbe essere disconnesso da Internet. In questa situazione, potrebbe essere indicata la disponibilità di Internet ma non è disponibile una connessione attiva.

## <a name="api"></a>API

* [Codice sorgente di Connectivity](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [Documentazione dell'API Connectivity](xref:Xamarin.Essentials.Connectivity)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Connectivity-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
