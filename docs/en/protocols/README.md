# Protocols
There are two different types of protocols.

- [**Probe Protocol**](#probe-protocols). It includes descriptions and definitions on how agents send collected metrics data and traces, as well as the format of each entity.

- [**Query Protocol**](#query-protocol). The backend enables the query function in SkyWalking's own UI and other UIs. These queries are based on GraphQL.


## Probe Protocols
They also related to the probe group. For more information, see [Concepts and Designs](../concepts-and-designs/overview.md).
These groups are **language-based native agent protocol**, **service mesh protocol** and **3rd-party instrument protocol**.

### Language-based native agent protocol
There are two types of protocols that help language agents work in distributed environments.
1. **Cross Process Propagation Headers Protocol** and **Cross Process Correlation Headers Protocol** come in in-wire data format. Agent/SDK usually uses HTTP/MQ/HTTP2 headers
to carry the data with the RPC request. The remote agent will receive this in the request handler, and bind the context with this specific request.
1. **Trace Data Protocol** is in out-of-wire data format. Agent/SDK uses this to send traces and metrics to SkyWalking or other compatible backends. 

[Cross Process Propagation Headers Protocol v3](Skywalking-Cross-Process-Propagation-Headers-Protocol-v3.md) has been the new protocol for in-wire context propagation since the version 8.0.0 release.

[Cross Process Correlation Headers Protocol v1](Skywalking-Cross-Process-Correlation-Headers-Protocol-v1.md) is a new in-wire context propagation protocol which is additional and optional. 
Please read SkyWalking language agents documentation to see whether it is supported. 
This protocol defines the data format of transporting custom data with `Cross Process Propagation Headers Protocol`.
It has been supported by the SkyWalking javaagent since 8.0.0, 

[SkyWalking Trace Data Protocol v3](Trace-Data-Protocol-v3.md) defines the communication method and format between the agent and backend.

[SkyWalking Log Data Protocol](Log-Data-Protocol.md) defines the communication method and format between the agent and backend.

### Browser probe protocol

The browser probe, such as  [skywalking-client-js](https://github.com/apache/skywalking-client-js), could use this protocol to send data to the backend. This service is provided by gRPC.

[SkyWalking Browser Protocol](Browser-Protocol.md) defines the communication method and format between `skywalking-client-js` and backend.

### Service Mesh probe protocol
The probe in sidecar or proxy could use this protocol to send data to the backend. This service provided by gRPC requires 
the following key information:

1. Service Name or ID on both sides.
1. Service Instance Name or ID on both sides.
1. Endpoint. URI in HTTP, service method full signature in gRPC.
1. Latency. In milliseconds.
1. Response code in HTTP
1. Status. Success or fail.
1. Protocol. HTTP, gRPC
1. DetectPoint. In Service Mesh sidecar, `client` or `server`. In normal L7 proxy, value is `proxy`.

### Events Report Protocol

The protocol is used to report events to the backend. The [doc](../concepts-and-designs/event.md) introduces the definition of an event, and [the protocol repository](https://github.com/apache/skywalking-data-collect-protocol/blob/master/event) defines gRPC services and message formats of events.

Report `JSON` format events via HTTP API, the endpoint is `http://<oap-address>:12800/v3/events`.
JSON event record example:
```json
[
    {
        "uuid": "f498b3c0-8bca-438d-a5b0-3701826ae21c",
        "source": {
            "service": "SERVICE-A",
            "instance": "INSTANCE-1"
        },
        "name": "Reboot",
        "type": "Normal",
        "message": "App reboot.",
        "parameters": {},
        "startTime": 1628044330000,
        "endTime": 1628044331000
    }
]
```

### 3rd-party instrument protocol
3rd-party instrument protocols are not defined by SkyWalking. They are just protocols/formats with which SkyWalking is compatible, and SkyWalking could receive them from their existing libraries. SkyWalking starts with supporting Zipkin v1, v2 data formats.

The backend has a modular design, so it is very easy to extend a new receiver to support a new protocol/format.

## Query Protocol
The query protocol follows GraphQL grammar, and provides data query capabilities, which depends on your analysis metrics.
Read [query protocol doc](query-protocol.md) for more details.
