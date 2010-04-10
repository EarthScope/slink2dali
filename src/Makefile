
# Build environment can be configured the following
# environment variables:
#   CC : Specify the C compiler to use
#   CFLAGS : Specify compiler options to use

# Required compiler parameters
CFLAGS += -I../libslink -I../libdali -I../libmseed

LDFLAGS = -L../libslink -L../libdali -L../libmseed
LDLIBS  = -lslink -ldali -lmseed

# For SunOS/Solaris uncomment the following line
#LDLIBS = -lslink -ldali -lmseed -lsocket -lnsl -lrt

BIN  = ../slink2dali

OBJS = slink2dali.o

all: $(BIN)

$(BIN): $(OBJS)
	$(CC) $(CFLAGS) -o $(BIN) $(OBJS) $(LDFLAGS) $(LDLIBS)

static: $(OBJS)
	$(CC) $(CFLAGS) -static -o $(BIN) $(OBJS) $(LDFLAGS) $(LDLIBS)

cc:
	@$(MAKE) "CC=$(CC)" "CFLAGS=$(CFLAGS)"

debug:
	$(MAKE) "CC=$(CC)" "CFLAGS=-g $(CFLAGS)"

clean:
	rm -f $(OBJS) $(BIN)

install:
	@echo
	@echo "No install method, copy the binary and man page yourself"
	@echo

