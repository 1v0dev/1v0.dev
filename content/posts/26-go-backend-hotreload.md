---
title: "Hot reload during development for Go and Svelte with single command"
date: 2025-10-23T10:15:00+03:00
tags:
    - Go
    - Svelte
keywords:
    - Go
    - Svelte
    - Hot-reload
    - Make
    - Concurrently
---

I started developing and app that has Go backend and SvelteKit frontend. So to ease development I wanted to be able 
to run both the backend and the frontend with a single command while both having hot reload to avoid manually restarting
the services.

<!--more-->

# App structure
My app is a monorepo containing all services. Go backend is in apps/backend and the SvelteKit frontend is in apps/frontend.
The structure looks like this:
```
root
    -- apps
        -- backend
        -- fronted
    -- db
        -- migrations
    compose.infra.yaml
    Makefile
```

# Make

Recently I started to use Make (https://www.gnu.org/software/make). It's often recommended when developing with Go and
like it. It's really flexible and while it is designed mainly for building apps it actually allows you to define any 
complex set of commands under easy to use alias. So it's perfect for our goal.

Let's start by creating a make goal called `dev` in our makefile. My app needs a database and I created a docker compose file to
run it. That will be our first step:

```makefile
dev:
    docker compose -f compose.infra.yaml up -d
```

# Concurrently

We need a way to run both the frontend and the backend as long-running processes in a single terminal window from a single
command. The tool for the job is Concurrently (https://github.com/open-cli-tools/concurrently) which allows you to pass
a list of commands and each will be run on the same terminal and the logs will also be shown prefixed with a name so you can
differentiate. It's build on Node.js so we need npm to install it `npm i -g concurrently`
It's a great tool with a lot of options, you can read more about it in the official documentation.
So let's add the concurrently command to the make goal. We can use the alias `conc` for convenience:

```makefile
dev:
    docker compose -f compose.infra.yaml up -d
    conc --names "backend,frontend" \
	  --prefix "[{name}]" \
	  "go -C apps/backend run ./cmd/api -db-dsn postgres://localhost:5432" \
	  "npm --prefix apps/frontend run dev"
```

Since we want to execute that from the root directory we need to instruct both Go and npm which dir to use as working one.

So far, so good. We can run the above command, and it will start both the frontend and the backend in a single terminal.
Pressing Ctrl+C will exit both processes.
I did not configure it to stop the docker compose stack with the database automatically because I don't want to 
restart it each time. But by removing the `-d` parameter and moving the command inside concurrently you can achieve that easily.

But while `npm run dev` uses hot reload by default (when using SvelteKit), the Go backend needs to be restarted. So we
need one more tool - Air

# Air

Air (https://github.com/air-verse/air) is a hot reload tool for Go. It works well and allows extensive configuration.

First you have to install it with `go install github.com/air-verse/air@latest`. Then we need to create the `.air.toml`
configuration file:
```shell
cd apps/backend
air init
```

The main challenge for me was to configure Air to be able to run from the root folder, as it is designed to be run from the 
base of the go module, so it needed a little trial and error. To work I had to adjust the `root` dir and the paths in `cmd` and `bin`  
  
```toml
# .air.toml
root = "./apps/backend"
bin = "apps\\backend\\tmp\\main.exe"
cmd = "go -C apps/backend build -o ./tmp/main.exe ./cmd/api"
```

then we can finish our make goal:
```makefile
dev:
    docker compose -f compose.infra.yaml up -d
    conc --names "backend,frontend" \
	  --prefix "[{name}]" \
	  "air -c ./apps/backend/.air.toml -- -db-dsn postgres://localhost:5432" \
	  "npm --prefix apps/frontend run dev"
```

Take note of the double dash `--` before our parameter `-db-dsn`. It's needed to instruct Air to pass that to the binary
and not use it as its own parameter.

# Running

Now you can just do `make dev` in the root and everything will start. Here is the output on my computer:
```shell
> make dev
docker compose -f compose.infra.yaml up -d
[+] Running 2/2
 ✔ Container db     Running                                                                                                     0.0s 
 ✔ Container pgweb  Running                                                                                                     0.0s 
conc --names "backend,frontend" \
  --prefix "[{name}]" \
  "air -c ./apps/backend/.air.toml -- -db-dsn postgres://localhost:5432" \
  "npm --prefix apps/frontend run dev"
[backend] 
[backend]   __    _   ___
[backend]  / /\  | | | |_)
[backend] /_/--\ |_| |_| \_ v1.63.0, built with Go go1.25.3
[backend]
[backend] watching .
[backend] watching cmd
[backend] watching cmd\api
[backend] watching internal
[backend] watching internal\data
[backend] !exclude tmp
[backend] building...
[frontend] 
[frontend] > frontend@0.0.1 dev
[frontend] > vite dev
[frontend]
[frontend] 
[frontend]   VITE v7.1.11  ready in 2457 ms
[frontend]
[frontend]   ➜  Local:   http://localhost:5173/
[frontend]   ➜  Network: use --host to expose
[backend] running...
[backend] 2025/10/23 10:26:10 starting development server on :8081
```