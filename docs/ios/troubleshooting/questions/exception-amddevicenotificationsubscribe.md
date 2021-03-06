---
title: System. Exception AMDeviceNotificationSubscribe ha restituito...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: e1633989fc9b85d969f464857ab763153aea2e7d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031107"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>System. Exception AMDeviceNotificationSubscribe ha restituito...

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di Novell. Tuttavia, se il problema si verifica nella versione più recente del software, inserire un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con le informazioni complete sul controllo delle versioni e l'output del log di compilazione completo.

## <a name="fix"></a>Correggi

1. Terminare il processo di `usbmuxd` in modo che il sistema lo riavvii:

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2. Se il problema persiste, riavviare il computer Mac.

## <a name="error-message"></a>Messaggio di errore

```csharp
Exception: Exception type: System.Exception
AMDeviceNotificationSubscribe returned: 3892314211
  at Xamarin.MacDev.IPhoneDeviceManager.SubscribeToNotifications () [0x00000] in <filename unknown="">:0
  at Xamarin.MacDev.IPhoneDeviceManager.StartWatching (Boolean useOwnRunloop) [0x00000] in <filename unknown="">:0
  at Mtb.Server.DeviceListener.Start () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.RunDeviceListener () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.Main (System.String[] args) [0x00000] in <filename unknown="">:0
```

Questo messaggio può essere visualizzato in una finestra di dialogo di errore quando si avvia per la prima volta Visual Studio per Mac o nel file di `mtbserver.log` nell'app host di compilazione Xamarin.iOS (host di compilazione**Xamarin.ios > visualizzare il log dell'host di compilazione**).

Si noti che si tratta di un problema non comune. Se si verificano problemi durante la connessione di Visual Studio all'host di compilazione Mac, sono presenti altri errori che potrebbero essere visualizzati nel file di `mtbserver.log`.

### <a name="errors-in-systemlog"></a>Errori in System. log

In alcuni casi i due messaggi di errore seguenti possono essere visualizzati più volte in `/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>Informazioni aggiuntive

Una supposizione alla causa principale dell'errore è che i servizi di sistema OS X responsabili della creazione di report sul dispositivo iOS e le informazioni sul simulatore possono in rari casi immettere uno stato imprevisto. Novell non può interagire correttamente con i servizi di sistema in questo stato. Il riavvio del computer riavvia i servizi di sistema e risolve il problema.

In base agli errori di `system.log` sembra che questo problema possa essere correlato a Bonjour (`mDNSResponder`). La modifica tra reti Wi-Fi diverse sembra aumentare le probabilità di raggiungere il problema.

## <a name="references"></a>Riferimenti

* [Bug 11789-MonoTouch. MobileDevice. MobileDeviceException: AMDeviceNotificationSubscribe restituito: 0xe8000063 [resolved noresponse]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
