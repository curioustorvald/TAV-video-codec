# Created by CuriousTorvald and Claude on 2025-08-17.
# Makefile for TSVM Enhanced Video (TEV) encoder

CC = gcc
CXX = g++
CFLAGS = -std=c99 -Wall -Wextra -O2 -D_GNU_SOURCE
CXXFLAGS = -std=c++11 -Wall -Wextra -O2 -D_GNU_SOURCE

# Zstd flags (use pkg-config if available, fallback for cross-platform compatibility)
ZSTD_CFLAGS = $(shell pkg-config --cflags libzstd 2>/dev/null || echo "")
ZSTD_LIBS = $(shell pkg-config --libs libzstd 2>/dev/null || echo "-lzstd")
LIBS = -lm $(ZSTD_LIBS)

# OpenCV flags (for TAV encoder with mesh warping)
OPENCV_CFLAGS = $(shell pkg-config --cflags opencv4)
OPENCV_LIBS = $(shell pkg-config --libs opencv4)

# Source files and targets
TARGETS = tav tav_inspector

# Build all encoders
all: $(TARGETS)

# Build main encoder

tav: encoder_tav.c encoder_tad.c encoder_tav_opencv.cpp
	rm -f encoder_tav encoder_tav.o encoder_tad.o encoder_tav_opencv.o
	$(CC) $(CFLAGS) $(ZSTD_CFLAGS) -c encoder_tav.c -o encoder_tav.o
	$(CC) $(CFLAGS) $(ZSTD_CFLAGS) -c encoder_tad.c -o encoder_tad.o
	$(CXX) $(CXXFLAGS) $(OPENCV_CFLAGS) $(ZSTD_CFLAGS) -c encoder_tav_opencv.cpp -o encoder_tav_opencv.o
	$(CXX) -o encoder_tav encoder_tav.o encoder_tad.o encoder_tav_opencv.o $(LIBS) $(OPENCV_LIBS)

tav_inspector: tav_inspector.c
	rm -f tav_inspector
	$(CC) $(CFLAGS) $(ZSTD_CFLAGS) -o tav_inspector $< $(LIBS)


# Build with debug symbols
debug: CFLAGS += -g -DDEBUG
debug: $(TARGETS)

# Clean build artifacts
clean:
	rm -f $(TARGETS) *.o

# Install (copy to PATH)
install: $(TARGETS) $(TAD_TARGETS)
	cp encoder_tav /usr/local/bin/
	cp decoder_tav /usr/local/bin/

# Check for required dependencies
check-deps:
	@echo "Checking dependencies..."
	@pkg-config --exists libzstd || (echo "Error: libzstd-dev not found. Install with: sudo apt install libzstd-dev" && exit 1)
	@pkg-config --exists opencv4 || (echo "Error: OpenCV 4 not found. Install with: sudo apt install libopencv-dev" && exit 1)
	@echo "All dependencies found."

# Help
help:
	@echo "TSVM Advanced Video (TAV) Encoders"
	@echo ""
	@echo "Targets:"
	@echo "  all          - Build video encoders (default)"
	@echo "  tav          - Build the TAV advanced video encoder"
	@echo "  debug        - Build with debug symbols"
	@echo "  clean        - Remove build artifacts"
	@echo "  install      - Install to /usr/local/bin"
	@echo "  check-deps   - Check for required dependencies"
	@echo "  help         - Show this help"
	@echo ""
	@echo "Usage:"
	@echo "  make               # Build video encoders"
	@echo "  make tav           # Build TAV encoder"
	@echo "  sudo make install  # Install all encoders"

.PHONY: all clean install check-deps help debug
