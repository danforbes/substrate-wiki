When the change over happens:

- All PoC-1 era nodes will stop. (New blocks will be available on the new libp2p network, and not on the devp2p network, and there's no nodes that exist on both.)
- If you're already synced using a vaguely recent `master` build or `v0.1.69` or `v0.2-devp2p`, then you can easily upgrade once the changeover happens. You'll need to stop your node, upgrade the database (see below) and then start a new node build from either the `v0.2.0` tag, or the `v0.2` or `master` branches.
- If you want to get started right now, then you can also switch (or start a `v0.2`/`master` node) earlier, but your node will stall until the network switches runtime.

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

