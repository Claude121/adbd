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

LOCAL_SRC_FILES := \

LOCAL_SRC_FILES += \

# DISABLED SOURCES for ADB_NON_ANDROID:
#   qemu_tracing.cpp
#   usb_linux.cpp
#   jdwp_service.cpp

LOCAL_SRC_C_FILES := \

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

########################################

OBJS = $(LOCAL_SRC_FILES:.cpp=.o) $(LOCAL_SRC_C_FILES:.c=.o)
CXXFLAGS = $(OPT_CXXFLAGS) -std=c++11 $(LOCAL_CFLAGS) $(LOCAL_INCLUDES)
CFLAGS= $(OPT_CFLAGS) $(LOCAL_CFLAGS) $(LOCAL_INCLUDES)

%.o: %.cpp
	${CXX} -g -c -fPIC $(CXXFLAGS) $(LFLAGS) $< -o $@

%.o: %.c
	${CC} -g -c -fPIC $(CFLAGS) $(LFLAGS) $< -o $@

all: $(LOCAL_MODULE)

$(LOCAL_MODULE): $(OBJS)
	${CXX} -fPIC $(CXXFLAGS) $^ -L../liblog -L../base/ -L../libcutils/ -L../libpcrecp -llog -lbase -lcutils -lpcrecp -o $@
	@#${CXX} -fPIC $(CXXFLAGS) $^ -L ../libcutils/*.o ../base/*.o ../libcrypto_utils/*.o -lpthread -lresolv -lutil -L $(openssldir) -lcrypto -lssl -lutil -ldl -o $@

xdg-adbd: xdg-adbd.c
	${CC} -o $@ $< $(CFLAGS) $(LFLAGS) `pkg-config --libs --cflags glib-2.0`

clean:
	rm -f $(LOCAL_MODULE)

