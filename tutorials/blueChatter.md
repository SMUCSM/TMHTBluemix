# Blue Chatter

This tutorial assumes you have `git` and the Cloud Foundry CLI `cf`. It will show an existing [example](https://github.com/IBM-Bluemix/bluechatter) and how to get it running quickly.

1. Setup a Redis service `cf create-service rediscloud 25mb redis-chatter`
2. Change to your prefered directory `cd ~/Develop`
3. Clone the repository `git clone https://github.com/CodenameBlueMix/bluechatter.git`
4. Change to that directory `cd ~/Develop/bluechatter`
5. Deploy to bluemix `cf push my-unique-chat-app-name` the app name must be unique accross JazzHub or it will fail, if so try again with a different name
6. View the application at `http://my-unique-chat-app-name.mybluemix.net`

## Bonus

You can scale your application right from the command line. The following command will make it run on 5 instances. While this can be done using the [Web Dashboard](https://console.ng.bluemix.net), CLI is more fun :smile:
```bash
cf scale my-unique-chat-app-name -i 5
```
