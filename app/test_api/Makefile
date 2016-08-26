# Figure out app path
TEST_DIR := $(shell readlink $(dir $(lastword $(MAKEFILE_LIST))) -f)
PETUUM_ROOT = $(TEST_DIR)/../../

include $(PETUUM_ROOT)/defns.mk

PETUUM_INCFLAGS+= ${HAS_HDFS}

# Define macros in src/matrix_loader.hpp
# Element in matrix whose absolute value is smaller than INFINITESIMAL is 
# considered 0 at output
PETUUM_CXXFLAGS += -DINFINITESIMAL=0.00001
# Element in matrix is set to MAXELEVAL if its value exceeds MAXELEVAL
# This bound is to prevent numeric overflow
PETUUM_CXXFLAGS += -DMAXELEVAL=100000
# Element in matrix is set to MINELEVAL if its value is lower than MINELEVAL
# This bound is to prevent numeric overflow
PETUUM_CXXFLAGS += -DMINELEVAL=-100000

PETUUM_CXXFLAGS += -static-libstdc++

TEST_SRC = $(wildcard $(TEST_DIR)/*.cpp)
TEST_HDR = $(wildcard $(TEST_DIR)/*.hpp)
TEST_BIN = $(TEST_DIR)/bin
TEST_OBJ = $(TEST_SRC:.cpp=.o)

all: test_api 

test_api: $(TEST_BIN)/test_api

$(TEST_BIN):
	mkdir -p $(TEST_BIN)

$(TEST_BIN)/test_api: $(TEST_OBJ) $(PETUUM_PS_LIB) $(PETUUM_ML_LIB) $(TEST_BIN)
	$(PETUUM_CXX) $(PETUUM_CXXFLAGS) $(PETUUM_INCFLAGS) \
	$(TEST_OBJ) $(PETUUM_PS_LIB) $(PETUUM_ML_LIB) $(PETUUM_LDFLAGS) -o $@

$(TEST_OBJ): %.o: %.cpp $(TEST_HDR)
	$(PETUUM_CXX) $(NDEBUG) $(PETUUM_CXXFLAGS) -Wno-unused-result $(PETUUM_INCFLAGS) -c $< -o $@


clean:
	rm -rf $(TEST_OBJ)
	rm -rf $(TEST_BIN)/test_api

.PHONY: clean test_api
