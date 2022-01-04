# nrf91_modem_lockup

Steps to reproduce the nrf91 modem lockup issue:

```shell
west init -m https://github.com/huelsenfruchtzwerg/nrf91_modem_lockup.git --mr main nrf91_modem_lockup
cd nrf91_modem_lockup
west update
west build -b nrf9160dk_nrf9160_ns nrf/samples/nrf9160/lwm2m_client -- -DOVERLAY_CONFIG="overlay-nbiot.conf $(pwd)/nrf91_modem_lockup/overlay.conf"
west flash
```

Also add the PSK for your device to https://leshan.eclipseprojects.io as per documentation for the lwm2m_client example.

Now go to `https://leshan.eclipseprojects.io/#/clients/urn:imei:[[IMEI]]/3` (where [[IMEI]] is the IMEI of the board) and observe all resources of the 3/0 object instance, while observing the log-output of the device.

At some point the log will stop and the last log messages will read something along these lines:

```
[00:33:24.093,444] <dbg> net_lwm2m_engine.lwm2m_udp_receive: checking for reply from [unk]
[00:33:24.093,505] <dbg> net_lwm2m_engine.notify_message_reply_cb: NOTIFY ACK type:2 code:0.0 reply_token:'350b42368049d3a1'
[00:33:24.093,597] <dbg> net_lwm2m_engine.lwm2m_udp_receive: reply 0x20019978 handled and removed
[00:33:24.628,479] <dbg> net_lwm2m_engine.generate_notify_message: [AUTO] NOTIFY MSG START: 3/0/17(3) token:'81c87141886ba740' [23.97.187.154] 4296971924
[00:33:24.628,570] <dbg> net_lwm2m_engine.generate_notify_message: NOTIFY MSG: SENT
[00:33:24.628,906] <inf> net_lwm2m_engine: pre_send
```

At which point it will be stuck indefinitely and can only be recovered by resetting.
