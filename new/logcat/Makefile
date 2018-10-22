# ############################################
# makefile to build logcat natively for linux:
CROSS_COMPILE ?=
CC  =$(CROSS_COMPILE)gcc
CXX =$(CROSS_COMPILE)g++
RANLIB =$(CROSS_COMPILE)ranlib

openssldir ?= .
top_srcdir ?= ..
srcdir = $(top_srcdir)/logcat
VPATH = $(srcdir)
include $(top_srcdir)/include.mk

LOCAL_PATH:= $(shell cd $(top_srcdir) && pwd)
logcat_version := $(shell git -C $(LOCAL_PATH) rev-parse --short=12 HEAD 2>/dev/null)-android

LIBLOGCAT_SRC_FILES := \
	logcat.cpp

LIBADB_linux_SRC_FILES := \
    fdevent.cpp \
    get_my_path_linux.cpp \

LOCAL_SRC_FILES := \
    $(LIBLOGCAT_SRC_FILES) \
    $(LIBADB_linux_SRC_FILES) \
    adb_auth_client.cpp \
    fdevent.cpp

LOCAL_SRC_FILES += \
    services.cpp \
    file_sync_service.cpp \
    framebuffer_service.cpp \
    remount_service.cpp \
    set_verity_enable_state_service.cpp \

# DISABLED SOURCES for ADB_NON_ANDROID:
#   qemu_tracing.cpp
#   usb_linux.cpp
#   jdwp_service.cpp

LOCAL_SRC_C_FILES := \
	usb_linux_client.c

LOCAL_CFLAGS := -DADB_HOST=0 -Wall -Wno-unused-parameter
LOCAL_CFLAGS += -D_XOPEN_SOURCE -D_GNU_SOURCE -DHAVE_PTHREADS=1
LOCAL_CFLAGS += -DADB_NON_ANDROID=1 -DADB_REVISION='"$(adb_version)"'
LOCAL_CFLAGS += -DPROP_NAME_MAX=32 -DPROP_VALUE_MAX=92 -DALLOW_ADBD_NO_AUTH=1
LOCAL_INCLUDES := -I$(top_srcdir)/include \
			      -I$(top_srcdir)/base/include/ \
				  -I$(top_srcdir)/libcrypto_utils/include/ \
				  -I$(srcdir) \
				  -I$(openssldir)/include

LOCAL_MODULE := logcat

LOCAL_FORCE_STATIC_EXECUTABLE := true
LOCAL_MODULE_PATH := $(TARGET_ROOT_OUT_SBIN)
LOCAL_UNSTRIPPED_PATH := $(TARGET_ROOT_OUT_SBIN_UNSTRIPPED)

LOCAL_STATIC_LIBRARIES := libcutils libc

########################################

OBJS = $(LOCAL_SRC_FILES:.cpp=.o) $(LOCAL_SRC_C_FILES:.c=.o)
CXXFLAGS = $(OPT_CXXFLAGS) -std=c++11 $(LOCAL_CFLAGS) $(LOCAL_INCLUDES)
CFLAGS= $(OPT_CFLAGS) $(LOCAL_CFLAGS) $(LOCAL_INCLUDES)

%.o: %.cpp
	${CXX} -g -c -fPIC $(CXXFLAGS) $(LFLAGS) $< -o $@
	@#g++ -g -c -fPIC $(CXXFLAGS) $(LFLAGS) $< -o $@

%.o: %.c
	${CC} -g -c -fPIC $(CFLAGS) $(LFLAGS) $< -o $@
	@#gcc -g -c -fPIC $(CFLAGS) $(LFLAGS) $< -o $@

#all: $(LOCAL_MODULE) xdg-adbd
all: $(LOCAL_MODULE)

$(LOCAL_MODULE): $(OBJS)
	${CXX} -fPIC $(CXXFLAGS) $^ -L ../libcutils/*.o ../base/*.o ../libcrypto_utils/*.o -lpthread -lresolv -lutil -L $(openssldir) -lcrypto -lssl -lutil -ldl -o $@
	@#g++ -fPIC $(CXXFLAGS) $^ -L ../libcutils/*.o ../base/*.o ../libcrypto_utils/*.o -lpthread -lresolv -lcrypto -lssl -lutil -o $@

xdg-adbd: xdg-adbd.c
	${CC} -o $@ $< $(CFLAGS) $(LFLAGS) `pkg-config --libs --cflags glib-2.0`
	@#gcc -o $@ $< $(CFLAGS) $(LFLAGS) `pkg-config --libs --cflags glib-2.0`

clean:
	rm -f $(LOCAL_MODULE) xdg-adbd *.o daemon/*.o
