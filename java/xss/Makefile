# Generic Makefile for Docker images

# Copyright (C) 2018-2024 Peter Mosmans [Go Forward]
# SPDX-License-Identifier: GPL-3.0-or-later

# TAG	!= git tag|tail -1
TAG     = "latest"
DIRNAME != basename $(dir $(abspath $(lastword $(MAKEFILE_LIST)))) 2>/dev/null
NAME    := $(if $(DIRNAME),$(DIRNAME),test)
DOCKER_IMG := $(NAME):$(TAG)

# Local repository to speed up builds - absolute path
MAVEN_REPO = ~/maven-repo
MAVEN_TAG = 3.9.6-eclipse-temurin-21-alpine
NETWORK = labs
PORT    = 5000
TARGET = target/app.jar
WORKDIR = skf

# Enforce Bash as shell, as that makes it easier to script
SHELL := /bin/bash

SOURCES = $(wildcard **/*.java)

# Some hard-coded variables

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
.PHONY: configuration container image network run

$(MAVEN_REPO):
	@mkdir --parent $(MAVEN_REPO); \
	echo -e "Generated ${COL_BOLD}$(MAVEN_REPO)${COL_RESET} for first use..."

$(TARGET): pom.xml $(SOURCES) $(MAVEN_REPO)
	docker run --rm -it \
	-u $$UID \
	-v $(MAVEN_REPO):/srv/maven-repo \
	-v $$PWD:/$(WORKDIR) \
	--workdir /$(WORKDIR) \
	maven:$(MAVEN_TAG) \
	mvn -Dmaven.repo.local=/srv/maven-repo -Dmaven.test.skip=true package

configuration: # Display current configuration
	@echo -e "Container name:                            ${COL_BOLD}$(NAME)${COL_RESET}" && \
	echo -e "Docker image name:                         ${COL_BOLD}$(DOCKER_IMG)${COL_RESET}" && \
	echo -e "Maven image used for building and running: ${COL_BOLD}maven:$(MAVEN_TAG)${COL_RESET}" && \
	echo -e "Local Maven cache:                         ${COL_BOLD}$(MAVEN_REPO)${COL_RESET}" && \
	echo -e "Docker network name:                       ${COL_BOLD}$(NETWORK)${COL_RESET}" && \
	echo -e "Listening port number:                     ${COL_BOLD}$(PORT)${COL_RESET}" && \
	echo -e "Target application:                        ${COL_BOLD}$(TARGET)${COL_RESET}" && \
	echo -e "Workdir:                                   ${COL_BOLD}$(WORKDIR)${COL_RESET}" && \
	echo -e "\nAny of these values can be changed in the Makefile itself"

build: target/app.jar # Build application using a Docker container

image: # Build Docker image
	@echo "Building $(DOCKER_IMG)..." && \
	DOCKER_BUILDKIT=1 docker build . -t $(DOCKER_IMG)

run: $(TARGET) # Run app locally using a Java container
	@echo "Will be running maven:$(MAVEN_TAG) on port $(PORT) - press Ctrl-C to stop" && \
	docker run --rm -it \
	-u $$UID \
	-p $(PORT):5000 \
	-v $$PWD:/$(WORKDIR) \
	--workdir /$(WORKDIR) \
	maven:$(MAVEN_TAG) \
	java -jar /$(WORKDIR)/$(TARGET)

network: $(TARGET) # Run app locally using a Java container in a specific network
	@echo "Will be running as $(NAME) on port $(PORT) in network $(NETWORK) - press Ctrl-C to stop" && \
	docker run --rm -it \
	--name $(NAME) \
	--network $(NETWORK) \
	-u $$UID \
	-p $(PORT):5000 \
	-v $$PWD:/$(WORKDIR) \
	--workdir /$(WORKDIR) \
	maven:$(MAVEN_TAG) \
	java -jar /$(WORKDIR)/$(TARGET)

container: image # Run example using its own Docker container
	@echo "Will be running $(DOCKER_IMG) on port $(PORT) - press Ctrl-C to stop" && \
	docker run --rm -it -p $(PORT):5000 $(DOCKER_IMG)

clean: # Clean app
	@rm -rf $(TARGET)

forceclean:
	docker run --rm -it \
	-u $$UID \
	-v $(MAVEN_REPO):/srv/maven-repo \
	-v $$PWD:/$(WORKDIR) \
	--workdir /$(WORKDIR) \
	maven:$(MAVEN_TAG) \
	mvn -Dmaven.repo.local=/srv/maven-repo clean
