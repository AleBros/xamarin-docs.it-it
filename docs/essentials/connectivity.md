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

La classe **Connectivity** permette di monitorare le modifiche alla connesione di rete del dispositivo, controllare l'accesso alla rete corrente e il modo in cui è attualmente connesso.

## <a name="getting-started"></a>Introduzione

Per accedere alle funzione **Connectivity** è necessario eseguire le impostazioni specifiche della piattaforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

L'Autorizzazione `AccessNetworkState` è obbligatoria e deve essere configurata nel progetto Android. E' possibile aggiungerla nei modi seguenti:

Aprire il **AssemblyInfo.cs** file nella cartella **proprietà** e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

In alternativa,è possibile modificare il file manifest di Android:

Aprire il file **androidmanifest. XML** nella cartella **proprietà** e aggiungere il codice seguente all'interno del **manifest** .

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Oppure fare clic con il pulsante destro sul progetto Android e aprire le proprietà del progetto. Nella sezione **manifesto Android** trovare l'area **autorizzazioni necessarie:**  e verificare l'autorizzazione **stato di accesso rete**. Il file **androidmanifest. XML** si aggiornerà automaticamente.

# <a name="iostabios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

Non è necessaria alcuna configurazione aggiuntiva.

-----

## <a name="using-connectivity"></a>Utilizzare Connectivity

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

[NetworkAccess](xref:Xamarin.Essentials.NetworkAccess) rientra rientra nei seguenti valori:

* **Internet** – accesso locale e internet.
* **ConstrainedInternet** – accesso internet limitato. Indica una connettivà protetta, in cui viene fornito accesso locale a un portale web, ma l'accesso a Internet richiede di inserire in un portale delle credenziali specifiche s.
* **Locale** : accesso solo alla rete locale.
* **Nessuno** – nessuna connettività è disponibile.
* **Sconosciuto** : Impossibile determinare la connettività internet.

È possibile controllare il tipo di [profilo di connessione](xref:Xamarin.Essentials.ConnectionProfile) che il dispositivo stà utilizzando:

```csharp
var profiles = Connectivity.Profiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

Ogni volta che il profilo di connessione o l'accesso alla rete subisce delle modifiche è possibile ricevere la notifica di un evento:

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

È importante notare che è possibile che `Internet` venga ritornato da `NetworkAccess` ma accesso completo al web non è disponibile. A causa del funzionamento della connettività in ogni piattaforma si può garantire solo che sia disponibile una connessione. Ad esempio il dispositivo può essere collegato a una rete Wi-Fi, ma il router è disconnesso da internet. In questo caso può essere ritornato Internet, ma non è disponibile una connessione attiva.

## <a name="api"></a>API

* [Codice sorgente di connettività](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [Documentazione delle API di connettività](xref:Xamarin.Essentials.Connectivity)
