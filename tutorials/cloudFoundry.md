# Cloud Foundry CLI tutorial

After you set up an account on [IBM Bluemix](https://console.ng.bluemix.net/) this is the first tutorial you should follow. While you aren't forced to use the `cf` CLI it will definitely speed up your workflow and the amount of tutorials you can follow.

1. [Download](https://github.com/cloudfoundry/cli/releases) the CLI for your system
2. Setup cf to default to bluemix with this command: `cf api https://api.ng.bluemix.net`
3. Login to bluemix, your email is the one you registered with above. `dev` is the default space
```bash
cf login -u yourEmail@domain.com -o yourEmail@domain.com -s dev
```

You are now setup to use the Cloud Foundry CLI! Why not check out some of its [documentation?](https://www.ng.bluemix.net/docs/#cli/cli.html#cfcommands)
