- [PM2 Installation](#pm2-installation)
- [PM2 common commands](#pm2-common-commands)
- [Start an app](#start-an-app)
  - [Cluster mode](#cluster-mode)
  - [Startup script](#startup-script)
- [Ecosystem File](#ecosystem-file)
- [Updating PM2](#updating-pm2)
- [CheatSheet](#cheatsheet)
- [PM2 with Nextjs](#pm2-with-nextjs)


# PM2 Installation
> Node install is prerequisite.

```sh
npm i pm2 -g
```

# PM2 common commands
```sh
# Manage process
pm2 start app_name
pm2 restart app_name
pm2 reload app_name
pm2 stop app_name
pm2 delete app_name

# Instead of app_name you can pass:
#- all
#- id

# List Manage applications
pm2 [list|ls|status]

# Display Log in Real Time
pm2 logs

# Real time monitor dashboard for process
pm2 monit
```

# Start an app
```shell
pm2 start bashscript.sh
pm2 start python-app.py --watch
pm2 start binary-file -- --port 1520
```
Some options you can pass to the CLI:
```sh
# Specify an app name
--name <app_name>

# Watch and Restart app when files change
--watch

# Set memory threshold for app reload
--max-memory-restart <200MB>

# Specify log file
--log <log_path>

# Pass extra arguments to the script
-- arg1 arg2 arg3

# Delay between automatic restarts
--restart-delay <delay in ms>

# Prefix logs with time
--time

# Do not auto restart app
--no-autorestart

# Specify cron for forced restart
--cron <cron_pattern>

# Attach to application log
--no-daemon
```

## Cluster mode
PM2 includes an automatic load balancer that will share all HTTP[s]/Websocket/TCP/UDP connections between each spawned processes.
To start an application in Cluster mode:
```
pm2 start app.js -i max
```

## Startup script
Restarting PM2 with the processes you manage on server boot/reboot is critical. To solve this, just run this command to generate an active startup script:
```sh
pm2 startup
```

# Ecosystem File
You can also create a configuration file, called `Ecosystem` File, to manage multiple applications which is similar to `docker-compose`. To generate an Ecosystem file:
```sh
# This command will generate ecosystem.config.js 
pm2 ecosystem
```
Example **ecosystem.config.js**:
```javascript
const DB_CONNECTION = "User ID=postgres;Password=SophicPost;Host=127.0.0.1;Port=5432;Database=iot_framework1;";
const KRATOS_DB = "postgres://postgres:SophicPost@127.0.0.1:5432/kratosdb?sslmode=disable&max_conns=20&max_idle_conns=4"
const HOST_NAME = "localhost"
const BASE_URL = `http://${HOST_NAME}:8080`

module.exports = {
  apps : [
    {
      name   : "jetstream",
      cwd    : "./nats",
      script : "./nats-server.exe",
      args   : "-c ../cfg/nats.json",
      intepreter: null,
    },
    {
      name   : "kratos",
      cwd    : "./kratos",
      script : "./kratos.exe",
      args   : "serve -c ../cfg/kratos.yml --watch-courier --sqa-opt-out",
      intepreter: null,
      env: {
        DSN: KRATOS_DB,
        SERVE_PUBLIC_CORS_ALLOWED_ORIGINS_0: BASE_URL,
        SELFSERVICE_DEFAULT_BROWSER_RETURN_URL: BASE_URL,
        SELFSERVICE_ALLOWED_RETURN_URLS_0: BASE_URL,
      },
    },
    {
      name   : "smtp4dev",
      cwd    : "./smtp4dev-server",
      script : "Rnwood.Smtp4dev.exe",
      args   : "--urls \"http://0.0.0.0:6001/\"",
      intepreter: null,
    },
    {
      name   : "nerveweb",
      cwd    : "./web",
      script : "./server/index.mjs",
      env: {
        NUXT_PUBLIC_KRATOS_URL: `${BASE_URL}/authapi/`,
        NUXT_PUBLIC_GRAPHQL_URL: `${BASE_URL}/api/graphql`,
        NUXT_PUBLIC_KRATOS_ADMIN_URL: `${BASE_URL}:4435/`,
      },
    },
    {
      name   : "kratosadmincors",
      cwd    : "./web",
      script : "./cors-proxy.js",
    },
    {
      name   : "chiffon",
      cwd    : "./chiffon",
      script : "Chiffon.exe",
      intepreter: null,
      restart_delay: 1500,
      env: {
        NERVE_API__ConnectionStrings__Default: DB_CONNECTION
      },
    },
    {
      name   : "computes",
      cwd    : "./computes",
      script : "computes.exe",
      intepreter: null,
      restart_delay: 3000,
      env: {
        NERVE_COMPUTES__ConnectionStrings__Default: DB_CONNECTION
      },
    },
    {
      name   : "NerveDBIntegration",
      cwd    : "./NerveDBIntegration",
      script : "NerveDBIntegration.exe",
      intepreter: null,
      restart_delay: 3000,
      env: {
        NERVE_DBIntegration__ConnectionStrings__Default: DB_CONNECTION
      },
    },
    {
      name   : "eventframe_handler",
      cwd    : "./eventframe_handler",
      script : "eventframe_handler.exe",
      intepreter: null,
      restart_delay: 1000,
      env: {
        NERVE_EVENTS__ConnectionStrings__Default: DB_CONNECTION
      },
    },
    {
      name   : "notifications",
      cwd    : "./notifications",
      script : "notifications.exe",
      intepreter: null,
      restart_delay: 1000,
      env: {
        NERVE_NOTIF__ConnectionStrings__Default: DB_CONNECTION
      },
    },
    {
      name   : "traefik",
      cwd    : "./traefik",
      script : "traefik.exe",
      env: {
        HOST_NAME: HOST_NAME,
      },
    },
  ]
}
```
And start it easily:
```sh
pm2 start ecosystem.config.js
```

# Updating PM2
```sh
npm install pm2@latest -g
# or
pm2 update
```

# CheatSheet
Here are some commands that are worth knowing. Just try them with a sample application or with your current web application on your development machine:
```bash
# Fork mode
pm2 start app.js --name my-api # Name process

# Cluster mode
pm2 start app.js -i 0        # Will start maximum processes with LB depending on available CPUs
pm2 start app.js -i max      # Same as above, but deprecated.
pm2 scale app +3             # Scales `app` up by 3 workers
pm2 scale app 2              # Scales `app` up or down to 2 workers total

# Listing

pm2 list               # Display all processes status
pm2 jlist              # Print process list in raw JSON
pm2 prettylist         # Print process list in beautified JSON

pm2 describe 0         # Display all information about a specific process

pm2 monit              # Monitor all processes

# Logs

pm2 logs [--raw]       # Display all processes logs in streaming
pm2 flush              # Empty all log files
pm2 reloadLogs         # Reload all logs

# Actions

pm2 stop all           # Stop all processes
pm2 restart all        # Restart all processes

pm2 reload all         # Will 0s downtime reload (for NETWORKED apps)

pm2 stop 0             # Stop specific process id
pm2 restart 0          # Restart specific process id

pm2 delete 0           # Will remove process from pm2 list
pm2 delete all         # Will remove all processes from pm2 list

# Misc

pm2 reset <process>    # Reset meta data (restarted time...)
pm2 updatePM2          # Update in memory pm2
pm2 ping               # Ensure pm2 daemon has been launched
pm2 sendSignal SIGUSR2 my-app # Send system signal to script
pm2 start app.js --no-daemon
pm2 start app.js --no-vizion
pm2 start app.js --no-autorestart
```

# PM2 with Nextjs

Start a new ecosystem with `pm2 start ecosystem.config.js`.This is the main PM2 config used for Next.js deployment script in their documentation website.
```js
module.exports = {
  apps: [
    {
      name: 'NextAppName',
      exec_mode: 'cluster',
      instances: 'max',
      script: 'node_modules/next/dist/bin/next',
      args: 'start',
      env_local: {
        NODE_ENV: 'local',
        NEXTAUTH_SECRET: 'nuMEdbtcieujVcNXvBGCDw==',
        NEXTAUTH_URL: 'http://localhost:3000'
      },
      env_dev: {
        NODE_ENV: 'dev',
        NEXTAUTH_SECRET: 'z1kYCuLD74EiD8OTBhWAHQ==',
        NEXTAUTH_URL: 'https://xx....com',
      },
      env_prod: {
        NODE_ENV: 'prod',
        NEXTAUTH_SECRET: 'z1kYCuLD74EiD8OTBhWAHQ==',
        NEXTAUTH_URL: 'https://xx....com',
      }
    }
  ]
}
```
or start with npm or any runtime like bun(need install first)
```js
module.exports = {
  apps : [{
    name: "next-app",
    script: "npm",
    args: "start",
    cwd: "/path/to/your/nextjs/app",
    watch: true,
    env_local: {
        NODE_ENV: 'local',
        NEXTAUTH_SECRET: 'nuMEdbtcieujVcNXvBGCDw==',
        NEXTAUTH_URL: 'http://localhost:3000'
      },
      env_dev: {
        NODE_ENV: 'dev',
        NEXTAUTH_SECRET: 'z1kYCuLD74EiD8OTBhWAHQ==',
        NEXTAUTH_URL: 'https://xx....com',
      },
      env_prod: {
        NODE_ENV: 'prod',
        NEXTAUTH_SECRET: 'z1kYCuLD74EiD8OTBhWAHQ==',
        NEXTAUTH_URL: 'https://xx....com',
      }
  }]
};
```
Running the PM2 ecosystem config file for different environments `pm2 startOrRestart ecosystem.config.js --env local`, $ `pm2 startOrRestart ecosystem.config.js --env dev` or $ `pm2 startOrRestart ecosystem.config.js --env prod`.
<br/>

For more simplyfy command, you can add this into your `package.json` scripts.
```json
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "deploy:local": "next build && pm2 startOrRestart ecosystem.config.js --env local"
    "deploy:dev": "next build && pm2 startOrRestart ecosystem.config.js --env dev"
    "deploy:prod": "next build && pm2 startOrRestart ecosystem.config.js --env prod"
  },
```

With your ecosystem.config.js file ready, you can now start your applications using PM2:
```bash
pm2 start ecosystem.config.js
```

