[![Build Status](https://travis-ci.com/sider/devon_rex.svg?branch=master)](https://travis-ci.com/sider/devon_rex)

# devon_rex

Docker images for Sider runners.

This repository's release tags indicate the Docker image tag, and all Docker images have the same tag.

* [devon_rex_base](https://hub.docker.com/r/sider/devon_rex_base)
* [devon_rex_ruby](https://hub.docker.com/r/sider/devon_rex_ruby)
* [devon_rex_npm](https://hub.docker.com/r/sider/devon_rex_npm)
* [devon_rex_php](https://hub.docker.com/r/sider/devon_rex_php)
* [devon_rex_python](https://hub.docker.com/r/sider/devon_rex_python)
* [devon_rex_java](https://hub.docker.com/r/sider/devon_rex_java)
* [devon_rex_swift](https://hub.docker.com/r/sider/devon_rex_swift)
* [devon_rex_go](https://hub.docker.com/r/sider/devon_rex_go)

## Development

This project depends on Ruby, so you have to install it and run `bundle install`.

Every Docker image is written in `Dockerfile.erb` file.
If you want to change a Docker image, update `Dockerfile.erb` file,
and run `bundle exec rake dockerfile:generate` task.
Don't forget to include the changes of `Dockerfile` ☺️

By the way, why do we use ERB?
Because we want to reuse the same Dockerfile instructions like `base/Dockerfile.prepare.erb`.

## Licensing

The devon_rex repository is licensed under The MIT License. See [LICENSE](LICENSE) for the full license text.
