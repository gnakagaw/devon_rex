# devon_rex

Docker images for Sider runners.

This repository's release tags indicate the Docker image tag, and all Docker images have the same tag.

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
