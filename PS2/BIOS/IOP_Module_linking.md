---
sort: 9
---

# BIOS IOP Module Linking

IOP modules are dynamically linked to each other, but Sony devised an ingenious way to handle this without exposing symbols.  
Linking is accomplished through export tables and import tables. A module can have multiple export tables, which define the functions other modules may access. Import tables give a module access to another module's export table.

The format of an export table is as follows.
```c
  struct export_table
  {
    uint magic; //Must equal 0x41C00000!
    export_table* next; //Internal data for LOADCORE
    ushort version; //0x101 would be version 1.01
    ushort mode; //Unknown what this does
    char name[8]; //Name of the module. Must include a NULL terminator.
    void* export[0]; //An arbitrarily sized array of function pointers. 
  }
  
The format of a import table is similar.
```c
  struct import_table
  {
    uint magic; //Must equal 0x41E00000!
    import_table* next; //Internal data for LOADCORE
    ushort version;
    ushort mode;
    char name[8];
    void* import[0]; //An arbitrarily sized array of jr ra; addiu zero, zero, X instruction pairs. More on that below.
  }
```

`LOADCORE` is responsible for linking modules to each other. When a new module is loaded, `LOADCORE` parses its import tables. When an import table matches an export table, it looks up functions by taking X from addiu zero, zero, X, which is used as an index in the export's function pointer array. Each jr ra is then replaced with j $function. In other words, the return statement is replaced with a direct jump to an imported module's function.

Since functions are only linked by their IDs, rather than their names, in theory this keeps others from learning the names of the functions. In practice, game developers often created custom IOP modules which they forgot to strip, which is how the symbols in Sony's IOP modules are known.
