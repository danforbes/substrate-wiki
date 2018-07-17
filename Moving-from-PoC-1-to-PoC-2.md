When the change over happens, you'll probably need to do something.

## If you're running a `v0.1` build

- It will stop at the block that the runtime gets upgraded. You'll need to sync a new `v0.2` node from scratch, or find a kind soul with a v0.2 database zipped up and unzip it to the right place.

## If you're running a `v0.2-devp2p` or recent `master` build

- If the build is *really* recent (after `25d9afb` commit), then it will stop at the block that the runtime gets upgraded. You'll need to upgrade your node to a `v0.2` branch, but your database is compatible: just follow the database upgrade instructions below.
- If the build is older, then it'll stop at the point that the runtime-upgrade transaction is introduced due to a bug that was fixed in `25d9afb` commit. You have a choice: either run the node with `--heap-pages=1024` (or `--min-heap-pages=1024 --max-heap-pages 1024`) as a CLI param or alternatively upgrade to `v0.2` branch and run that instead. (Note: older versions of `v0.2-devp2p` don't have those CLI options and you'll just need to upgrade the node.)

# Upgrading the Database

There's a manual step in moving from an older build to a new `master` or `v0.2` branch build because the database is stored in a new place. Essentially, you'll need to move the contents of whatever place your node uses to store the database &c. into a subpath `chains/poc-1-testnet`.

If you're using the defaults on Mac OS, then just run:

```sh
mkdir -p /tmp/chains/poc-1-testnet && \
mv ~/Library/Application\ Support/Polkadot/* /tmp/chains/poc-1-testnet && \
mv /tmp/chains ~/Library/Application\ Support/Polkadot/
```

If you have been using the `-d MY_PATH` CLI parameter before then you'll want to run:

```sh
mkdir -p /tmp/chains/poc-1-testnet && \
mv MY_PATH/* /tmp/chains/poc-1-testnet && \
mv /tmp/chains MY_PATH/
```

