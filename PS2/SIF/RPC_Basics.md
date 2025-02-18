---
sort: 3
---
# SIF RPC Basics

Broadly, the SIF protocol can be split into three different layers of abstraction, from lowest to highest:
- **SIF DMA**: Accesses raw hardware registers to transfer data between the EE and IOP.
- **SIF CMD**: Manages interrupt handling, allowing programs to send commands to the other side.
- **SIF RPC**: Defines a server/client interface around SIF CMD, allowing programs to call functions on the other side as if they were part of the program. For example, from the game's perspective, sceMcWrite is like any other function, even though internally it has to send a request to the IOP to access the memory card.

## Calling RPC functions
Assume that a game on the EE wants to open a file.

- The EE client sends a BIND request to the IOP. To do this, the game must know the ID of the server it wants to bind to. Since it is trying to open a file, it would use `0x80000001`, the ID of FILEIO.
- The IOP receives the request and looks up the server struct associated with the ID.
- The IOP finds the server and sends an END packet to the EE. This contains a pointer to the IOP server, along with some other information. After receiving the END reply, the EE client can now call FILEIO functions.
- The EE sends a CALL request to the IOP. The IOP receives this and wakes up the RPC thread associated with the server, passing the function number, data buffer, and size of the data to the RPC thread.
- FILEIO executes the open command and returns a result. The IOP sends an END packet to the EE, and it can optionally directly DMA the result of the RPC command to a different buffer. In this case, it would be a file descriptor ID.

> In more succinct terms, the EE first uses `SifBindRpc` to bind the server to the client, then it uses `SifCallRpc` to call an IOP function.  

## Registering an RPC server
Servers can be registered on both the EE and the IOP. In the vast majority of cases however, only IOP modules register servers.  
Create a new thread, which will be used by the server. All actions below are done on the new thread.  
Call `SifSetRpcQueue` first and then `SifRegisterRpc` to register the server.
Finally, call `SifRpcLoop`. This puts the server thread to sleep until a new SIF RPC request arrives.  
The above creates a dedicated server thread, which can only be used for SIF RPC. If non-blocking execution is desired for whatever reason, one can use `SifGetNextRequest` and `SifExecRequest`.
