VERSION=0.8.0

BISON ?= bison
FLEX ?= flex
CFLAGS ?= -Wall -Wextra

# _GNU_SOURCE is for asprintf
EXTRA_CFLAGS = -D_GNU_SOURCE -DPROGRAM_VERSION=$(VERSION) -std=c99

ifneq ($(DEBUG),)
EXTRA_CFLAGS += -DDEBUG
endif

CFLAGS += -DPROGRAM_VERSION=$(VERSION)

OBJS = nerves_initramfs.o util.o crc32.o uboot_env.o lex.yy.o parser.tab.o script.o linenoise.o block_device.o cmd.o rootdisk.o

ifeq ($(shell uname),Darwin)
EXTRA_CFLAGS += -Icompat
OBJS += compat/compat.o
endif

init: $(OBJS)
	$(CC) $(LDFLAGS) -o $@ $^

%.o: %.c
	$(CC) $(CFLAGS) $(EXTRA_CFLAGS) -c -o $@ $^

parser.tab.c parser.tab.h: parser.y
	$(BISON) -d $(BISONFLAGS) $<

lex.yy.c: lexer.l parser.tab.h
	$(FLEX) $<

clean:
	$(RM) init $(OBJS) parser.tab.c parser.tab.h lex.yy.c

format: script.c
	astyle --style=kr --indent=spaces=4 --align-pointer=name --align-reference=name --convert-tabs --attach-namespaces --max-code-length=100 --max-instatement-indent=120 --pad-header --pad-oper $^

.PHONY: all clean format
