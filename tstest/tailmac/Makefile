XCPRETTIFIER := xcpretty
ifeq (, $(shell which $(XCPRETTIFIER)))
	XCPRETTIFIER := cat
endif

.PHONY: tailmac
tailmac:
	xcodebuild -scheme tailmac -arch arm64 -derivedDataPath build -configuration Release build | $(XCPRETTIFIER)
	cp -r ./build/Build/Products/Release/tailmac ./bin/tailmac

.PHONY: host
host:
	xcodebuild -scheme host -arch arm64 -derivedDataPath build -configuration Release build | $(XCPRETTIFIER)
	cp -r ./build/Build/Products/Release/TailMac.app ./bin/TailMac.app

.PHONY: clean
clean:
	rm -rf ./bin
	rm -rf ./build
	mkdir -p ./bin

.PHONY: all
all: clean tailmac host
