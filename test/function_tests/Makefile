# Copyright Contributors to the Open Cluster Management project

SUBSCRIPTION_NAME := "advanced-cluster-management"
PULL_SECRET_NAME := "multiclusterhub-operator-pull-secret"
CHANNEL := "release-2.7"
SOURCE_NAMESPACE := "open-cluster-management"
SOURCE_NAME := "acm-custom-registry"

## Run the installer downstream functional tests
ft-downstream-install:
	docker run --network host \
		--env pullSecret=$(PULL_SECRET_NAME) \
		--env source=$(SOURCE_NAME) \
		--env channel=$(CHANNEL) \
		--env sourceNamespace=$(SOURCE_NAMESPACE) \
		--env name=$(SUBSCRIPTION_NAME) \
		--env TEST_MODE="install" \
		--env full_test_suite="false" \
		--volume ~/.kube/config:/opt/.kube/config \
		$(REGISTRY)/$(IMG)-tests:$(VERSION)

## Run the uninstall functional tests
ft-downstream-uninstall:
	docker run --network host \
		--env pullSecret=$(PULL_SECRET_NAME) \
		--env source=$(SOURCE_NAME) \
		--env channel=$(CHANNEL) \
		--env sourceNamespace=$(SOURCE_NAMESPACE) \
		--env name=$(SUBSCRIPTION_NAME) \
		--env TEST_MODE="uninstall" \
		--env skipSubscription="true" \
		--volume ~/.kube/config:/opt/.kube/config \
		$(REGISTRY)/$(IMG)-tests:$(VERSION)

## Run the uninstall functional tests
ft-downstream-update:
	docker run --network host \
		--env pullSecret=$(PULL_SECRET_NAME) \
		--env source=$(SOURCE_NAME) \
		--env channel=$(CHANNEL) \
		--env sourceNamespace=$(SOURCE_NAMESPACE) \
		--env name=$(SUBSCRIPTION_NAME) \
		--env TEST_MODE="update" \
		--env startVersion="2.1.0" \
		--env updateVersion="2.7.0" \
		--volume ~/.kube/config:/opt/.kube/config \
		$(REGISTRY)/$(IMG)-tests:$(VERSION)

## Run the installer downstream functional tests
podman-ft-downstream-install:
	podman run --network host \
		--env pullSecret=$(PULL_SECRET_NAME) \
		--env source=$(SOURCE_NAME) \
		--env channel=$(CHANNEL) \
		--env sourceNamespace=$(SOURCE_NAMESPACE) \
		--env name=$(SUBSCRIPTION_NAME) \
		--env TEST_MODE="install" \
		--env full_test_suite="false" \
		--volume ~/.kube/config:/opt/.kube/config \
		$(REGISTRY)/$(IMG)-tests:$(VERSION)

## Run the uninstall functional tests
podman-ft-downstream-uninstall:
	podman run --network host \
		--env pullSecret=$(PULL_SECRET_NAME) \
		--env source=$(SOURCE_NAME) \
		--env channel=$(CHANNEL) \
		--env sourceNamespace=$(SOURCE_NAMESPACE) \
		--env name=$(SUBSCRIPTION_NAME) \
		--env TEST_MODE="uninstall" \
		--env skipSubscription="true" \
		--volume ~/.kube/config:/opt/.kube/config \
		$(REGISTRY)/$(IMG)-tests:$(VERSION)

## Run the uninstall functional tests
podman-ft-downstream-update:
	podman run --network host \
		--env pullSecret=$(PULL_SECRET_NAME) \
		--env source=$(SOURCE_NAME) \
		--env channel=$(CHANNEL) \
		--env sourceNamespace=$(SOURCE_NAMESPACE) \
		--env name=$(SUBSCRIPTION_NAME) \
		--env TEST_MODE="update" \
		--env startVersion="2.1.0" \
		--env updateVersion="2.7.0" \
		--volume ~/.kube/config:/opt/.kube/config \
		$(REGISTRY)/$(IMG)-tests:$(VERSION)

## Run the install functional tests directly
ft-install: 
	export skipSubscription="true";	ginkgo -tags functional -v --slow-spec-threshold=150s test/function_tests/multiclusterhub_install_test

## Run the uninstall functional tests directly
ft-uninstall:
	export skipSubscription="true"; ginkgo -tags functional -v --slow-spec-threshold=100s test/function_tests/multiclusterhub_uninstall_test

## Run the update functional tests directly
ft-update:
	export pullSecret=$(PULL_SECRET_NAME) \
		source=$(SOURCE_NAME) \
		channel="latest" \
		sourceNamespace=$(SOURCE_NAMESPACE) \
		name=$(SUBSCRIPTION_NAME) \
		TEST_MODE="update" \
		startVersion="2.1.0" \
		updateVersion="2.7.0";	ginkgo -tags functional -v --slow-spec-threshold=300s test/function_tests/multiclusterhub_update_test

## Build the MCH functional test image
test-image: deps
	go mod vendor
	@echo "Building $(REGISTRY)/multiclusterhub-operator-tests:$(VERSION)"
	docker build . -f build/Dockerfile.test.prow -t $(REGISTRY)/multiclusterhub-operator-tests:$(VERSION)
	rm -rf vendor

## Build the MCH functional test image
podman-test-image: deps
	go mod vendor
	@echo "Building $(REGISTRY)/multiclusterhub-operator-tests:$(VERSION)"
	podman build . -f build/Dockerfile.test.prow -t $(REGISTRY)/multiclusterhub-operator-tests:$(VERSION)
	rm -rf vendor

# Pass downstream bundle image snapshots for beginning version and the desired version to upgrade to 
# https://quay.io/repository/stolostron/acm-operator-bundle?tab=tags
# Usage: common/scripts/bundle-acm.sh "<Starting Snapshot>" <Update Snapshot> <Start Version> <Update Version>
## Creates an index image to test updates
test-update-image:
	bash common/scripts/bundle-acm.sh "2.1.0-SNAPSHOT-2020-08-21-16-48-23" "2.7.0-snapshot-here" "2.1.0" "2.7.0"

acm-index-install: ns secrets og test-update-image
	# Run `make  test-update-image` to generate a new index if necessary
	oc apply -k build/index-install/composite

