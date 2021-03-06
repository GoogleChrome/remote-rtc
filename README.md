## RemoteRTC

Remote RTC is a demo library to show how a page could control peer connections
in a remote device in order to directly send/receive media on them and with an
API as close as possible to peer connection. Together with this demo library, a
googlecast receiver app and a sample send client are provided to show a working
use case.

### Why isn't WebRTC API ready for being RPC'd right out-of-the-box?
Parts of webrtc api surface only through events/callbacks, such as onaddstream,
onicecandidate, etc. This leads to the need to wire up those callbacks from the
remote instance into the controller which is non-trival since javascript allows
those callbacks to be wired up after construction by simple assigning methods to
attributes.

Additionally mediastreams are only existent as native objects and need to be
wrapped with a URL.createObjectUrl before being pass around.

### Hows does RemoteRTC helps with it?
RemoteRTC provices an API that encapsulates a remote device and its interface
that can communicate with each other by an api that can be directly integrated
on an rpc protocol such as dnode (see section below on dnode). It does that by
registering callbacks between the controller and the device as early as possible
thus allowing the client to lazy register the callbacks as in the local peer
connection scenario.

  ./interface.js: Implements class RemoteDeviceInterface(remoteDevice) to be used
                  on the controller once it has an rpc interface to the device.

  ./device.js:    Implements a RemoteDevice on top of the provided video and
                  infoElements with an object ready to be exposed on an rpc
                  interface.

### RemoteDevice interface:
  - CreatePeerConnection(callback, configuration, constraints)
      Creates a peer connection on the device and passes it on the provided
      callback. The created peer connection exposes the methods close,
      createOffer, createAnswer, setRemoteDescription, setLocalDescription,
      addIceCandidate and triggers the callbacks on its onicecandidate and
      onaddstream members as close as possible as a normal peer connection.
      Notable difference is on onaddstream which gets called with an object url
      instead of the actual stream.

  - SetCurrentMediaStream(stream)
      Sets the mediastream to be shown on the device.

  - ShowInfo(info)
      Displays an information message on the device.

### dnode (https://github.com/substack/dnode) (MIT license)
"""Dnode is an asynchronous rpc system for node.js that lets you call remote functions.
You can pass callbacks to remote functions, and the remote end can call the functions
you passed in with callbacks of its own and so on. It's callbacks all the way down!"""
