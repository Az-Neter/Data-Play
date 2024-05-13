V1

; Division instruction set
; Assumes input dividend in eax, input divisor in ebx

; Clear edx to ensure it's zeroed for the division operation
xor edx, edx

; Check for division by zero (optional)
cmp ebx, 0
je division_by_zero_error

; Count leading zeros in divisor
bsr ecx, ebx   ; Get position of the most significant bit (divisor)
neg ecx        ; Negate the result to get the number of leading zeros
mov edi, ecx   ; Store the number of leading zeros in edi

; Shift dividend left by the number of leading zeros
shl eax, cl

; Initialize quotient to 0
xor eax, eax

division_loop:
  ; Shift left 1 bit of the quotient
  shl eax, 1
  
  ; Subtract divisor from dividend
  sub eax, ebx

  ; Check the sign flag
  js division_exit   ; If the sign flag is set, exit loop

  ; Increment quotient
  inc eax

  ; Check if dividend is zero
  test eax, eax
  jnz division_loop

division_exit:
  ; Adjust quotient if needed
  sub eax, 1
  
  ; Restore dividend
  shr eax, cl

  ; Remainder is in edx
  xor edx, edx

  ; Handle overflow or errors as needed
  ; ...

division_by_zero_error:
  ; Handle division by zero error
  ; ...
  ret



V2

; Division instruction set
; Assumes input dividend in eax, input divisor in ebx

; Check for division by zero
test ebx, ebx
jz division_by_zero_error

; Calculate leading zeros in divisor
bsr ecx, ebx   ; Get position of the most significant bit (divisor)

; Shift dividend left by the number of leading zeros
shl eax, (31 - ecx)

; Initialize quotient to 0
xor edx, edx

; Division loop
division_loop:
  ; Combine subtraction and update quotient (using setcc instruction)
  cmp eax, ebx  ; Compare dividend and divisor
  setc cl       ; Set carry flag if borrow occurred
  sub eax, ebx  ; Subtract divisor (automatically sets sign flag)
  adc edx, cl   ; Add carry flag to quotient (effectively sets LSB)

  ; Early exit on zero dividend
  test eax, eax
  jnz division_loop

division_exit:
  ; Restore dividend (not needed due to combined leading zeros and shift)
; shr eax, (31 - ecx)

; Remainder is in edx

; Handle overflow or errors as needed
; ...

division_by_zero_error:
  ; Handle division by zero error
  ; ...
  ret

