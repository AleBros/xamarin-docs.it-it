---
title: 'Xamarin.Essentials: connettività'
description: La classe di connettività in Xamarin.Essentials consente di monitorare le modifiche in condizioni di rete del dispositivo, controllare l'accesso alla rete corrente e modo in cui è attualmente connesso.
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 96b4ee0487034c651bec1dfb168fed7567b63c96
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353698"
---
# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials: connettività

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **connettività** permette di classe monitorare le modifiche in condizioni di rete del dispositivo, controllare l'accesso alla rete corrente e modo in cui è attualmente connesso.

## <a name="getting-started"></a>Introduzione

Per l'accesso di **connettività** è necessaria la funzionalità le seguenti impostazioni specifiche della piattaforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Il `AccessNetworkState` l'autorizzazione è obbligatorio e deve essere configurata nel progetto Android. Ciò è possibile aggiungere nei modi seguenti:

Aprire il **AssemblyInfo.cs** file sotto il **proprietà** cartella e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

In alternativa, aggiornare il manifesto di Android:

Aprire il **androidmanifest. XML** file sotto il **proprietà** cartella e aggiungere il codice seguente all'interno del **manifesto** nodo.

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Oppure fare clic con il pulsante destro sul progetto Android e aprire le proprietà del progetto. Sotto **manifesto Android** trovare il **autorizzazioni necessarie:** area e verificare il **stato di accesso rete** autorizzazione. Si aggiornerà automaticamente il **androidmanifest. XML** file.

# <a name="iostabios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

Non è necessaria alcuna configurazione aggiuntiva.

-----

## <a name="using-connectivity"></a>Utilizza la connettività

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

[Accesso alla rete](xref:Xamarin.Essentials.NetworkAccess) rientra nelle categorie seguenti:

* **Internet** – accesso locale e internet.
* **ConstrainedInternet** – accesso internet limitato. Indica captive la connettività del portale, in cui viene fornito accesso locale a un portale web, ma richiede l'accesso a Internet che credenziali specifiche sono disponibili tramite un portale.
* **Locale** : locale solo l'accesso alla rete.
* **Nessuno** – nessuna connettività è disponibile.
* **Sconosciuto** : Impossibile determinare la connettività internet.

È possibile controllare il tipo di [profilo di connessione](xref:Xamarin.Essentials.ConnectionProfile) il dispositivo in uso:

```csharp
var profiles = Connectivity.Profiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

Ogni volta che il profilo di connessione o rete accedere alle modifiche è possibile ricevere un evento quando attivata:

```csharp
public class ConnectivityTest
{
    public ConnectivityTest()
    {
        // Register for connectivity changes, be sure to unsubscribe when finished
        Connectivity.ConnectivityChanged += Connectivity_ConnectivityChanged;
    }

    void Connectivity_ConnectivityChanged(ConnectivityChangedEventArgs  e)
    {
        var access = e.NetworkAccess;
        var profiles = e.Profiles;
    }
}
```

## <a name="limitations"></a>Limitazioni

È importante notare che è possibile che `Internet` viene segnalato da `NetworkAccess` ma accesso completo al web non è disponibile. A causa di funzionamento della connettività in ogni piattaforma può garantire solo che sia disponibile una connessione. Ad esempio il dispositivo può essere collegato a una rete Wi-Fi, ma il router è disconnesso da internet. In questa istanza possono essere riportati Internet, ma non è disponibile una connessione attiva.

## <a name="api"></a>API

* [Codice sorgente di connettività](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [Documentazione delle API di connettività](xref:Xamarin.Essentials.Connectivity)
