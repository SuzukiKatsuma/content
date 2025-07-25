---
title: "WebTransport: createUnidirectionalStream() method"
short-title: createUnidirectionalStream()
slug: Web/API/WebTransport/createUnidirectionalStream
page-type: web-api-instance-method
browser-compat: api.WebTransport.createUnidirectionalStream
---

{{APIRef("WebTransport API")}}{{SecureContext_Header}} {{AvailableInWorkers}}

The **`createUnidirectionalStream()`** method of the {{domxref("WebTransport")}} interface asynchronously opens a unidirectional stream.

The method returns a {{jsxref("Promise")}} that resolves to a {{domxref("WritableStream")}} object, which can be used to reliably write data to the server.

<!-- Note, returns a `WebTransportSendStream` according to spec, but not yet implemented -->

"Reliable" means that transmission and order of data are guaranteed. This provides slower delivery (albeit faster than with WebSockets) than {{domxref("WebTransport.datagrams", "datagrams")}}, but is needed in situations where reliability and ordering are important, like chat applications.

The relative order in which queued bytes are emptied from created streams can be specified using the `sendOrder` option.
If set, queued bytes in streams with a higher send order are guaranteed to be sent before queued bytes for streams with a lower send order.
If the order number is not set then the order in which bytes are sent is implementation dependent.
Note however that even though bytes from higher send-order streams are sent first, they may not arrive first.

## Syntax

```js-nolint
createUnidirectionalStream()
createUnidirectionalStream(options)
```

### Parameters

- `options` {{optional_inline}}
  - : An object that may have the following properties:
    - `sendOrder` {{optional_inline}}
      - : A integer value specifying the send priority of this stream relative to other streams for which the value has been set.
        Queued bytes are sent first for streams that have a higher value.
        If not set, the send order depends on the implementation.

### Return value

A {{jsxref("Promise")}} that resolves to a `WebTransportSendStream` object (this is a {{domxref("WritableStream")}}).

### Exceptions

- `InvalidStateError` {{domxref("DOMException")}}
  - : Thrown if `createUnidirectionalStream()` is invoked while the WebTransport is closed or failed.

## Examples

Use the `createUnidirectionalStream()` method to get a reference to a {{domxref("WritableStream")}}. From this you can {{domxref("WritableStream.getWriter", "get a writer", "", "nocode")}} to allow data to be written to the stream and sent to the server.

Use the {{domxref("WritableStreamDefaultWriter.close", "close()")}} method of the resulting {{domxref("WritableStreamDefaultWriter")}} to close the associated HTTP/3 connection. The browser tries to send all pending data before actually closing the associated connection.

```js
async function writeData() {
  const stream = await transport.createUnidirectionalStream({
    sendOrder: "596996858",
  });
  const writer = stream.getWriter();
  const data1 = new Uint8Array([65, 66, 67]);
  const data2 = new Uint8Array([68, 69, 70]);
  writer.write(data1);
  writer.write(data2);

  try {
    await writer.close();
    console.log("All data has been sent.");
  } catch (error) {
    console.error(`An error occurred: ${error}`);
  }
}
```

You can also use {{domxref("WritableStreamDefaultWriter.abort()")}} to abruptly terminate the stream. When using `abort()`, the browser may discard any pending data that hasn't yet been sent.

```js
// …

const stream = await transport.createUnidirectionalStream();
const writer = stream.getWriter();

// …

writer.write(data1);
writer.write(data2);
await writer.abort();
// Not all the data may have been written.
```

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Using WebTransport](https://developer.chrome.com/docs/capabilities/web-apis/webtransport)
- {{domxref("WebTransport.createBidirectionalStream()")}}
- {{domxref("WebSockets API", "WebSockets API", "", "nocode")}}
- {{domxref("Streams API", "Streams API", "", "nocode")}}
- [WebTransport over HTTP/3](https://datatracker.ietf.org/doc/html/draft-ietf-webtrans-http3/)
