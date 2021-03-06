#
# A GNU Makefile
#
# This Makefile builds the codeCoverage Dyninst mutator
#

# Make sure to set the DYNINST_ROOT environment variable to the directory where
# Dyninst is installed. The directory should contain the include and
# $(PLATFORM)/lib directories

# Or uncomment the following line and change the path accordingly
# DYNINST_ROOT = /change/me

ifeq ($(DYNINST_ROOT),)
$(error DYNINST_ROOT is not set)
endif

ifeq ($(PLATFORM),)
$(error PLATFORM is not set)
endif

DYNINST_INCLUDE = $(DYNINST_ROOT)/include
DYNINST_LIB =  $(DYNINST_ROOT)/$(PLATFORM)/lib

# These should point to where libelf and libdwarf are installed
LOCAL_INC_DIR = /usr/local/include
LOCAL_LIBS_DIR = /usr/local/lib

CXX = g++
CXXFLAGS = -g -Wall
LIBFLAGS = -fpic -shared

CC = gcc
CFLAGS = -Wall -pedantic -g -std=gnu99

all: codeCoverage libInst.so testcc

codeCoverage: codeCoverage.o
	$(CXX) $(CXXFLAGS) -L$(DYNINST_LIB) \
		-L$(LOCAL_LIBS_DIR) \
		-lcommon \
		-liberty \
		-ldyninstAPI \
		-lpthread \
		-o codeCoverage codeCoverage.o

codeCoverage-static: codeCoverage.o
	$(CXX) $(CXXFLAGS) -I$(DYNINST_INCLUDE) -L$(DYNINST_LIB) \
		-L$(LOCAL_LIBS_DIR) \
		-o codeCoverage-static codeCoverage.o \
		-Wl,-Bstatic \
		-ldyninstAPI \
		-lpatchAPI -lparseAPI -lstackwalk \
		-lsymLite -lsymtabAPI -ldynElf -ldynDwarf -linstructionAPI -lpcontrol \
		-ldwarf -lelf \
		-lcommon \
		-liberty \
		-Wl,-Bdynamic \
		-pthread \
		-ldl 

libInst.so: libInst.C
	$(CXX) $(CXXFLAGS) $(LIBFLAGS) libInst.C -o libInst.so  

codeCoverage.o: codeCoverage.C
	$(CXX) $(CXXFLAGS) -I$(LOCAL_INC_DIR) -I$(DYNINST_INCLUDE) -c codeCoverage.C

libtestcc.so: libtestcc.c libtestcc.h
	$(CC) $(CFLAGS) $(LIBFLAGS) -o libtestcc.so libtestcc.c

testcc: libtestcc.so testcc.c
	$(CC) $(CFLAGS) -o testcc testcc.c ./libtestcc.so

clean:
	rm -f codeCoverage codeCoverage-static testcc *.so *.o testcc.inst
