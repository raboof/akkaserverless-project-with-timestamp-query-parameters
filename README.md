# Akkaserverless project with timestamp query parameters

## Designing

While designing your service it is useful to read [designing services](https://developer.lightbend.com/docs/akka-serverless/services/development-process.html).

## Developing

This project has a bare-bones skeleton service ready to go, but in order to adapt and
extend it it may be useful to read up on [developing services](https://developer.lightbend.com/docs/akka-serverless/developing/index.html)
and in particular the [JVM section](https://developer.lightbend.com/docs/akka-serverless/java-services/index.html).

## Building

You can use [sbt](https://www.scala-sbt.org/) to build your project,
which will also take care of generating code based on the `.proto` definitions:

```
sbt compile
```

## Running Locally

In order to run your application locally, you must run the Akka Serverless proxy. The included `docker-compose.yml` file contains the configuration required to run the proxy for a locally running application.
It also contains the configuration to start a local Google Pub/Sub emulator that the Akka Serverless proxy will connect to.
To start the proxy, run the following command from this directory:

```
docker-compose up
```

> On Linux this requires Docker 20.10 or later (https://github.com/moby/moby/pull/40007),
> or for a `USER_FUNCTION_HOST` environment variable to be set manually.

To start the application locally, start it from your IDE or use:

```
sbt run
```

With both the proxy and your application running, any defined endpoints should be available at `http://localhost:9000`.

You can ask whether Dolly Parton is working at a particular timestamp:

```shell
> curl localhost:9000/is-dolly-working?at=2022-01-27T16:00:00Z
{"works":true,"from":"2022-01-27T09:00:00Z","remaining":"3600s"}
```

And whether she still has a given duration on her shift:

```shell
> curl localhost:9000/is-dolly-working?at=2022-01-27T16:00:00Z\&for=3600s
{"works":true,"from":"2022-01-27T09:00:00Z","remaining":"3600s"}
? curl localhost:9000/is-dolly-working?at=2022-01-27T16:00:00Z\&for=3601s
{"works":false}
```

## Deploying

To deploy your service, install the `akkasls` CLI as documented in
[Setting up a local development environment](https://developer.lightbend.com/docs/akka-serverless/setting-up/)
and configure a Docker Registry to upload your Docker image to.

You will need to set your `docker.username` as a system property:

```
sbt -Ddocker.username=mary docker:publish
```

Refer to [Configuring registries](https://developer.lightbend.com/docs/akka-serverless/projects/container-registries.html)
for more information on how to make your Docker image available to Akka Serverless.

Finally you can or use the [Akka Serverless Console](https://console.akkaserverless.com)
to create a project and then deploy your service into the project
through the `akkasls` CLI.
