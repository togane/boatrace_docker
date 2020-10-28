# README

## Clone following repositories into machine running docker host.

* [togane/boatrace_fundamental_data_server](https://github.com/togane/boatrace_fundamental_data_server)
* [togane/boatrace_official_website_crawler](https://github.com/togane/boatrace_official_website_crawler)
* [togane/boatrace_official_website_proxy](https://github.com/togane/boatrace_official_website_proxy)
* [togane/teleboat_agent](https://github.com/togane/teleboat_agent)
* [togane/boatrace_forecaster](https://github.com/togane/boatrace_forecaster)

## Configure .env

Refer to .env.example.

## docker compose up

```
docker-compose up
```

## build db

execute following command after above steps have done.

```
docker-compose exec fundamental-data-server bin/rake db:create db:migrate db:seed
docker-compose exec forecaster bin/rake db:create db:migrate db:seed
```

If necessary, import sql to each databases instead of above migration commands.

## initilize queue

We use fake_sqs to emulate AWS SQS.
However fake_sqs clears all queue each time container is stopped.

Therefore need to execute below command to create queue each time to start container.

```
curl http://127.0.0.1:9494 -d "Action=CreateQueue&QueueName=UpdateRacerProfile&AWSAccessKeyId=access_key_id"
```

## start to handle queue

Also need to execute shoryuken as daemon in crawler repository which receive queues

```
docker-compose exec official-website-crawler bundle exec shoryuken --rails --config ./config/shoryuken.yml
```

## start worker

Some repositories depend to sidekiq and redis.
Redis will being started when container had been build.
However sidekiq does not start automatically by current settings.
Therefore need to execute below command.

```
docker-compose exec official-website-crawler bundle exec sidekiq -C ./config/sidekiq.yml
```


