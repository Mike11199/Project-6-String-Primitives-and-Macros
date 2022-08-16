# Project-6-String-Primitives-and-Macros

This is my submission for CS 271 - Computer Architecture & Assembly Language Project 6, taken Summer 2022 at Oregon State University.  Per the Syllabus, this is the portfolio project, which is allowed to be made public, unlike other projects.  This was coded in Visual Studio in x86 Assembly Language, using the MASM assembler.

<h2> Project Description </h2>

-This program allows numbers to be read/written by the users without Irvine library procedures, using manual conversion of numbers to strings and vice versa.

-This program allows user to enter in a series of numbers as strings.  It then converts the string into a number using the ReadVal procedure.  It accomplishes this without Irvine library procedures, by REPEATEDLY MULTIPLYING each char of the string converted to a number by 10 if necessary, building up a number digit by digit.

-After creating the number, it saves it to an array of numbers.  It then calculates the average and sum of these numbers and displays these to the user.

-Finally, it uses the WriteVal function (also used for the sum/average previously) to loop through each SDWORD integer array and convert that number
back into a string manually wihtout Irvine library procedures.  It does this by REPEATEDLY DIVIDING each number by 10 to build back a string from the
remainder of each division. From this, it uses string primitve instructions to reverse the string, so that it is in the correct order.

-It also makes use of two macros to get and display strings to the user.


<h2> Assembly Concepts Used </h2>

-This pro


<h2> Procedure Example - Convert Integer to String </h2>

-Here is an example of a procedure used in the program.  It repeatedly divides an integer by 10 and converts its remainder to a string.  It then has to reverse the string at the end, and add a negative sign if the integer was negative.  It handles base cases, such as zeroes in the middle of the string, or the number only being a zero, which can cause issues.

-In retrospect some of the loops could have been improved.  For example, I could have added 48 to arrive at the ASCII char representation of a number, instead of comparing each digit manually.  This was done as at the time I was having difficulty moving values between the EAX and AL register, which I believe could have been resolved using the PTR operator.


```assembly
_MainConversionLoop:
	
	;need to repeatedly divide by 10, multiply by zeros until no remainder left, then reverse string array created.
	MOV					EAX, num
	CDQ 
	MOV					ebx, 10
	IDIV					ebx
	MOV					quotient, EAX
	MOV					remainder, EDX

	CMP					remainder, 0
	jg					_remainderExists
	CMP					quotient, 0
	jg					_Quotient				; if no quotient and remainder
	jmp					_AddTERMINATOR


_Quotient:
	MOV					EAX, 0
	MOV					num, EAX
	jmp					_startNumConversion

_remainderExists:
	MOV					EAX, remainder
	MOV					num, EAX
	jmp					_startNumConversion


_startNumConversion:
	CMP					num, 0
	jz					_zero_num
	CMP					num, 1
	jz					_one_num
	CMP					num, 2
	jz					_two_num
	CMP					num, 3
	jz					_three_num
	CMP					num, 4
	jz					_four_num
	CMP					num, 5
	jz					_five_num
	CMP					num, 6
	jz					_six_num
	CMP					num, 7
	jz					_seven_num
	CMP					num, 8
	jz					_eight_num
	CMP					num, 9
	jz					_nine_num


_zero_num:
	MOV					AL, 48 
	jmp					add_num_to_string

_one_num:
	MOV					AL, 49 
	jmp					add_num_to_string

_two_num:
	MOV					AL, 50 
	jmp					add_num_to_string

_three_num:
	MOV					AL, 51 
	jmp					add_num_to_string

_four_num:
	MOV					AL, 52
	jmp					add_num_to_string

_five_num:
	MOV					AL, 53 
	jmp					add_num_to_string

_six_num:
	MOV					AL, 54 
	jmp					add_num_to_string

_seven_num:
	MOV					AL, 55 
	jmp					add_num_to_string

_eight_num:
	MOV					AL, 56
	jmp					add_num_to_string

_nine_num:
	MOV					AL, 57 
	jmp					add_num_to_string

_NumisJustZero:
	MOV					AL, 48
	MOV					[EDI], AL				; move result to output variable
	add					EDI, 1					; increment
	inc					newStringLen
	jmp					_AddTERMINATOR

add_num_to_string:
	MOV					[EDI], AL				; move result to output variable
	add					EDI, 1					; increment
	MOV					EAX, quotient
	MOV					num, EAX
	inc					newStringLen
	jmp					_MainConversionLoop


_AddTERMINATOR:
	MOV					AL, 0
	MOV					[EDI], AL				; move result to output variable
	inc					newStringLen


_FinishConvertingNumtoString:

	;NEED TO REVERSE STRING AFTERWARDS
	MOV					ECX, newStringLen
	MOV					ESI, [EBP + 12]			; temp string offset from stack
	add					ESI, ECX				; so source strings starts from end
	dec					ESI
	dec					ESI
	MOV					EDI, [EBP + 16]			; temp string offset2 from stack
	
	CMP					negativeFlag, 2
	jz					_addNegativeSignBeforeReversal
	jmp					_revLoop

_addNegativeSignBeforeReversal:
	MOV					EAX, 45
	MOV					[EDI], EAX				; temp string offset2 from stack
	add					edi, 1

_revLoop:										;reference StringManipulator.asm from canvas
	STD
	LODSB
	CLD
	STOSB
	LOOP				_revLoop



	POPAD
	ret 12

ConvertNumtoASCII ENDP
```
