#
# compiler
#
CC	        = g++
CXXFLAGS	= -Wall -Wextra -std=c++11 -O3 -DNDEBUG -DSCOTS_BDD
#
# scots
#
SCOTSROOT	= ../../../../ext/SCOTSv2.0
SCOTSINC	= -I$(SCOTSROOT)/src -I$(SCOTSROOT)/utils
#
# cudd
#
CUDD		= -I/usr/local/include -L/usr/local/lib -lcudd
#
# OPTDET
#
OPTDETROOT      = ../../../../src/
OPTDETINC       = -I$(OPTDETROOT)/optdet/

.PHONY: dcdc

TARGET = dcdc

all: $(TARGET)

dcdc:
	$(CC) $(CXXFLAGS) $(SCOTSINC) $(OPTDETINC) dcdc.cc -o dcdc $(CUDD)

clean:
	rm dcdc
