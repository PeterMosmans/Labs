# Generic Makefile for Docker images

# Copyright (C) 2018-2024 Peter Mosmans [Go Forward]
# SPDX-License-Identifier: GPL-3.0-or-later

# TAG	!= git tag|tail -1
TAG     = "latest"
NAME	!= basename $(dir $(abspath $(lastword $(MAKEFILE_LIST))))
DOCKER_IMG := gofwd/$(NAME):$(TAG)

# Enforce Bash as shell, as that makes it easier to script
SHELL := /bin/bash

# Some hard-coded variables
PORT    = 5000
COL_BLUE=\033[1;34m
COL_BOLD=\033[1m
COL_GREEN=\033[32m
COL_RED=\033[0;31m
COL_RESET=\033[0m
COL_YELLOW=\033[0;33m
# \033 beginning/ending of style
# 0 normal / 1 bold / 2 dim / 3 italic / 4 underlined / 5 blinking / 7 reverse / 8 invisible
# \m end of style sequence
# [0m reset all attributes

# Help first: This will be the default target
help: # Display useful commands
	@grep '^[^.#]\+:\s\+.*#' Makefile | \
	sed "s/\(.\+\):\s*\(.*\) #\s*\(.*\)/`echo -e "${COL_YELLOW}"`\1	`echo -e "${COL_RESET}"`\3/" | \
	expand -t25

# Recipes that aren't filenames: This ensures that they always will be executed
.PHONY: image run

image: # Build image
	@echo "Building $(DOCKER_IMG)..." && \
	DOCKER_BUILDKIT=1 docker build . -t $(DOCKER_IMG)

run: image # Run example
	@echo "Will be running $(DOCKER_IMG) on port $(PORT) - press Ctrl-C to stop" && \
	docker run --rm -it -p $(PORT):5000 $(DOCKER_IMG)
