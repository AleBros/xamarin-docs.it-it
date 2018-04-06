---
title: Istruzioni di configurazione del firewall di Xamarin
description: Un elenco di host da includere nell'elenco degli elementi consentiti del firewall per il corretto funzionamento della piattaforma Xamarin nella propria azienda.
ms.prod: xamarin
ms.assetid: 658f699b-8cca-48f7-ae54-fa956384b6d6
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: bb551b548f241cacfc4cb700d247684c15f6fcf7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="xamarin-firewall-configuration-instructions"></a>Istruzioni di configurazione del firewall di Xamarin

_Un elenco di host da includere nell'elenco degli elementi consentiti del firewall per il corretto funzionamento della piattaforma Xamarin nella propria azienda._

Per un'installazione e un funzionamento corretti dei prodotti Xamarin è necessario che alcuni endpoint siano accessibili per scaricare gli strumenti e gli aggiornamenti necessari per il software. Se le impostazioni del firewall dell'utente o dell'azienda sono troppo restrittive potrebbero verificarsi problemi relativi all'installazione, alle licenze, ai componenti e altro. In questo documento vengono descritti alcuni degli endpoint noti che devono essere inclusi nell'elenco degli elementi consentiti del firewall affinché Xamarin funzioni. Questo elenco non include gli endpoint necessari per gli strumenti di terze parti inclusi nel download. Se dopo aver esaminato questo elenco si riscontrano ancora problemi, consultare le guide per la risoluzione dei problemi di installazione di Apple o Android.

## <a name="endpoints-to-whitelist"></a>Endpoint da includere nell'elenco degli elementi consentiti

### <a name="xamarin-installer"></a>Programma di installazione di Xamarin

Per la corretta installazione del software quando si usa la versione più recente del programma di installazione di Xamarin è necessario aggiungere gli indirizzi noti seguenti:

-  xamarin.com (manifesti del programma di installazione)
-  dl.xamarin.com (percorso di download del pacchetto)
-  dl.google.com (per scaricare Android SDK)
-  download.oracle.com (JDK)
-  visualstudio.com (percorso di download dei pacchetti di installazione)
-  go.microsoft.com (risoluzione degli URL di installazione)
-  aka.ms (risoluzione degli URL di installazione)

Se si usa un Mac e si riscontrano problemi di installazione di Xamarin.Android, verificare che macOS sia in grado di scaricare Java.


### <a name="components-store-and-nuget-including-xamarinforms"></a>Store dei componenti e NuGet (che include Xamarin.Forms)

Per accedere allo Store dei componenti Xamarin o a NuGet (Xamarin.Forms è disponibile come pacchetto NuGet) è necessario aggiungere gli indirizzi seguenti:

-  components.xamarin.com (per usare lo Store dei componenti Xamarin)
-  xampubdl.blob.core.windows.net (ospita i download dello Store dei componenti)
-  www.nuget.org (per accedere a NuGet)
-  az320820.vo.msecnd.net (download di NuGet)
-  dl-ssl.google.com (componenti Google)


### <a name="software-updates"></a>Aggiornamenti software

Per il corretto download degli aggiornamenti software è necessario aggiungere gli indirizzi seguenti:

-  software.xamarin.com (servizio di aggiornamento)
-  download.visualstudio.microsoft.com
-  dl.xamarin.com

## <a name="xamarin-mac-agent"></a>Xamarin Mac Agent

Per la connessione di Visual Studio a un host di compilazione Mac tramite Xamarin Mac Agent è necessario che la porta SSH sia aperta. Per impostazione predefinita, è la **porta 22**.

## <a name="summary"></a>Riepilogo

In questa guida sono stati illustrati gli endpoint da includere nell'elenco degli elementi consentiti per l'installazione e l'aggiornamento corretti dei prodotti Xamarin nel computer.