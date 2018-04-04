---
title: System. Exception AMDeviceNotificationSubscribe restituito...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 257e0c14de3c23825b6abe6601c25438db81c58e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>System. Exception AMDeviceNotificationSubscribe restituito...

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di Xamarin. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.


## <a name="fix"></a>Correzione

1.  Terminare il `usbmuxd` elaborare in modo che il sistema verrà riavviata:

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2.  Se non viene risolto il problema, riavviare il computer Mac.

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

Questo messaggio viene visualizzato in una finestra di dialogo di errore quando si avvia Visual Studio per Mac o nel `mtbserver.log` file nell'Host di compilazione xamarin app (**Host di compilazione xamarin. IOS > Visualizza Log di Host di compilazione**).

Si noti che questo è un problema comune. Se Visual Studio si verificano problemi di connessione all'host di compilazione Mac, sono presenti altri errori che più possono vengono visualizzati di `mtbserver.log` file.

### <a name="errors-in-systemlog"></a>Errori in System. log

Errore alcuni casi i seguenti due messaggi possono anche essere inclusi più volte in `/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>Informazioni aggiuntive

Una stima può essere la causa radice dell'errore è che i servizi di sistema responsabili per la segnalazione di informazioni sul dispositivo o del simulatore iOS possono in rari casi OS X entri in uno stato imprevisto. Xamarin non è possibile interagire correttamente con i servizi di sistema in questo stato. Il riavvio del computer riavvia i servizi di sistema e risolve il problema.

In base agli errori da `system.log` risulta che il problema potrebbe essere correlato a Bonjour (`mDNSResponder`). Modifica tra diverse reti Wi-Fi potrebbe aumentare le probabilità di raggiungere il problema.

## <a name="references"></a>Riferimenti

*   [Bug 11789 - MonoTouch.MobileDevice.MobileDeviceException: AMDeviceNotificationSubscribe restituito: 0xe8000063 [risolto NORESPONSE]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
