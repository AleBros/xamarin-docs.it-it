---
title: "Novell Firewall Configuration instructions" Description: "questo documento fornisce un elenco di host che devono essere consentiti nel firewall per consentire il funzionamento di Novell in un ambiente aziendale."
ms. prod: Novell MS. AssetID: 658f699b-8CCA-48f7-ae54-fa956384b6d6 autore: conceptdev ms. Author: crdun ms. Date: 07/17/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarin-firewall-configuration-instructions"></a>Istruzioni di configurazione del firewall di Xamarin

_Un elenco di host da consentire nel firewall per il corretto funzionamento della piattaforma Xamarin nella propria azienda._

Per un'installazione e un funzionamento corretti dei prodotti Xamarin è necessario che alcuni endpoint siano accessibili per scaricare gli strumenti e gli aggiornamenti necessari per il software. Se le impostazioni del firewall dell'utente o dell'azienda sono troppo restrittive potrebbero verificarsi problemi relativi all'installazione, alle licenze, ai componenti e altro. In questo documento vengono descritti alcuni degli endpoint noti che devono essere consentiti nel firewall perché Xamarin funzioni. Questo elenco non include gli endpoint necessari per gli strumenti di terze parti inclusi nel download. Se dopo aver esaminato questo elenco si riscontrano ancora problemi, consultare le guide per la risoluzione dei problemi di installazione di Apple o Android.

## <a name="endpoints-to-allow"></a>Endpoint da consentire

### <a name="xamarin-installer"></a>Programma di installazione di Xamarin

Per la corretta installazione del software quando si usa la versione più recente del programma di installazione di Xamarin è necessario aggiungere gli indirizzi noti seguenti:

- xamarin.com (manifesti del programma di installazione)
- dl.xamarin.com (percorso di download del pacchetto)
- dl.google.com (per scaricare Android SDK)
- download.oracle.com (JDK)
- visualstudio.com (percorso di download dei pacchetti di installazione)
- go.microsoft.com (risoluzione degli URL di installazione)
- aka.ms (risoluzione degli URL di installazione)

Se si usa un Mac e si riscontrano problemi di installazione di Xamarin.Android, verificare che macOS sia in grado di scaricare Java.

### <a name="nuget-including-xamarinforms"></a>NuGet (incluso Xamarin.Forms )

È necessario aggiungere gli indirizzi seguenti per accedere a NuGet ( Xamarin.Forms è incluso in un pacchetto NuGet):

- www.nuget.org (per accedere a NuGet)
- globalcdn.nuget.org (download di NuGet)
- dl-ssl.google.com (Google Components per Android e Xamarin.Forms )

### <a name="software-updates"></a>Aggiornamenti software

Per il corretto download degli aggiornamenti software è necessario aggiungere gli indirizzi seguenti:

- software.xamarin.com (servizio di aggiornamento)
- download.visualstudio.microsoft.com
- dl.xamarin.com

## <a name="xamarin-mac-agent"></a>Xamarin Mac Agent

Per la connessione di Visual Studio a un host di compilazione Mac tramite Xamarin Mac Agent è necessario che la porta SSH sia aperta. Per impostazione predefinita, è la **porta 22**.
