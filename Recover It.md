#chạy thử:

./prob

ELF 64-bit LSB pie executable, x86-64

Input: Input length mismatch!

#Kiểm tra strings

Strings prob

strings prob | grep -E "(Input|Correct|Wrong|memcmp|scanf|rol|ror|cmptable)"

<img width="804" height="1044" alt="image" src="https://github.com/user-attachments/assets/9057f91b-e841-464f-86c8-67a42f44cde6" />

<img width="741" height="518" alt="image" src="https://github.com/user-attachments/assets/98bfd388-cc9f-4e16-83e1-09060a1ca2c5" />

#Disassemble hàm main

Dùng objdump -d để xem assembly của main. Cờ -M intel cho syntax dễ đọc hơn.

objdump -d -M intel prob | grep -A 120 "&lt;main&gt;"


tìm 3 dòng quan trọng trong output

#1) kiểm tra độ dài input:

1310: cmp $0x40,%rax ← input phải dài 0x40 = 64

1314: je 132c

#2) vòng lặp biến đổi:

1344: add $0x67,%eax ← key = i + 0x67

1347: xor %edx,%eax ← XOR với input[i]

1350: mov %dl,(...)

#3) so sánh với cmptable:

1369: lea 0x2cb0(%rip),%rcx ← &cmptable

1376: call memcmp@plt ← memcmp(input, cmptable, 0x40)


<img width="664" height="598" alt="image" src="https://github.com/user-attachments/assets/fa302fb4-3ff4-4f95-bfa9-e1290f18c4e6" />

<img width="661" height="679" alt="image" src="https://github.com/user-attachments/assets/8eeee586-4f6c-4e84-9873-03e027acfe78" />

#tìm địa chỉ và trích xuất cmptable

readelf -S prob | grep -E "(Name|\\.data|\\.bss)"

<img width="540" height="108" alt="image" src="https://github.com/user-attachments/assets/9b2052b4-735c-42b5-97b8-64ea855a333f" />

#Từ disassembly biết cmptable ở địa chỉ 0x4020. Tính offset file:

#.data: vaddr=0x4000, file_offset=0x3000

#cmptable vaddr = 0x4020

#→ file_offset = 0x3000 + (0x4020 - 0x4000) = 0x3020

#viết script giải XOR

with open('prob', 'rb') as f:

 f.seek(0x3020)
 
 ct = f.read(64)
 
flag = ''.join(chr(((i + 0x67) ^ c) & 0xFF) for i, c in enumerate(ct))

print("hacktheon2026{" + flag + "}")

<img width="731" height="219" alt="image" src="https://github.com/user-attachments/assets/df43d528-e47f-46c2-8ccf-ac362fd2a590" />




