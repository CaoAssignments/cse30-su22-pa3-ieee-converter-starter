#
# Makefile for CSE30 -- PA3
# You should not need to change anything in this file
#

HEADERS		= pa3.h pa3Strings.h test.h

FPDEC_C_SRCS	= fpdec.c
FPDEC_EC_SRCS	= fpdecEC.s
FPDEC_EC2_SRCS	= fpdecEC2.c
ASM_SRCS	= parseNum.s extractParts.s

FPDEC_C_OBJS		= $(FPDEC_C_SRCS:.c=.o) 
FPDEC_EC_C_OBJS		= $(FPDEC_EC_SRCS:.s=.o)
FPDEC_EC2_C_OBJS	= $(FPDEC_EC2_SRCS:.c=.o)
ASM_OBJS		= $(ASM_SRCS:.s=.o)

FPDEC_OBJS	= $(FPDEC_C_OBJS) $(ASM_OBJS)
FPDEC_EC_OBJS	= $(FPDEC_EC_C_OBJS) $(ASM_OBJS)
FPDEC_EC2_OBJS	= $(FPDEC_EC2_C_OBJS) $(ASM_OBJS)

FPDEC_EXE	= fpdec
FPDEC_EC_EXE	= fpdecEC
FPDEC_EC2_EXE	= fpdecEC2
TEST_EXE	= testparseNum testextractParts

GCC		= gcc
ASM		= $(GCC)
RM		= rm

CFLAGS		= -g -O0 --std=gnu99
ASM_FLAGS	= -c -g
GCC_FLAGS	= -c -g -std=c11 -pedantic -Wall -D__EXTENSIONS__

#
# Standard rules
#
.s.o:
	@echo "Assembling each assembly source file separately ..."
	$(ASM) $(ASM_FLAGS) $<

.c.o:
	@echo "Compiling each C source file separately ..."
	$(GCC) $(GCC_FLAGS) $<

#
# Target to create executable
#
$(FPDEC_EXE): $(FPDEC_OBJS)
	$(RM) -f test*.o test*.ln
	$(GCC) $(CFLAGS)  -o $(FPDEC_EXE)  $(FPDEC_OBJS)
	@echo "Compilation Successful!"

#
# Target to create EC executable
#
$(FPDEC_EC_EXE): $(FPDEC_EC_OBJS)
	$(RM) -f test*.o test*.ln
	$(GCC) $(CFLAGS) -o $(FPDEC_EC_EXE) $(FPDEC_EC_OBJS)
	@echo "Compilation Successful!"

#
# Target to create EC2 executable
#
$(FPDEC_EC2_EXE): $(FPDEC_EC2_OBJS)
	$(RM) -f test*.o test*.ln
	$(GCC) $(CFLAGS) -o $(FPDEC_EC2_EXE) $(FPDEC_EC2_OBJS) -lm

clean:
	@echo "Cleaning up project directory ..."
	$(RM) -f $(FPDEC_EXE) $(FPDEC_EC_EXE) $(FPDEC_EC2_EXE) $(TEST_EXE) *.o *.ln
	@echo "Clean."


#
# Unit test targets
#
# --- Test for parseNum ---
testparseNum: $(HEADERS) parseNum.o testparseNum.o
	@echo "Compiling testparseNum.c"
	$(GCC) $(CFLAGS) -o testparseNum testparseNum.o parseNum.o
	@echo "Compilation successful!"

# --- Test for extractParts ---
testextractParts: $(HEADERS) extractParts.o testextractParts.o
	@echo "Compiling testextractParts.c"
	$(GCC) $(CFLAGS) -o testextractParts testextractParts.o extractParts.o
	@echo "Compilation successful!"
