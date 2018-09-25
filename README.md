# embedded-systems
Repository for EE319K Lab code - for educational purposes only.
GPIO_PORTE_DATA_R  EQU 0x400243FC
GPIO_PORTE_DIR_R   EQU 0x40024400
GPIO_PORTE_AFSEL_R EQU 0x40024420
GPIO_PORTE_DEN_R   EQU 0x4002451C
; PortF device registers
GPIO_PORTF_DATA_R  EQU 0x400253FC
GPIO_PORTF_DIR_R   EQU 0x40025400
GPIO_PORTF_AFSEL_R EQU 0x40025420
GPIO_PORTF_PUR_R   EQU 0x40025510
GPIO_PORTF_DEN_R   EQU 0x4002551C
GPIO_PORTF_LOCK_R  EQU 0x40025520
GPIO_PORTF_CR_R    EQU 0x40025524
GPIO_LOCK_KEY      EQU 0x4C4F434B  ; Unlocks the GPIO_CR register
SYSCTL_RCGCGPIO_R  EQU 0x400FE608

       IMPORT  TExaS_Init
       THUMB
       AREA    DATA, ALIGN=2
;global variables go here


       AREA    |.text|, CODE, READONLY, ALIGN=2
       THUMB
       EXPORT  Start
Start
 ; TExaS_Init sets bus clock at 80 MHz
     BL  TExaS_Init ; voltmeter, scope on PD3
 ; Initialization goes here
	LDR R1, =SYSCTL_RCGCGPIO_R ;clock
	LDR R0, [R1]
	ORR R0, R0, #0x10 ; setting bit 4 for port E
	STR R0, [R1] ;storing to clock
	NOP ;delay for clock
	NOP
	LDR R1, =GPIO_PORTE_DIR_R
	LDR R0, [R1]
	AND R0, #0XF0 ;equivalent to MOV R0, #0X08 
	ORR R0, #0X08 ;0 input 1 output pe3 outpu
	STR R0, [R1]
	LDR R1, =GPIO_PORTE_DEN_R
	MOV R0, #0X08 ;initalizing IO only need ports 0-3
	STR R0, [R1]
	

     CPSIE  I    ; TExaS voltmeter, scope runs on interrupts
loop  
; main engine goes here
		LDR R1, =GPIO_PORTE_DATA_R
		MOV R0, #0x08
		STR R0, [R1]
		MOV R0, #90002
wait 	SUBS R0, R0, #0x01
		BNE  wait
		LDR R1, =GPIO_PORTE_DATA_R
		MOV R0, #0x00
		STR R0, [R1]
		MOV R0, #9999
wait1 	SUBS R0,R0,#0x01
		BNE  wait1
		B    loop

      
     ALIGN      ; make sure the end of this section is aligned
     END        ; end of file
