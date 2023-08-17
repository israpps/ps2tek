---
sort: 4
---

# IOP Console

The IOP uses multiple addresses when printing output to the console. Registers $a1 and $a2 are used to store the string address in memory and its size respectively.
```cpp
  if (PC == 0x12C48 || PC == 0x1420C || PC == 0x1430C)
  {
      uint32_t pointer = gpr[5];
      uint32_t text_size = gpr[6];
      while (text_size)
      {
          auto c = (char)ram[pointer & 0x1FFFFF];
          putc(c);
          
          pointer++;
          text_size--;
      }
  }
```
