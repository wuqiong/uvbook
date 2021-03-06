examples=\
	helloworld\
	idle-basic\
	uvcat\
	uvtee\
	onchange\
	thread-create\
	queue-work\
	progress\
	tcp-echo-server\
	dns\
	udp-dhcp\
	idle-compute\
	ref-timer\
	spawn\
	detach\
	proc-streams\
	cgi\
	pipe-echo-server\
	multi-echo-server\
	tty\
	tty-gravity\
	interfaces

UV_PATH=$(shell pwd)/../libuv
CFLAGS=-g -Wall -I$(UV_PATH)/include $(UV_PATH)/uv.a

uname_S=$(shell uname -s)

ifeq (Darwin, $(uname_S))
CFLAGS+=-framework CoreServices
SHARED_LIB_FLAGS=-bundle -undefined dynamic_lookup -o plugin/libhello.dylib
endif

ifeq (Linux, $(uname_S))
CFLAGS+=-lrt -ldl -lm -lev -pthread
SHARED_LIB_FLAGS=-shared -Wl,-soname,libhello.so -o plugin/libhello.so
PLUGIN_EXE_FLAGS=-Wl,-export-dynamic
endif

all: $(UV_PATH)/uv.a plugin/plugin proc-streams/test cgi/tick multi-echo-server/worker uvwget/uvwget
	for dir in $(examples); do cd $$dir; echo "--- `pwd`"; gcc $(CFLAGS) -o $$dir main.c $(UV_PATH)/uv.a; cd ..; done

plugin/plugin: plugin/*.c
	gcc $(CFLAGS) $(PLUGIN_EXE_FLAGS) -o plugin/plugin plugin/main.c plugin/plugin.c $(UV_PATH)/uv.a
	gcc -g -Wall -c -fPIC -o plugin/hello.o plugin/hello.c
	gcc $(SHARED_LIB_FLAGS) plugin/hello.o

proc-streams/test: proc-streams/test.c
	gcc -g -Wall -o proc-streams/test proc-streams/test.c

cgi/tick: cgi/tick.c
	gcc -g -Wall -o cgi/tick cgi/tick.c

multi-echo-server/worker: multi-echo-server/worker.c
	gcc $(CFLAGS) -o multi-echo-server/worker multi-echo-server/worker.c $(UV_PATH)/uv.a

uvwget/uvwget: uvwget/main.c
	gcc $(CFLAGS) `curl-config --cflags --libs` -o uvwget/uvwget uvwget/main.c $(UV_PATH)/uv.a

$(UV_PATH)/uv.a:
	make -C ../libuv

clean:
	for dir in $(examples); do cd $$dir; rm -f $$dir; cd ..; done
	rm -rf plugin/*.o plugin/libhello.*
	rm -rf plugin/plugin
	rm -rf proc-streams/test
	rm -rf cgi/tick
	rm -rf multi-echo-server/worker
	rm -rf uvwget/uvwget

.PHONY: clean
