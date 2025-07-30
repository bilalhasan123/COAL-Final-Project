# COAL-Final-Project
Include irvine32.inc
Include macros.inc

BUFFER_SIZE = 1000000

.data
    arr         BYTE  'A', 'B', 'E', 'A', 'C', 'H', 'T', 'R', 'A', 'J'
                BYTE  'F', 'R', 'H', 'I', 'R', 'K', 'L', 'M', 'N', 'O'
                BYTE  'A', 'O', 'G', 'R', 'G', 'A', 'Y', 'Z', 'C', 'K'
                BYTE  'S', 'T', 'R', 'U', 'D', 'E', 'E', 'A', 'L', 'E'
                BYTE  'T', 'A', 'W', 'X', 'M', 'J', 'D', 'L', 'W', 'R'                
                BYTE  'H', 'E', 'R', 'P', 'Q', 'E', 'D', 'S', 'S', 'T'
                BYTE  'U', 'R', 'A', 'S', 'T', 'K', 'N', 'E', 'N', 'O'
                BYTE  'F', 'L', 'Y', 'T', 'I', 'G', 'E', 'T', 'A', 'T'
    space BYTE  "  ", 0
    space1 BYTE  "                                                        ", 0
    space2 BYTE "                                                                               ",0 
    temp DWORD ?
    rows  DWORD 8
    cols  DWORD 10
    check DWORD 1
    Lives DWORD 3
    project BYTE " ",0
name1 BYTE "             23K-2023 MUZZAMMIL YOUSUF ",0
name2 BYTE "             23K-2078 ABDUL REHMAN ",0
name3 BYTE "             23K-2000 BILAL HASAN ",0

    msg1 byte "                                                               Enter any word you want to search : ",0
    msg2 byte "                                                                     The word doesn't found",0
    msg3 byte "                                         The word found",0
    userInput byte 10 DUP (0)
    lengthofArr dword 80
    tempForUserCountNo dword 0
    SavingESI dword ?
    LengthofUserInput dword ?
    score dword ?
    msgB byte "                                                        The word you entered is present Downward",0
    msgC byte "                                                        The word you entered is present Left to Right",0
    msgI byte "                                                        The length of string cannot be greater then 8",0
    repo byte "                                        Enter any number to continue and zero(0) to exit:",0
    menu1 byte "                                                    1.Quick Play ",0
    menu2 byte "                                                    2.See SCORE ",0
    menu3 byte "                                                    3.QUIT ",0
    indexStored dword 8 DUP (?)
    comma byte ","
    char BYTE 4 Dup("0")

    ;FILLING 
    filename byte "score.txt",0
BUFFER_SIZE = 1024
fileHandle HANDLE ?
NameTag dword ?
NextLine byte 0dh,0ah
bytesWritten dword ?
buffer BYTE BUFFER_SIZE DUP(?)
tempRead byte BUFFER_SIZE dup (?)
bytesRead dword ?
    

.code
main PROC


      Again:

call crlf
call crlf
call crlf
call crlf
mov edx,offset menu1
call writestring
call crlf
mov edx,offset menu2
call writestring
call crlf
mov edx,offset menu3
call writestring
call crlf
call crlf
call crlf
call crlf
call crlf

mWrite<"                                           Enter Choice:",0>
mov eax,0
call readdec

cmp al,1
jne next
call clrscr


call Quick_play
jmp Again
jmp quit

next:
cmp al,2
jne next3
      mWrite<"Scores :",0ah,0dh>
      call read_file
	  call crlf
jmp quit

next3:
cmp al,3
jne next4
mov check,0
jmp Quit1

next4:
call crlf
call crlf
mWrite <"                                        You Enter aInvalid Number",0dh,0ah>
mov eax,500
jmp Again
quit:

call readdec
cmp check,0
jne Again

Quit1:
  


    

    exit

main ENDP


Quick_play PROC

   mov edx, offset name1
    call writestring
    mov edx, offset name2
    call writestring
    mov edx, offset name3
    call writestring
;call clrscr
    mov esi, OFFSET arr   ; ESI points to the start of the 2D array
    mov ecx, rows         ; ECX is the number of rows
    call crlf
    call crlf
    call crlf
    call crlf
    call crlf
    call crlf
     mWrite <"                                 Lives : ",0>
    mov eax,Lives
    call writedec
    mWrite <"                                         ",0>
    mWrite <"                            Score : ",0>
    mov eax,score
    call writedec
    mov NameTag,eax 
    call crlf
    call crlf
    
    call crlf
    call crlf
print_rows:
    mov edx, cols          ; Set the number of columns for each row
    call print_row        ; Call a function to print the row
    loop print_rows       ; Continue until all rows are printed

    R1::
   call crlf
    call crlf
    call crlf
    call crlf

    mov edx,offset msg1
    call writestring
    mov edx,offset userInput
    mov ecx,lengthof userInput -1
    call readstring
    

    ; Calculate the length of the string manually
    mov eax, 0
    mov esi, OFFSET userInput
    count_characters:
    cmp byte ptr [esi], 0
    je done
    inc eax
    inc esi
    jmp count_characters

    done:
    cmp eax,9
    jl t1
    mov edx,offset msgI
    CALL crlf
    call writestring
    CALL crlf
    jmp R1
    
    t1:
     mov LengthOfUserInput,eax
    call SearchWord
   
    call WaitMsg           ; Wait for a key press before exiting
   
