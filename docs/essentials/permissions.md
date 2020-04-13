---
title: 'Xamarin.Essentials: Autorizzazioni'
description: In questo documento viene descritta la classe Permissions in Xamarin.Essentials, che consente di controllare e richiedere autorizzazioni di runtime.
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 21f2079ace4adae6fd84d89426e5d66692af2a0a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78289802"
---
# <a name="xamarinessentials-permissions"></a>Xamarin.Essentials: Autorizzazioni

La classe **Permissions** consente di controllare e richiedere le autorizzazioni di runtime.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-permissions"></a>Utilizzo delle autorizzazioni

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>Controllo delle autorizzazioni

Per verificare lo stato corrente `CheckStatusAsync` di un'autorizzazione, utilizzare il metodo insieme all'autorizzazione specifica per ottenere lo stato.

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

Se `PermissionException` l'autorizzazione richiesta non è dichiarata, viene generata un'autorizzazione.

## <a name="requesting-permissions"></a>Richiesta di autorizzazioni

Per richiedere un'autorizzazione agli `RequestAsync` utenti, utilizzare il metodo insieme all'autorizzazione specifica per richiedere. Se l'utente ha concesso in precedenza l'autorizzazione `Granted` e non l'ha revocata, questo metodo restituirà immediatamente e non visualizzerà una finestra di dialogo. 

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

Se `PermissionException` l'autorizzazione richiesta non è dichiarata, viene generata un'autorizzazione. 

Si noti che su alcune piattaforme una richiesta di autorizzazione può essere attivata una sola volta. Ulteriori prompt devono essere gestiti dallo sviluppatore per verificare `Denied` se una permisione è nello stato e chiedere all'utente di attivarla manualmente.

## <a name="permission-status"></a>Stato autorizzazione

Quando `CheckStatusAsync` si `RequestAsync` `PermissionStatus` utilizza o verrà restituito un oggetto che verrà utilizzato per determinare i passaggi successivi.

* Sconosciuto - L'autorizzazione è in uno stato sconosciuto
* Negato - L'utente ha negato la richiesta di autorizzazione
* Disabilitato - La funzione è disattivata sul dispositivo
* Concesso - L'utente ha concesso l'autorizzazione o viene concessa automaticamente
* Limitato - In uno stato limitato

## <a name="available-permissions"></a>Autorizzazioni disponibili

Xamarin.Essentials tenta di astrarre il maggior numero possibile di autorizzazioni, tuttavia ogni sistema operativo dispone di un set diverso di autorizzazioni di runtime. Inoltre, esistono differenze nella possibilità di fornire una singola API per alcune autorizzazioni. Ecco una guida alle autorizzazioni attualmente disponibili:

Guida alle icone:

* ![Supporto completo](~/media/shared/yes.png "supporto completo") - Supportato
* ![Non supportato](~/media/shared/no.png "Non supportato o richiesto") - Non supportato/obbligatorio

