---
sort: 5
----

# SIF RPC Commands

## `80000000h`: Change SADDR
```c
struct SifSaddrPkt
{
  struct SifCmdHeader header;
  void* buff;
}
```

This just changes the EE's receive buffer on the IOP side. Used when SifInitRpc is called more than once.

## `80000001h`: Set SREG
```c
struct SifCmdSRegData
{
  SifCmdHeader header;
  int	index;
  uint value;
}
```

Sets a software SIF register - that is, a variable in memory rather than an I/O register.  
Used by the IOP during SIF RPC initialization to tell the EE what the IOP has stored for the EE's receive buffer.

## `80000002h`: SIFCMD Init
```c
struct SifInitPkt
{
  struct SifCmdHeader header; //NOTE: The "opt" field is used by the IOP.
  void* buff;
}
```

This command is sent twice by the EE during SIFRPC initialization, the first with opt=0 and the second with opt=1.  
When opt=0, buff is used as the EE's SIF0 receive address, and the IOP sets SMFLG to 20000h.  
When opt=1, the IOP finishes SIFRPC initialization.

## `80000003h`: Reboot IOP
```c
struct SifIopResetPkt
{
  struct SifCmdHeader header;
  int	arglen; //Length of the command
  int	mode; //Bit 31 enables debug logging, unknown what other values do
  char arg[80]; //The command to be passed to MODLOAD
}
```

See [BIOS IOP REBOOT](/PS2/BIOS/IOP_REBOOT.md) for details on the reboot procedure.

## `80000008h`: Request End
```c
struct SifRpcRendPkt
{
   struct SifCmdHeader sifcmd;
   int rec_id;
   void	*pkt_addr;
   int rpc_id;
   struct SifRpcClientData *client;
   uint cid; //ID of the command sent by the other side (e.g. the command that triggered a REND)
   struct SifRpcServerData *server;
   void	*buff, *cbuff;
}
```

## `80000009h`: Bind
```c
struct SifRpcBindPkt
{
   struct SifCmdHeader sifcmd;
   int rec_id;
   void *pkt_addr;
   int rpc_id;
   struct SifRpcClientData *client;
   int sid; //ID of the server
}
```

## `8000000Ah`: Call

```c
struct SifRpcCallPkt
{
   struct SifCmdHeader sifcmd;
   int rec_id;	
   void *pkt_addr;
   int rpc_id;
   struct SifRpcClientData *client;
   int rpc_number; //ID of the function to call on the server
   int send_size; //Size of data to send to the server
   void *receive; //Buffer to hold reply data from the server
   int recv_size; //Size of reply buffer
   int rmode;
   struct SifRpcServerData *server;
}
```

## `8000000Ch`: Get other data
```c
struct SifRpcOtherDataPkt
{
   struct SifCmdHeader sifcmd;
   int rec_id;
   void *pkt_addr;
   int rpc_id;

   struct SifRpcReceiveData *receive;
   void *src;
   void *dest;
   int size;
}
```

Used by a server to request more data from a client, which replies with an END packet.