Quick_play endp
print_row PROC
    mov temp,ecx
    mov ecx, cols
   
   mWrite <"         ",0>
   MOV EDX,offset space1
        call writestring
    print_cols:
        
        movzx eax, byte ptr [esi]  ; Load the current byte from the array
        call WriteChar              ; Print the character
        mov edx, OFFSET space
        call WriteString           ; Print a space between elements
        inc esi                     ; Move to the next byte in the array
        loop print_cols             ; Continue until all columns are printed
        mov ecx,temp
    call Crlf                       ; Move to the next line
    ret
print_row ENDP

SearchWord PROC

    mov ecx, lengthofArr
    mov edi, 0
    mov al, userInput[edi]
    mov edx, 0
    mov esi, 0
    jmp l1

l1:
    cmp al, arr[edx]
    JE saveESI

    cmp edx, lengthofArr - 1
    jGE process

    inc edx
    loop l1

saveESI:
    mov SavingESI, edx
    mov indexStored[esi], edx
    add esi, type indexStored
    jmp step2a

step2a:   ; Left to Right
    inc edx
    inc edi
    mov al, userInput[edi]
    cmp al, arr[edx]
    JE resumeA
    jmp restart1

resumeA:
    mov indexStored[esi], edx
    add esi, type indexStored
    add tempForUserCountNo, 1
    mov ebx, lengthofUserInput
    dec ebx
    cmp tempForUserCountNo, ebx
    JE messageA
    jmp step2a

messageA:
    mov edx, offset msgC
    call writestring
    jmp found

restart1:
    mov tempForUserCountNo, 0
    mov edx, SavingESI
    mov esi, 0
    mov indexStored[esi], edx
    add esi, type indexStored
    mov edi, 0
    jmp step2b

step2b:   ; Top to Bottom
    inc edi
    add edx, 10
    mov al, userInput[edi]
    cmp al, arr[edx]
    JE resumeB
    jmp restart2

resumeB:
    mov indexStored[esi], edx
    add esi, type indexStored
    add tempForUserCountNo, 1
    mov ebx, lengthofUserInput
    dec ebx
    cmp tempForUserCountNo, ebx
    JE messageB
    jmp step2b

messageB:
    mov edx, offset msgB
    call writestring
    jmp found

restart2:
    mov tempForUserCountNo, 0
    mov edx, SavingESI
    mov esi, 0
    mov indexStored[esi], edx
    add esi, type indexStored
    mov edi, 0
    jmp process

process:
    sub Lives, 1
    call crlf
    call crlf
    mov edx, offset msg2
    call writestring
    jmp exitt

found:
    call crlf
    add score, 1
    mov ecx, lengthof indexStored
    mov ebx, 0

l100:
    mov eax, indexStored[ebx]
    cmp eax, 0
    JE continue1

continue2:
    CALL CRLF
    CALL CRLF
    MOV EDX, OFFSET space2
    call writestring
    mov eax, indexStored[ebx]
    call writedec
    mov edx, offset space
    call writestring
    add ebx, type indexStored
    loop l100

continue1:
    mov edx, ebx
    add edx, 4
    mov eax, indexStored[edx]
    cmp eax, 0
    JE exitt
    jmp continue2

exitt:
    call crlf
    MOV tempForUserCountNo, 0
    mov LengthOfUserInput, 0
    mov SavingESI, 0
    mov esi, 0
    mov ecx, 10

x1:
    mov indexStored[esi], 0
    ADD esi, 4
    loop x1
    CALL CRLF
    CALL CRLF
    MOV EDX, offset space1
    call writestring
    call WaitMsg
    CALL CLRSCR
    call crlf
    call crlf
    call crlf
    call crlf
    mWrite <"                                                   SEARCH THE NEXT WORD !!!", 0dh, 0ah>

    mov eax, 2000

    cmp Lives, 0
    jne Quick_play

    ret

SearchWord ENDP


read_file proc
INVOKE CreateFile,ADDR fileName,GENERIC_READ,DO_NOT_SHARE,NULL,OPEN_ALWAYS,FILE_ATTRIBUTE_NORMAL,0

mov fileHandle, eax

INVOKE setfilepointer, filehandle, 0,0, FILE_BEGIN

INVOKE ReadFile,fileHandle,ADDR tempRead,1024,ADDR bytesRead,0

mov edx, offset tempread
call writestring
call crlf
INVOKE CloseHandle, fileHandle
ret
read_file endp


write_file PROC
INVOKE CreateFile,ADDR fileName,GENERIC_WRITE+GENERIC_READ,DO_NOT_SHARE,NULL,OPEN_ALWAYS,FILE_ATTRIBUTE_NORMAL,0
mov fileHandle,eax
INVOKE SetFilePointer,fileHandle,0,0,FILE_END
INVOKE WriteFile, fileHandle,ADDR NameTag, SIZEOF NameTag,ADDR bytesWritten,NULL
INVOKE WriteFile, fileHandle,ADDR NextLine, SIZEOF NextLine,ADDR bytesWritten,NULL
INVOKE CloseHandle, fileHandle
ret
write_file endp

END main
