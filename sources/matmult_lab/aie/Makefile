# Copyright (C) 2023 Advanced Micro Devices, Inc
#
# SPDX-License-Identifier: MIT

PLATFORM := xilinx_vck5000_gen4x8_xdma_2_202210_1
TARGET := hw

XPFM = $(shell platforminfo -p $(PLATFORM) --json="file")
XSA = $(strip $(patsubst %.xpfm, % , $(shell basename $(PLATFORM))))

# OUTPUT PRODUCTS 
BUILD_DIR = build.$(TARGET)
WORK_DIR = work
SRC_DIR = $(shell readlink -f src/)
DATA_DIR = $(shell readlink -f data/)

# DEPENDENCIES for make aie
GRAPH_CPP := $(SRC_DIR)/graph.cpp
DEPS := $(GRAPH_CPP)
DEPS += $(SRC_DIR)/aie_kernels.h
DEPS += $(SRC_DIR)/graph.h
DEPS += $(SRC_DIR)/system_settings.h
DEPS += $(SRC_DIR)/aie_kernels/matmult_float.cpp
DEPS += $(SRC_DIR)/aie_kernels/matmult_generic.h
DEPS += $(SRC_DIR)/aie_kernels/matmult_int32.cpp
DEPS += $(SRC_DIR)/aie_kernels/matmult_int16.cpp
DEPS += $(SRC_DIR)/aie_kernels/matmult_int8.cpp
AIE_FLAGS += --platform=$(XPFM)

all: $(BUILD_DIR)/libadf.a

$(BUILD_DIR)/libadf.a: $(DEPS)
	@mkdir -p $(BUILD_DIR);
	cd $(BUILD_DIR); \
	aiecompiler -v --target=$(TARGET) \
		--stacksize=1024 \
		-include="$(XILINX_VITIS)/aietools/include" \
		-include="$(SRC_DIR)"  \
		-include="$(DATA_DIR)" \
		$(AIE_FLAGS) \
		$(GRAPH_CPP) \
		-workdir=$(WORK_DIR) 2>&1 | tee aiecompiler.log

clean:
	rm -rf $(BUILD_DIR)

aieemu:
	cd $(BUILD_DIR); \
	aiesimulator --pkg-dir=$(WORK_DIR) --i=.. --profile --dump-vcd=foo --simulation-cycle-timeout=200000

x86sim:
	cd $(BUILD_DIR); \
	x86simulator --pkg-dir=$(WORK_DIR) --i=..