# sdp-web-server
The web server for GrowBot Web.

## Contributing & Installing

[Go installation instructions]: https://golang.org/doc/install

1. Make sure Go is set up (you should have `GOROOT` and `GOPATH` set up appropriately).

    Don't have Go? Please follow the [Go installation instructions].

    You should also add `$GOPATH/bin` to your `$PATH`. This is so that any self-built binaries (such as [`goimports`](https://godoc.org/golang.org/x/tools/cmd/goimports)) are easy to run.

1. Already have Go set up? Make sure `go version` says you are running go 1.11 or later. We use [the new Go Modules system](https://blog.golang.org/modules2019), so if necessary, follow the [Go installation instructions] to upgrade.

    Curious about Go Modules? See the Go [Modules](https://github.com/golang/go/wiki/Modules) wiki page!

1. Make sure your editor is set up correctly. All files should be correctly formatted as per `go fmt`.

    We recommend you to use any of the following extensions with the obvious editors: [vscode-go](https://code.visualstudio.com/docs/languages/go), [GoSublime](https://packagecontrol.io/packages/GoSublime) or [vim-go](https://github.com/fatih/vim-go). These all run `gofmt` whenever you save a `.go` file. (You may find using `goimports` instead of `gofmt` more convenient.)

    If you choose to use Goland, please:
    - **do not** add any IDEA-related workspace folder to Git.
    - configure `gofmt` on save—it is not a default feature. Here are [instructions](https://stackoverflow.com/questions/33774950/execute-gofmt-on-file-save-in-intellij) to configure `gofmt` on file save. (This Stack Overflow link has not been tested, so you may find other instructions online that are easier to follow.)

1. Git commits must be [clean, atomic,](https://chris.beams.io/posts/git-commit/) and most importantly, follow the [seven sacred rules](https://chris.beams.io/posts/git-commit/#seven-rules).

    If you have a big commit that needs splitting up, you can use `git add --patch` (in short, `git add -p`) to interactively stage [hunks](https://www.bignerdranch.com/blog/using-git-hunks/).

    If you are stuck, please ask for assistance!

1. In your folder of choice run `git clone github.com/teamxiv/growbot-api`.

    If you clone the repository inside your `$GOPATH`, make sure the root of the repository is situated at exactly `$GOPATH/src/github.com/teamxiv/growbot-api`.

    If you clone the repository anywhere else on your filesystem, the folder does not matter.

1. Run `go get ./...` to download dependencies. ~~Once you have the repository cloned, type `go mod download` to download dependencies. Then type `go mod verify` to verify that all is OK.~~

    If you run into a `command not found` error, please jump to step 1 and re-read the [Go installation instructions].

1. Now, from any directory, you can run the following command: `go install github.com/teamxiv/growbot-api/cmd/growbot-api`.
    - This will build the command-line program (from [`./cmd/growbot-api`](/cmd/growbot-api/main.go)) to your `$GOPATH/bin` directory (in step 1 you should have added this path to your `$PATH`).
    - You can now simply type `growbot-api` from any directory to start the API.

## Set up database

**Install postgresql (on macOS)**
- Run `brew install postgresql`
- Read the caveats
- Run `brew services run postgresql` (`start` will run & also tell postgresql to start on boot, you probably don't want to start it on boot)

**Set up permissions**
- Run `psql postgres` to open a postgres shell
- Execute `create role growbot with login;` to create a `growbot` "role" that is able to log in (so it's basically a user). This user has no password for convenience.
- Run `make reset_schema` to create a database, give our `growbot` user admin permissions on `db growbot_dev`, and set the database schema
- **If you make updates to the database structure**: run `make schema.sql` to dump the _database schema_. It is **not** a full data dump with all rows.

## Live reload

1. Install [Gin](https://github.com/codegangsta/gin) (same name, different project).
2. Use the following command, run from the project root. Note that `9999` can be any random port as we don't want to use the reverse proxy.

    ```
    config=config.yml gin --notifications -i --path="." -d "cmd/growbot-api" --appPort 8080 --port 9999 --bin "cmd/growbot-api/growbot-api" run main.go
    ```

## Running

Command line documentation is available by supplying the `--help` argument. As of 737fa69e9799f8886103180ed318395b8a863c96 the following text is printed:

```
➜ growbot-api --help
Usage of growbot-api:
  -bindaddress
    	Change value of BindAddress. (default 0.0.0.0:8080)
  -loglevel
    	Change value of LogLevel. (default debug)

Generated environment variables:
   CONFIG_BINDADDRESS
   CONFIG_LOGLEVEL

flag: help requested
```

Refer to [./internal/config](/internal/config/config.go) to see what each of these options mean.

Please note that `0.0.0.0:8080` means "bind to all addresses, port 8080". You should try to connect to your loopback address instead of `0.0.0.0:8080`. This is usually `127.0.0.1` or `localhost`.

Instead of using environment variables or command-line arguments, you can use a YAML, JSON or TOML file.
- Just use the argument names as a data key. See `config.example.yml` as an example.
- To use a config file, e.g. `config.yml`, set the `config` environment variable, like so: `config=config.yml growbot-api`.

### Test websockets

You can use [wsc](https://github.com/danielstjules/wsc). Just do `yarn global add wsc` and then `wsc -er "ws://localhost:8080/stream/<uuid>` should work!

## Nomenclature

- `uuid`s are provided by [`github.com/google/uuid`](https://godoc.org/github.com/google/uuid).

    UUIDs will generally be used as the serial number of the robot. User accounts will most likely use an `int64` as their primary key.

## Software License

This software is governed by the license defined in [/LICENSE](/LICENSE) at the root of this repository.
