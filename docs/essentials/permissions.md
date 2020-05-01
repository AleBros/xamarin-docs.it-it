---
title: 'Novell. Essentials: autorizzazioni'
description: Questo documento descrive la classe di autorizzazioni in Novell. Essentials, che consente di controllare e richiedere le autorizzazioni di Runtime.
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 3d61267ae78a4b84907a2bcf6e944eb286b113dd
ms.sourcegitcommit: 8b94b2af2ac69e4a60e210ddc764f4d276c8d88d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82605446"
---
# <a name="xamarinessentials-permissions"></a>Novell. Essentials: autorizzazioni

La classe **Permissions** consente di controllare e richiedere le autorizzazioni di Runtime.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-permissions"></a>Uso delle autorizzazioni

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>Controllo delle autorizzazioni

Per verificare lo stato corrente di un'autorizzazione, usare il `CheckStatusAsync` metodo insieme all'autorizzazione specifica per ottenere lo stato di.

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

Viene `PermissionException` generata un'eccezione se l'autorizzazione necessaria non è dichiarata.

È consigliabile controllare lo stato dell'autorizzazione prima di richiederlo. Ogni sistema operativo restituisce uno stato predefinito diverso se l'utente non è mai stato richiesto. iOS restituisce `Unknown`, mentre altri restituiscono `Denied`.

## <a name="requesting-permissions"></a>Richiesta di autorizzazioni

Per richiedere un'autorizzazione agli utenti, usare il `RequestAsync` metodo insieme all'autorizzazione specifica per la richiesta. Se l'utente ha precedentemente concesso l'autorizzazione e non lo ha revocato, questo metodo `Granted` restituirà immediatelly e non visualizzerà una finestra di dialogo. 

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

Viene `PermissionException` generata un'eccezione se l'autorizzazione necessaria non è dichiarata. 

Si noti che in alcune piattaforme una richiesta di autorizzazione può essere attivata una sola volta. Ulteriori richieste devono essere gestite dallo sviluppatore per verificare se un autorizzare è nello `Denied` stato e richiedere all'utente di attivarlo manualmente.

## <a name="permission-status"></a>Stato delle autorizzazioni

Quando `PermissionStatus` si `CheckStatusAsync` USA `RequestAsync` o viene restituito, che verrà usato per determinare i passaggi successivi.

* Sconosciuto: l'autorizzazione è in uno stato sconosciuto
* Negato-l'utente ha negato la richiesta di autorizzazione
* Disabilitato: la funzionalità è disabilitata nel dispositivo
* Concesso: l'utente ha concesso l'autorizzazione o viene concesso automaticamente
* Con restrizioni in stato con restrizioni

## <a name="available-permissions"></a>Autorizzazioni disponibili

Novell. Essentials tenta di astrarre il maggior numero possibile di autorizzazioni, ma ogni sistema operativo ha un set di autorizzazioni di runtime diverso. Esistono inoltre alcune differenze nell'essere in grado di fornire una singola API per alcune autorizzazioni. Ecco una guida per le autorizzazioni attualmente disponibili:

Guida alle icone:

* ![Supporto completo](~/media/shared/yes.png "supporto completo") : supportato
* ![Non supportato](~/media/shared/no.png "Non supportato o obbligatorio") : non supportato/obbligatorio

