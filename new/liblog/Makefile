CROSS_COMPILE ?=
CC  =$(CROSS_COMPILE)gcc
CXX =$(CROSS_COMPILE)g++
RANLIB =$(CROSS_COMPILE)ranlib

top_srcdir ?= ..
srcdir = $(top_srcdir)/libcutils
VPATH = $(srcdir)
include $(top_srcdir)/include.mk

targetSmpFlag := -DANDROID_SMP=1

commonSources := \
	config_read.c \
	config_write.c \
	event_tag_map.c \
	fake_log_device.c \
	fake_writer.c \
	log_event_list.c \
	log_event_write.c \
	log_is_loggable.c \
	logd_reader.c \
	logd_writer.c \
	logger_lock.c \
	logger_name.c \
	logger_read.c \
	logger_write.c \
	logprint.c \
	pmsg_reader.c \
	pmsg_writer.c \
	uio.c

# Shared and static library for target
# ========================================================
LOCAL_MODULE := libcutils.a
LOCAL_SRC_FILES := $(commonSources) \
#	ashmem-dev.c \
	mq.c \
	android_reboot.c \
	partition_utils.c \
	uevent.c \
	qtaguid.c \
	klog.c
# LOCAL_SRC_FILES += memory.c

LOCAL_CXX_SRC_FILES := \
	log_time.cpp 

LOCAL_C_INCLUDES := $(KERNEL_HEADERS)
LOCAL_STATIC_LIBRARIES := liblog
LOCAL_CFLAGS += $(targetSmpFlag)

################################

OBJS = $(LOCAL_SRC_FILES:.c=.o) $(LOCAL_CXX_SRC_FILES:.cpp=.o)
EXTRA_CFLAGS = $(LOCAL_CFLAGS) -I$(top_srcdir)/include -I$(srcdir) -DHAVE_PTHREADS=1 -DADB_NON_ANDROID=1 \
		-DLIBLOG_LOG_TAG=1005 -DSNET_EVENT_LOG_TAG=1397638484
EXTRA_CFLAGS += -DPROP_NAME_MAX=32 -DPROP_VALUE_MAX=92
CFLAGS = $(OPT_CFLAGS) $(EXTRA_CFLAGS)
CXXFLAGS = $(OPT_CXXFLAGS) $(EXTRA_CFLAGS)

all: $(LOCAL_MODULE)

%.o: %.cpp
	${CXX} -c -fPIC $(CXXFLAGS) $(LFLAGS) $< -o $@
	@#g++ -c -fPIC $(CXXFLAGS) $(LFLAGS) $< -o $@

%.o: %.c
	${CC} -c -fPIC $(CFLAGS) $(LFLAGS) $< -o $@
	@#gcc -c -fPIC $(CFLAGS) $(LFLAGS) $< -o $@

$(LOCAL_MODULE): $(OBJS)
	${AR} rcs $@ $?
	${RANLIB} $@
	@#ar rcs $@ $?
	@#ranlib $@

clean:
	rm -f $(LOCAL_MODULE) *.o

