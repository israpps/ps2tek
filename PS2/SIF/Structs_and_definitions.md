---
sort: 4
---
# SIF RPC Structs and Definitions

## Functions
```c
  typedef void (*SifCmdHandler)(void *data, void *harg);
  typedef void* (*SifRpcFunc)(int fno, void *buff, int length);
  typedef void (*SifRpcEndFunc)(void *end_param);
```

## Packet Headers
```c
struct SifCmdHeader
{
  uint psize:8; //Size of the command packet
  uint dsize:24; //Size of the payload, if any
  void *dest; //Destination of the payload, if any
  int	cid; //Command ID
  uint opt;
}

struct SifRpcPktHeader
{
  struct SifCmdHeader	sifcmd;
  int	rec_id;
  void *pkt_addr;
  int	rpc_id;
}
```

## Client

```c
struct SifRpcClientData
{
  struct SifRpcHeader	hdr;
  u32	command;
  void *buff, *cbuff;
  SifRpcEndFunc end_function;
  void *end_param;
  struct SifRpcServerData *server;
}
```

## Server

```c
struct SifRpcServerData
{
  int	sid;

  SifRpcFunc func;
  void *buff;
  int	size;

  SifRpcFunc cfunc;
  void *cbuff;
  int	size2;

  struct SifRpcClientData *client;
  void *pkt_addr;
  int	rpc_number;

  void *receive;
  int	rsize;
  int	rmode;
  int	rid;

  struct SifRpcServerData *link;
  struct SifRpcServerData *next;
  struct SifRpcDataQueue *base;
}
```

## Server Data Queue

```c
struct SifRpcDataQueue
{
  int	thread_id, active;
  struct SifRpcServerData *link, *start, *end;
  struct SifRpcDataQueue *next;
}
```

## DMA Packet

```c
struct SifDmaTransfer
{
  void *src, *dest;
  int size;
  int attr;
}
```