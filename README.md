DELTA = 0x9E3779B9
.global encrypt, decrypt
.section .note.GNU-stack,"",@progbits
.text

############################# tea ###################################
encrypt:
    push    %rbp
    mov     %rsp, %rbp

    mov     (%rdi), %eax        # y = text[0]
    mov     4(%rdi), %ebx       # z = text[1]
    xor     %ecx, %ecx          # sum = 0
    mov     $0x9e3779b9, %r8d   # delta
    mov     $32, %r9d           # loop counter n

encrypt_loop:
    cmp     $0, %r9d
    je      encrypt_end         # se n == 0, sai

    add     %r8d, %ecx          # sum += delta

    # y += ((z << 4) + k[0]) ^ (z+sum) ^ ((z >> 5) + k[1])
    mov     %ebx, %edx
    shl     $4, %edx
    add     (%rsi), %edx

    mov     %ebx, %r10d
    add     %ecx, %r10d
    xor     %r10d, %edx

    mov     %ebx, %r10d
    shr     $5, %r10d
    add     4(%rsi), %r10d
    xor     %r10d, %edx
    add     %edx, %eax

    # z += ((y << 4) + k[2]) ^ (y+sum) ^ ((y >> 5) + k[3])
    mov     %eax, %edx
    shl     $4, %edx
    add     8(%rsi), %edx

    mov     %eax, %r10d
    add     %ecx, %r10d
    xor     %r10d, %edx

    mov     %eax, %r10d
    shr     $5, %r10d
    add     12(%rsi), %r10d
    xor     %r10d, %edx
    add     %edx, %ebx

    dec     %r9d
    jmp     encrypt_loop

encrypt_end:
    mov     %eax, (%rdi)
    mov     %ebx, 4(%rdi)

    pop     %rbp
    retq

############################# detea ###################################	
decrypt:
						# %rdi points to buf
						# %rsi points to key
	retq
