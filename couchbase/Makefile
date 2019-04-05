include app.Makefile
include common.Makefile
include gcloud.Makefile
include var.Makefile

APP_DEPLOYER_IMAGE ?= $(CB_DEPLOYER_IMAGE)
TESTER_IMAGE ?= $(REGISTRY)/tester:$(TAG)

#NAME ?= cb-operator-1
APP_PARAMETERS ?= { \
  "name": "$(NAME)", \
  "namespace": "$(NAMESPACE)", \
  "couchbaseOperatorImage": "$(CB_OP_IMAGE)", \
  "dbUsername": "$(DB_USERNAME)", \
  "dbPassword": "$(DB_PASSWORD)", \
  "initialDelaySeconds": "$(INIT_DELAY_SEC)", \
  "periodSeconds": "$(PERIOD_SEC)", \
  "failureThreshold": "$(FAIL_THRESHOLD)", \
  "autoFailoverTimeout": "$(AUTO_FAIL_TIMEOUT)", \
  "bucketMemoryQuota": "$(BUCKET_MEM_QUOTA)", \
  "operatorReplicas": "$(OP_REPLICAS)", \
  "clusterReplicas": "$(CLUSTER_REPLICAS)", \
  "containerPort": "$(CONTAINER_PORT)", \
  "servers": "$(SERVERS)", \
  "dataServiceMemoryQuota": "$(DATA_SVC_QUOTA)", \
  "indexServiceMemoryQuota": "$(IDX_SVC_QUOTA)", \
  "searchServiceMemoryQuota": "$(SRC_SVC_QUOTA)" \
}

# Extend the target as defined in app.Makefile to
# include real dependencies.
app/build:: .build/couchbase/deployer \
            .build/couchbase/tester


.build/couchbase: | .build
	mkdir -p "$@"


.build/couchbase/deployer: apptest/**/* \
                           deployer/* \
                           manifest/* \
                           schema.yaml \
                           .build/var/APP_DEPLOYER_IMAGE \
                           .build/var/REGISTRY \
                           .build/var/TAG \
                           | .build/couchbase
	$(call print_target, $@)
	docker build \
	    --build-arg REGISTRY="$(REGISTRY)" \
	    --build-arg TAG="$(TAG)" \
	    --tag "$(APP_DEPLOYER_IMAGE)" \
	    -f deployer/Dockerfile \
	    .
	docker push "$(APP_DEPLOYER_IMAGE)"
	@touch "$@"


.build/couchbase/tester: apptest/tester/*
	$(call print_target, $@)
	cd apptest/tester \
	    && docker build --tag "$(TESTER_IMAGE)" .
	docker push "$(TESTER_IMAGE)"
	@touch "$@"