# Elixir/Elm Docker Image

Simple Docker image for Phoenix/Elm applications, built with GitLab CI in mind.
This image is publicly available on [Docker Hub](https://hub.docker.com/r/psguazz/elixir-elm/) and [GitHub](https://github.com/psguazz/elixir-elm).

The point is to be able to test and deploy Phoenix/Elm applications. As such, this image includes:
* Elixir
* Elm, along with `elm-test`
* Rubygems, along with the `dpl` gem
* Heroku CLI

Beyond these things, the image contains random utilities that have come in handy during testing -- the idea being that presumably having it installed them here from the get-go is faster than installing everything again every time tests are run.

Ruby and the `dpl` gem are used to deploy applications, as per [GitLab's docs](https://docs.gitlab.com/ce/ci/examples/deployment/README.html).

Here's a sample `.gitlat-ci.yml` file, again based off [GitLab's docs](https://docs.gitlab.com/ee/ci/examples/test-phoenix-application.html#add-gitlab-ci-yml-file-to-project) and extended with the Elm commands:

````yaml
image: psguazz/elixir-elm

services:
  - postgres:9.6

variables:
  MIX_ENV: "test"
  POSTGRES_DB: "<db>"
  POSTGRES_USER: "<user>"
  POSTGRES_PASSWORD: "<password>"

test:
  script:
    - mix local.hex --force
    - mix deps.get --only test
    - mix ecto.reset
    - mix test
    - cd <elm-test directory>
    - elm-test

staging:
  type: deploy
  script:
    - dpl --provider=heroku --app=<app name> --api-key=$HEROKU_STAGING_API_KEY
  only:
    - master
````