| Autorizzazione | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: 
| CalendarRead   | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchos supportati](~/media/shared/yes.png "watchos supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| CalendarWrite | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchos supportati](~/media/shared/yes.png "watchos supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Fotocamera | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchos non supportati](~/media/shared/no.png "watchos non supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| ContactsRead | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchos non supportati](~/media/shared/no.png "watchos non supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| ContactsWrite | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchos non supportati](~/media/shared/no.png "watchos non supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Torcia elettrica | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS non supportato](~/media/shared/no.png "iOS non supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchos non supportati](~/media/shared/no.png "watchos non supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| LocationWhenInUse | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchos supportati](~/media/shared/yes.png "watchos supportati") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato")  | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| LocationAlways | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchos supportati](~/media/shared/yes.png "watchos supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| Contenuti multimediali | ![Android non supportato](~/media/shared/no.png "Android non supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchos non supportati](~/media/shared/no.png "watchos non supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Microfono | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchos non supportati](~/media/shared/no.png "watchos non supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen supportato](~/media/shared/yes.png "Tizen supportato") |
| Telefono | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchos non supportati](~/media/shared/no.png "watchos non supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Foto | ![Android non supportato](~/media/shared/no.png "Android non supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchos non supportati](~/media/shared/no.png "watchos non supportati") | ![tvOS supportato](~/media/shared/yes.png "tvOS supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Reminders | ![Android non supportato](~/media/shared/no.png "Android non supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchos supportati](~/media/shared/yes.png "watchos supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Sensori | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP supportato](~/media/shared/yes.png "UWP supportato") | ![watchos supportati](~/media/shared/yes.png "watchos supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| SMS | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchos non supportati](~/media/shared/no.png "watchos non supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| Sintesi vocale | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS supportato](~/media/shared/yes.png "iOS supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchos non supportati](~/media/shared/no.png "watchos non supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| StorageRead | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS non supportato](~/media/shared/no.png "iOS non supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchos non supportati](~/media/shared/no.png "watchos non supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |
| StorageWrite | ![Android supportato](~/media/shared/yes.png "Android supportato") | ![iOS non supportato](~/media/shared/no.png "iOS non supportato") | ![UWP non supportato](~/media/shared/no.png "UWP non supportato") | ![watchos non supportati](~/media/shared/no.png "watchos non supportati") | ![tvOS non supportato](~/media/shared/no.png "tvOS non supportato") | ![Tizen non supportato](~/media/shared/no.png "Tizen non supportato") |

Se un'autorizzazione è contrassegnata come ![non supportata](~/media/shared/no.png "non supportato") , restituirà `Granted` sempre quando viene selezionata o richiesta.

## <a name="general-usage"></a>Utilizzo generale
Di seguito è riportato un modello di utilizzo generale per la gestione delle autorizzazioni.

```csharp
public async Task<PermissionStatus> CheckAndRequestLocationPermission()
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

Ogni tipo di autorizzazione può avere un'istanza di creata che i metodi possono essere chiamati direttamente.

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

## <a name="extending-permissions"></a>Estensione delle autorizzazioni

L'API delle autorizzazioni è stata creata per essere flessibile ed estendibile per le applicazioni che richiedono una convalida o autorizzazioni aggiuntive che non sono incluse in Novell. Essentials. Creare una nuova classe che eredita da `BasePermission` e implementare i metodi astratti richiesti. Risultato 

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

Quando si implementa un'autorizzazione in una piattaforma specifica, `BasePlatformPermission` la classe può essere ereditata da. Fornisce metodi di supporto della piattaforma aggiuntivi per controllare automaticamente le dichiarazioni.

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="android"></a>[Android](#tab/android)

Per le autorizzazioni devono essere impostati gli attributi corrispondenti nel file manifesto Android.

Per altre informazioni, vedere la documentazione sulle [autorizzazioni nella documentazione di Novell. Android](https://docs.microsoft.com/xamarin/android/app-fundamentals/permissions) .

# <a name="ios"></a>[iOS](#tab/ios)

Le `Info.plist` autorizzazioni devono contenere una stringa corrispondente nel file. Quando viene richiesta un'autorizzazione e viene negato un popup non verrà più visualizzato se si richiede l'autorizzazione una seconda volta. È necessario richiedere all'utente di modificare manualmente l'impostazione nella schermata Impostazioni applicazioni in iOS.

Per altre informazioni, vedere la documentazione sulle [funzionalità di sicurezza e privacy di iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/security-privacy) .

# <a name="uwp"></a>[UWP](#tab/uwp)

Le autorizzazioni devono avere funzionalità di corrispondenza dichiarate nel manifesto del pacchetto.

Per altre informazioni, vedere la documentazione relativa alla [dichiarazione della funzionalità delle app](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations) .

--------------

## <a name="api"></a>API

- [Codice sorgente delle autorizzazioni](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Permissions)
- [Documentazione dell'API delle autorizzazioni](xref:Xamarin.Essentials.Permissions)