| Autorizzazione | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: 
| CalendarRead (Lettura Calendario)   | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| CalendarScrivi | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Camera | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| ContattiLettura | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| ContattiScrittura | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Torcia elettrica | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![iOS non supportato](~/media/shared/no.png "iOS non supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| LocationWhenInUse | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato")  | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| PosizioneSempre | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| Contenuti multimediali | ![Android non supportato](~/media/shared/no.png "Android non supportato") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Microphone | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| Telefono | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Foto | ![Android non supportato](~/media/shared/no.png "Android non supportato") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Reminders | ![Android non supportato](~/media/shared/no.png "Android non supportato") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Sensori | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchOS supportato](~/media/shared/yes.png "watchOS supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Sms | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Sintesi vocale | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![IOS supportato](~/media/shared/yes.png "IOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| StorageRead | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![iOS non supportato](~/media/shared/no.png "iOS non supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| StorageWrite | ![Supportato per Android](~/media/shared/yes.png "Supportato per Android") | ![iOS non supportato](~/media/shared/no.png "iOS non supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchOS non supportato](~/media/shared/no.png "watchOS non supportato") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |

Se un'autorizzazione è contrassegnata come `Granted` ![non supportata,](~/media/shared/no.png "non supportato") verrà sempre restituita quando viene selezionata o richiesta.

## <a name="general-usage"></a>Utilizzo generale
Di seguito è riportato un utilizzo generale per la gestione delle autorizzazioni.

```csharp
public async Task<PermissionStatus> CheckAndRequestPermissionAsync<TPermission>()
{
    var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
    if (status != PermissionStatus.Granted)
    {
        status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
    }

    // Additionally could prompt the user to turn on in settings

    return status;
}
```

Ogni tipo di autorizzazione può avere un'istanza di creato che i metodi possono essere chiamati direttamente.

```csharp
public async Task GetLocationAsync()
{
    var status = await CheckAndRequestPermissionAsync(new Permissions.LocationWhenInUse());
    if (status != PermissionStatus.Granted)
    {
        // Notify user permission was denied
        return;
    }

    var location = await Geolocation.GetLocationAsync();
}

public async Task<PermissionStatus> CheckAndRequestPermissionAsync<T>(T permission)
            where T : BasePermission
{
    var status = await permission.CheckStatusAsync();
    if (status != PermissionStatus.Granted)
    {
        status = await permission.RequestAsync();
    }

    return status;
}
```

## <a name="extending-permissions"></a>Estensione delle autorizzazioniExtending Permissions

L'API Permissions è stata creata per essere flessibile ed estensibile per le applicazioni che richiedono una convalida aggiuntiva o autorizzazioni non incluse in Xamarin.Essentials. Creare una nuova classe `BasePermission` che eredita da e implementa i metodi astratti necessari. Risultato 

```csharp
public class MyPermission : BasePermission
{
    // This method checks if current status of the permission
    public override Task<PermissionStatus> CheckStatusAsync()
    {
        throw new System.NotImplementedException();
    }

    // This method is optional and a PermissionException is often thrown if a permission is not declared
    public override void EnsureDeclared()
    {
        throw new System.NotImplementedException();
    }

    // Requests the user to accept or deny a permission
    public override Task<PermissionStatus> RequestAsync()
    {
        throw new System.NotImplementedException();
    }
}
```

Quando si implementa un'autorizzazione `BasePlatformPermission` in una piattaforma specifica, la classe può essere ereditata da. In questo modo vengono forniti metodi helper della piattaforma aggiuntivi per controllare automaticamente le dichiarazioni.

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="android"></a>[Android](#tab/android)

Le autorizzazioni devono avere gli attributi corrispondenti impostati nel file manifesto Android.Permissions must have the matching attributes set in the Android Manifest file.

Per saperne di più sulle autorizzazioni nella documentazione di [Xamarin.Android.](https://docs.microsoft.com/xamarin/android/app-fundamentals/permissions)

# <a name="ios"></a>[iOS](#tab/ios)

Le autorizzazioni devono avere `Info.plist` una stringa corrispondente nel file. Una volta richiesta e negata un'autorizzazione, un popup non verrà più visualizzato se si richiede l'autorizzazione una seconda volta. È necessario richiedere all'utente di regolare manualmente l'impostazione nella schermata delle impostazioni delle applicazioni in iOS.You must prompt user to manually adjust the setting in the applications settings screen in iOS.

Ulteriori informazioni sono disponibili nella documentazione relativa alle funzionalità di sicurezza e privacy di iOS.Read more on the [iOS Security and Privacy Features](https://docs.microsoft.com/xamarin/ios/app-fundamentals/security-privacy) documentation.

# <a name="uwp"></a>[UWP](#tab/uwp)

Le autorizzazioni devono avere funzionalità corrispondenti dichiarate nel manifesto del pacchetto.

Ulteriori informazioni sulla documentazione della dichiarazione di [funzionalità dell'app.](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations)

--------------

## <a name="api"></a>API

- [Codice sorgente delle autorizzazioni](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Permissions)
- [Documentazione dell'API per le autorizzazioni](xref:Xamarin.Essentials.Permissions)

