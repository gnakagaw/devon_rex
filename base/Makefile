build:
	docker build -t quay.io/actcat/devon_rex_base:dev .

test:
	docker run -it --rm quay.io/actcat/devon_rex_base:dev bash -c "ruby -v && gem -v && bundle -v"
