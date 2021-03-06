SRC_DIRS    = ./hwloc
MAKE_DIR   = ../../make

#DO NOT EDIT BELOW

include ../../config.mk
include $(MAKE_DIR)/include_$(COMPILER).mk
include $(MAKE_DIR)/config_checks.mk
include $(MAKE_DIR)/config_defines.mk

CFLAGS    = -O2 -Wall -fPIC -fvisibility=hidden
INCLUDES  += -I./include
#DEFINES   =
LIBS      = -L. -lm
LFLAGS    = -Wno-unused-result -fPIC -fvisibility=hidden
Q         ?= @
DEFINES := $(filter-out -DVERSION=$(VERSION),$(DEFINES))
ifeq ($(DEBUG),true)
DEBUG_FLAGS = -g
else
DEBUG_FLAGS =
endif
ifeq ($(COMPILER),MIC)
CFLAGS += -mmic
LFLAGS += -mmic
endif

#CONFIGURE BUILD SYSTEM
BUILD_DIR  = ./$(COMPILER)

VPATH     = $(SRC_DIRS)
FILES     = $(notdir $(foreach dir,$(SRC_DIRS),$(wildcard $(dir)/*.c)))
OBJ       = $(patsubst %.c, $(BUILD_DIR)/%.o, $(FILES))
LIBHWLOC = $(shell basename $(TARGET_HWLOC_LIB))

CPPFLAGS := $(CPPFLAGS) $(DEFINES) $(INCLUDES)

all: $(BUILD_DIR) $(OBJ) $(LIBHWLOC)

$(BUILD_DIR):
	@mkdir $(BUILD_DIR)


$(STATIC_LIBHWLOC): $(OBJ)
	$(Q)${AR} -cq $(LIBHWLOC) $(OBJ)

$(SHARED_LIBHWLOC): $(OBJ)
	$(Q)$(CC) $(DEBUG_FLAGS) $(LFLAGS) -Wl,-soname,$(LIBHWLOC).$(VERSION)  -Wall -shared -fPIC -o $(LIBHWLOC) $(OBJ) $(LIBS)

#PATTERN RULES
$(BUILD_DIR)/%.o:  %.c
	$(Q)$(CC) -c $(DEBUG_FLAGS) $(CFLAGS) $(CPPFLAGS) $< -o $@
	$(Q)$(CC) $(DEBUG_FLAGS) $(CPPFLAGS) -MT $(@:.d=.o) -MM  $< > $(BUILD_DIR)/$*.d

ifeq ($(findstring $(MAKECMDGOALS),clean),)
-include $(OBJ:.o=.d)
endif

.PHONY: clean distclean

clean:
	@rm -f $(TARGET) $(SHARED_LIBHWLOC) $(STATIC_LIBHWLOC)

distclean: clean
	@rm -f $(TARGET) $(SHARED_LIBHWLOC) $(STATIC_LIBHWLOC)
	@rm -rf $(BUILD_DIR)



