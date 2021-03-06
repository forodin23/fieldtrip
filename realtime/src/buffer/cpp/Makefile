# Copyright (C) 2010 Stefan Klanke
# Donders Institute for Donders Institute for Brain, Cognition and Behaviour,
# Centre for Cognitive Neuroimaging, Radboud University Nijmegen,
# Kapittelweg 29, 6525 EN Nijmegen, The Netherlands

# Use MinGW for compilation on Windows

ifneq "$(OS)" "Windows_NT"
	OS      ?= $(shell uname -s)
	MACHINE ?= $(shell uname -m)
endif

PLATFORM ?= $(shell gcc -dumpmachine)

FIELDTRIP = ../../../..
FTBUFFER  = $(FIELDTRIP)/realtime/src/buffer

# defaults, might be overwritten further down
CC       = gcc
CXX      = g++
CFLAGS   = -O2 -w # -Wunused -Wall -pedantic
CXXFLAGS = -O2 -w # -Wunused -Wall -pedantic
INCPATH  = -I../src -I.
LIBPATH  = -L../src
LDLIBS   = -lpthread -lbuffer
SUFFIX   =

ifeq "$(PLATFORM)" "i686-pc-cygwin"
	BINDIR   = $(FIELDTRIP)/realtime/bin/win32
	LDLIBS  += -lm -lws2_32
	SUFFIX   = .exe
	fixpath  = $1
	RM       = rm -f
endif

ifeq "$(PLATFORM)" "x86_64-pc-cygwin"
	BINDIR   = $(FIELDTRIP)/realtime/bin/win64
	LDLIBS  += -lm -lws2_32
	SUFFIX   = .exe
	fixpath  = $1
	RM       = rm -f
endif

ifeq "$(PLATFORM)" "i686-w64-mingw32"
	BINDIR   = $(FIELDTRIP)/realtime/bin/win32
	# prevent dependency on libwinpthread-1.dll by linking statically
	# see http://stackoverflow.com/questions/13768515/how-to-do-static-linking-of-libwinpthread-1-dll-in-mingw
	LDLIBS  += -lws2_32 -lwinmm -static
	SUFFIX   = .exe
	RM       = del
	# make clean target work on windows:
	fixpath = $(subst /,\,$1)
endif

ifeq "$(PLATFORM)" "x86_64-w64-mingw32"
	BINDIR   = $(FIELDTRIP)/realtime/bin/win64
	# prevent dependency on libwinpthread-1.dll by linking statically
	# see http://stackoverflow.com/questions/13768515/how-to-do-static-linking-of-libwinpthread-1-dll-in-mingw
	LDLIBS  += -lws2_32 -lwinmm -static
	SUFFIX   = .exe
	RM       = del
	# make clean target work on windows:
	fixpath = $(subst /,\,$1)
endif

ifeq "$(OS)" "Linux"
	fixpath = $1
	LDLIBS += -ldl -lrt
	ifeq "$(MACHINE)" "i686"
		BINDIR = $(FIELDTRIP)/realtime/bin/glnx86
	endif
	ifeq "$(MACHINE)" "x86_64"
		BINDIR = $(FIELDTRIP)/realtime/bin/glnxa64
	endif
	ifeq "$(MACHINE)" "armv6l"
		BINDIR = $(FIELDTRIP)/realtime/bin/raspberrypi
	endif
	ifeq "$(MACHINE)" "armv7l"
		BINDIR = $(FIELDTRIP)/realtime/bin/raspberrypi
	endif
endif

ifeq "$(OS)" "Darwin"
	fixpath = $1
	LDLIBS += -ldl
	ifeq "$(MACHINE)" "i386"
		BINDIR    = $(FIELDTRIP)/realtime/bin/maci
		CFLAGS   += -m32
		CXXFLAGS += -m32
		LDFLAGS  += -m32
	endif
	ifeq "$(MACHINE)" "x86_64"
		BINDIR    = $(FIELDTRIP)/realtime/bin/maci64
		CFLAGS   += -m64
		CXXFLAGS += -m64
		LDFLAGS  += -m64
	endif
endif

ifndef BINDIR
$(error Unsupported platform: $(PLATFORM) :/.)
endif

INCLUDES = $(wildcard *.h)

TARGETS = $(patsubst %, $(BINDIR)/%$(SUFFIX), odmTest)

##############################################################################
all: odmTest$(SUFFIX)

%.o: %.cc ${INCLUDES}
	$(CXX) $(CXXFLAGS) $(INCPATH) -c $<

odmTest$(SUFFIX): odmTest.o SignalConfiguration.o GdfWriter.o FtConnection.o StringServer.o
	$(CXX) -o $(BINDIR)/$@ $^ $(LDFLAGS) $(LIBPATH) $(LDLIBS)

clean:
	$(RM) core *.o *.obj *.a $(call fixpath, $(TARGETS))
