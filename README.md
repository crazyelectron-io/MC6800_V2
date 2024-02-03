May 16 2022, Herb Johnson

On May 13 or so, Mike provided a newer listing of his "Version 2" of SmithBug. It
includes his S-record support that he added to his "V1" SmithBug. Since it
was produced later (about 1980), Mike says it has corrections to some of the
V1 code. I extracted this source listing, as a file. In this ZIP archive I call it
"MC6800_listing.txt" and "MC6800_symbols.txt".

So I adjusted this new V2 assembly code, to assemble to match the V1 binary I have,
at the same addresses for code and data as my V1 binary. Then I assembled it for binary comparison.
Those new V2 source and assembled files, in this ZIP, are "sm.asm, sm.lst, sm.hex
and sm.bin".

So any differences would be due to changes in the code, not just differences
in address or any shift of address from code modifications. Here's the differences
I found. - Herb Johnson

1) different addresses for the "ACIA Initialise" locations, early in the code. 
   Simply due to different hardware. 

2) After the "CR8: BRA IFILL1" at $F9FC, the V2 source has this code,
called by a patched-in different version of INEEE:

              ;SAVE X REGISTER 
              ; 
                        
   C1EF FF 7F14         SAV            STX     XTEMP 
   C1F2 39                             RTS 

   C1F3 8D FA           INEEE          BSR     SAV

followed by the remaining code for INEEE, with slightly different addresses:

   C1F5 B6 8018         IN1            LDAA    ACISCA
                                       ASR A
                                       BCC     INEEE
                                       ...

.. followed by a different version of OUTEEE at $F9FE thru $FA12. 
There's a bunch of NOPs to fill out the space. 

   f9fe   36            OUTEEE          PSH A 
   f9ff   b6 80 18      OUTEEE1         LDA A   ACIACS 
   fa02   47                            ASR A 
   fa03   47                            ASR A 
   fa04   24 f9                         BCC     OUTEEE1 
   fa06   32                            PUL A 
   fa07   b7 80 19                      STA A   ACIADA 
   fa0a   39                            RTS
                         
   fa0b   01                            NOP
   fa0c   01                            NOP
   fa0d   01                            NOP
   fa0e   01                            NOP
   fa0f   01                            NOP
   fa10   01                            NOP
   fa11   01                            NOP
   fa12   01                            NOP   

... followed by identical code for the JUMP command code and so on.

3) The V2 code correctly has the $3B RTI code: 
   fcd3   81 3B        NOTRTS:          CMP A   #$3B 

   the V1 code incorrectly has $38:

   fcd3   81 38        NOTRTS:          CMP A  #$38 

4) The V1 and earlier V2 source in the TBLKUP op-code lookup routine, 
  has additional code not in the newer V2 source. 
  PULA is $32, PSHA is $36, PSHB is $33

   fd1e   81 32         CMP A   #$32   ;  
   fd20   27 11         BEQ     IMLR3
   fd22   81 36         cmp a   #$36   ; code in older code,  not in newer V2
                                       ; also flagged as having £ symbol
   fd24   27 0d         beq     IMLR3  ; code in older code,  not in newer V2
   fd26   81 33         CMP A   #$33 
   fd28   27 0e         BEQ     IMLR4 

5) In jump table for SLOAD address after "&" symbol

   V2 code has  SLOAD and the the address is to the S-record routine
   V1 code has  a jump to another program address.


====================================

The new V2 code, has S-record output code after the V1 SmithBug code.
There's no V1 code to compare, so look at the new and old V2 listings to compare. 
Compared to the older V2 source, here's what I see.

1) The assembled new V2 code, has a long string for "this S1 load..." For some reason,
I don't think the assembler for the listing as given to me, included every byte of that 
string. I think it lost the last few bytes of the string. My assembler of course assembled
the whole string. 

2) Other than a few address differences due to different locations, the code for the
S-record processing looks *the same*. 


