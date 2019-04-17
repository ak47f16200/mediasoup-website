## Worker
{: #Worker}

<section markdown="1">

A worker represents a mediasoup subprocess that handles media realtime-communications. It runs in a single CPU core and can handle many [Router](#Router) instances.

</section>


### Dictionaries
{: #Worker-dictionaries}

<section markdown="1">

#### WorkerSettings
{: #WorkerSettings .code}

<div markdown="1" class="table-wrapper L3 M5">

Field                    | Type    | Description   | Required | Default
------------------------ | ------- | ------------- | -------- | ---------
`logLevel`               | String  | Logging level for logs generated by the media worker subprocesses (check the [Debugging](/documentation/v3/mediasoup/debugging/) documentation). Valid values are "debug", "warn", "error" and "none". | No | "error"
`logTags`                | Array&lt;String&gt; | Log tags for debugging. Check the list of available tags in [Debugging](/documentation/v3/mediasoup/debugging/) documentation. | No | `[ ]`
`rtcMinPort`             | Number  | Minimun RTC port for ICE, DTLS, RTP, etc. | No | 10000
`rtcMaxPort`             | Number  | Maximum RTC port for ICE, DTLS, RTP, etc. | No | 59999
`dtlsCertificateFile`    | String  | Path to the DTLS public certificate file in PEM format. If unset, a certificate is dynamically created. | No |
`dtlsPrivateKeyFile`     | String  | Path to the DTLS certificate private key file in PEM format. If unset, a certificate is dynamically created. | No |

</div>

<div markdown="1" class="note">
RTC listening IPs are not set at worker level. Instead, they are set per individual transport.
</div>


#### WorkerUpdateableSettings
{: #WorkerUpdateableSettings .code}

<div markdown="1" class="table-wrapper L3">

Field                    | Type    | Description   | Required | Default
------------------------ | ------- | ------------- | -------- | ---------
`logLevel`               | String  | Logging level for logs generated by the media worker subprocesses (check the [Debugging](/documentation/v3/mediasoup/debugging/) documentation). Valid values are "debug", "warn", "error" and "none". | No | "error"
`logTags`                | Array&lt;String&gt; | Log tags for debugging. Check the list of available tags in [Debugging](/documentation/v3/mediasoup/debugging/) documentation. | No |

</div>

</section>


### Properties
{: #Worker-properties}

<section markdown="1">

#### worker.pid
{: #worker-pid .code}

The PID of the worker process.

> `@type` Number, read only

```javascript
console.log(worker.pid);
// => 86665
```

#### worker.closed
{: #worker-closed .code}

Whether the worker is closed.

> `@type` Boolean, read only

```javascript
console.log(worker.closed);
// => false
```

#### worker.observer
{: #worker-observer .code}

See the [Observer Events](#Worker-observer-events) section below.

> `@type` [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter), read only

</section>


### Methods
{: #Worker-methods}

<section markdown="1">

#### worker.close()
{: #worker-close .code}

Closes the worker, including all its routers.

#### worker.updateSettings(settings)
{: #worker-updateSettings .code}

Updates the worker settings in runtime. Just a subset of the worker settings can be updated.

<div markdown="1" class="table-wrapper L3">

Argument   | Type    | Description | Required | Default 
---------- | ------- | ----------- | -------- | ----------
`settings` | [WorkerUpdateableSettings](#WorkerUpdateableSettings) | Worker updateable settings. | No |

</div>

> `@async`

```javascript
await worker.updateSettings({ logLevel: "warn" });
```

#### worker.createRouter({ mediaCodecs })
{: #worker-createRouter .code}

Creates a new router.

<div markdown="1" class="table-wrapper L3">

Argument      | Type    | Description | Required | Default 
------------- | ------- | ----------- | -------- | ----------
`mediaCodecs` | Array&lt;[RouterMediaCodec](#RouterMediaCodec)&gt; | Router media codecs. | Yes |

</div>

> `@async`
> 
> `@returns` [Router](#Router)

```javascript
const mediaCodecs =
[
  {
    kind        : "audio",
    mimeType    : "audio/opus",
    clockRate   : 48000,
    channels    : 2
  },
  {
    kind       : "video",
    mimeType   : "video/H264",
    clockRate  : 90000,
    parameters :
    {
      "packetization-mode"      : 1,
      "profile-level-id"        : "42e01f",
      "level-asymmetry-allowed" : 1
    }
  }
];

const router = await worker.createRouter({ mediaCodecs });
```

</section>


### Events
{: #Worker-events}

<section markdown="1">

#### worker.on("died")
{: #worker-on-died .code}

Emitted when the worker process unexpectedly dies.

<div markdown="1" class="note warn">
This should never happens (if it happens, it's a bug).
</div>

```javascript
worker.on("died", () =>
{
  console.error("mediasoup worker died!");
});
```

</section>


### Observer Events
{: #Worker-observer-events}

<section markdown="1">

#### worker.observer.on("close")
{: #worker-observer-on-close .code}

Emitted when the worker is closed for whatever reason.

#### worker.observer.on("newrouter", fn(router))
{: #worker-observer-on-newrouter .code}

Emitted when a new router is created.

<div markdown="1" class="table-wrapper L3">

Argument | Type    | Description   
-------- | ------- | ----------------
`router` | [Router](#Router) | New router.

</div>

```javascript
worker.observer.on("newrouter", (router) =>
{
  console.log("new router created [id:%s]", router.id);
});
```

</section>