include ../../config.mk
CXX = $(MINGW_CXX)

# Pass -Wno-format to disable format checking because gcc complains about
# %I64x.  I can't use %lld because it's broken on MinGW-on-WinXP, though it
# works on later OSs.  %I64x works everywhere with MinGW, at runtime.  I
# need to find a better way to do this int-to-string conversion.
CXXFLAGS += \
    -MMD \
    -Wall \
    -Wno-format \
    -Iharness -I../../shared \
    -std=c++11 \
    -DUNICODE \
    -D_UNICODE \
    -D_WIN32_WINNT=0x0600
LDFLAGS += -static -static-libgcc -static-libstdc++

# To disable PCH, just comment out these two lines.
PCHFLAGS = -include build/obj/pch.h
PCHDEPS = build/obj/pch.h.gch

# Use gmake -n to see the command-lines gmake would run.

COMMON_OBJECTS = \
    build/obj/harness/Event.o \
    build/obj/harness/NtHandleQuery.o \
    build/obj/harness/ShmemParcel.o \
    build/obj/harness/Spawn.o \
    build/obj/harness/UnicodeConversions.o \
    build/obj/harness/Util.o \
    build/obj/shared/DebugClient.o \
    build/obj/shared/WinptyAssert.o \
    build/obj/shared/winpty_wcsnlen.o

WORKER_OBJECTS = \
    build/obj/harness/WorkerProgram.o

TEST_OBJECTS = \
    build/obj/harness/RemoteHandle.o \
    build/obj/harness/RemoteWorker.o \
    build/obj/harness/TestUtil.o \

HANDLETESTS_OBJECTS = \
    build/obj/HandleTests/CreateProcess.o \
    build/obj/HandleTests/CreateProcess_Detached.o \
    build/obj/HandleTests/CreateProcess_Duplicate.o \
    build/obj/HandleTests/CreateProcess_Duplicate_PseudoHandleBug.o \
    build/obj/HandleTests/CreateProcess_Duplicate_XPPipeBug.o \
    build/obj/HandleTests/CreateProcess_InheritAllHandles.o \
    build/obj/HandleTests/CreateProcess_InheritList.o \
    build/obj/HandleTests/CreateProcess_NewConsole.o \
    build/obj/HandleTests/CreateProcess_UseStdHandles.o \
    build/obj/HandleTests/MiscTests.o \
    build/obj/HandleTests/Modern.o \
    build/obj/HandleTests/Traditional.o \
    build/obj/HandleTests/Win7_Conout_Crash.o \
    build/obj/HandleTests/main.o

include tests.mk

.PHONY : all
all : \
    $(TESTS) \
    build/Worker.exe \
    build/HandleTests.exe \
    build/HandleTests.exe.manifest

.PHONY : clean
clean:
	rm -fr build

build/obj/pch.h.gch : harness/pch.h
	@echo Compiling PCH $<
	@mkdir -p $$(dirname $@)
	@$(CXX) $(CXXFLAGS) $(CPPFLAGS) -c -o $@ $<

build/obj/%.o : %.cc $(PCHDEPS)
	@echo Compiling $<
	@mkdir -p $$(dirname $@)
	@$(CXX) $(PCHFLAGS) $(CXXFLAGS) $(CPPFLAGS) -c -o $@ $<

build/obj/shared/%.o : ../../shared/%.cc $(PCHDEPS)
	@echo Compiling $<
	@mkdir -p $$(dirname $@)
	@$(CXX) $(PCHFLAGS) $(CXXFLAGS) $(CPPFLAGS) -c -o $@ $<

.PRECIOUS : build/obj/%.o
.PRECIOUS : build/obj/shared/%.o

build/Worker.exe : $(WORKER_OBJECTS) $(COMMON_OBJECTS)
	@echo Linking $@
	@$(CXX) -o $@ $^ $(LDFLAGS)

build/HandleTests.exe : $(HANDLETESTS_OBJECTS) $(TEST_OBJECTS) $(COMMON_OBJECTS)
	@echo Linking $@
	@$(CXX) -o $@ $^ $(LDFLAGS)

build/HandleTests.exe.manifest : manifest.xml
	@Echo Copying $< to $@
	@mkdir -p $$(dirname $@)
	@cp $< $@

build/%.exe : build/obj/%.o $(TEST_OBJECTS) $(COMMON_OBJECTS)
	@echo Linking $@
	@$(CXX) -o $@ $^ $(LDFLAGS)

-include $(COMMON_OBJECTS:.o=.d)
-include $(WORKER_OBJECTS:.o=.d)
-include $(TEST_OBJECTS:.o=.d)
-include $(HANDLETESTS_OBJECTS:.o=.d)
-include build/obj/*.d
