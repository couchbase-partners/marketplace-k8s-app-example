FROM launcher.gcr.io/google/debian9 AS build

RUN apt-get update \
    && apt-get install -y --no-install-recommends gettext

ADD schema.yaml /tmp/schema.yaml

# Provide registry prefix and tag for default values for images.
ARG REGISTRY
ARG TAG
RUN cat /tmp/schema.yaml \
    | env -i "REGISTRY=$REGISTRY" "TAG=$TAG" envsubst \
    > /tmp/schema.yaml.new \
    && mv /tmp/schema.yaml.new /tmp/schema.yaml

ADD apptest/deployer/schema.yaml /tmp/data-test/schema.yaml
RUN cat /tmp/data-test/schema.yaml \
    | env -i "REGISTRY=$REGISTRY" "TAG=$TAG" envsubst \
    > /tmp/data-test/schema.yaml.new \
    && mv /tmp/data-test/schema.yaml.new /tmp/data-test/schema.yaml

FROM gcr.io/cloud-marketplace-tools/k8s/deployer_envsubst

COPY manifest /data/manifest
COPY --from=build /tmp/schema.yaml /data/
COPY --from=build /tmp/data-test/schema.yaml /data-test/
