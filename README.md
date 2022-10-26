# heroku-buildpacks-bug

Demonstrate that the heroku/nodejs buildpack is clobbering the `WEB_CONCURRENCY` environment variable, even when it's not the main buildpack.

## To run the examples below

* Install [docker](https://www.docker.com/)
* Install [pack](https://buildpacks.io/docs/tools/pack/)
* Clone this repo

## Just using the node buildpack

#### Build image
```bash
pack build heroku-buildpacks-bug --builder heroku/buildpacks:20 -b heroku/nodejs
```

#### When WEB_CONCURRENCY is not set, it returns a value
```bash
docker run --rm --entrypoint launcher heroku-buildpacks-bug sh -c 'echo $WEB_CONCURRENCY'
#=> 1
```

#### When WEB_CONCURRENCY is set, it returns wrong value
```bash
docker run --rm --entrypoint launcher -e WEB_CONCURRENCY=2 heroku-buildpacks-bug sh -c 'echo $WEB_CONCURRENCY'
#=> 1
```

## Using node buildpack first and ruby buildpack last

#### Build image
```bash
pack build heroku-buildpacks-bug --builder heroku/buildpacks:20 -b heroku/nodejs -b heroku/ruby
```

#### When WEB_CONCURRENCY is not set, it returns a value
```bash
docker run --rm --entrypoint launcher heroku-buildpacks-bug sh -c 'echo $WEB_CONCURRENCY'
#=> 1
```

#### When WEB_CONCURRENCY is set, it returns wrong value
```bash
docker run --rm --entrypoint launcher -e WEB_CONCURRENCY=2 heroku-buildpacks-bug sh -c 'echo $WEB_CONCURRENCY'
#=> 1
```

## Just using the ruby buildpack

#### Build image
```bash
pack build heroku-buildpacks-bug --builder heroku/buildpacks:20 -b heroku/ruby
```

#### When WEB_CONCURRENCY is not set, it does not return a value
```bash
docker run --rm --entrypoint launcher heroku-buildpacks-bug sh -c 'echo $WEB_CONCURRENCY'
#=>
```

#### When WEB_CONCURRENCY is set, it returns right value
```bash
docker run --rm --entrypoint launcher -e WEB_CONCURRENCY=2 heroku-buildpacks-bug sh -c 'echo $WEB_CONCURRENCY'
#=> 2
```

## Using the paketo buildpacks

#### Build image
```bash
pack build heroku-buildpacks-bug --builder paketobuildpacks/builder:full -b paketo-buildpacks/nodejs -b paketo-buildpacks/ruby
```

#### When WEB_CONCURRENCY is not set, it does not return a value
```bash
docker run --rm --entrypoint launcher heroku-buildpacks-bug sh -c 'echo $WEB_CONCURRENCY'
#=>
```

#### When WEB_CONCURRENCY is set, it returns right value
```bash
docker run --rm --entrypoint launcher -e WEB_CONCURRENCY=2 heroku-buildpacks-bug sh -c 'echo $WEB_CONCURRENCY'
#=> 2
```
