
.PHONY: all
all: runtime

.PHONY: clean
clean:
	docker rmi -f robnightingale/zookeeper:${TAG} || :

.PHONY: runtime
runtime:
	docker build \
		--build-arg BUILD_DATE=${BUILD_DATE} \
		--build-arg VCS_REF=${VCS_REF} \
		--build-arg VERSION=${VERSION} \
		-t robnightingale/zookeeper:${TAG} .
	docker images | grep zookeeper

.PHONY: test
test:
	(docker network ls | grep vnettest ) || docker network create vnettest
	(docker ps --format {{.Names}} | grep zookeeper-1 ) || docker run --name zookeeper-1 --hostname zookeeper-1.vnettest --net vnettest -d robnightingale/zookeeper:${VERSION}-alpine -server 1 1 vnettest 

	bats ../test/test_*.bats