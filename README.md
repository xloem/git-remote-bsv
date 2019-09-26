# git-remote-bsv

A git remote for the Bitcoin Satoshi Vision blockchain.

## Installation
Download node at [nodejs.org](http://nodejs.org) and install it, if you haven't already.

```sh
npm install -g git-remote-bsv
```

## Usage

git-remote-bsv handles bsv repos as pairs of urls, a private push one and a public pull one.
The private one is the private key to a BSV bitcoin account, and may upload changes.
The public one is the BSV bitcoin account address, and should be distributed to others.
The account must have enough coins to upload new changes.

Get a new pair of git remote urls:
```sh
$ git-remote-bsv

Here is a new url pair for you.  Remember to send coins to the pull address.
    push: bsv://Kxu7rmqc4zRw97KmvrA7mDTkXJt3KrM1UpnTc3TReNvKBm4pUpra/path/to/repo.git
    pull: bsv://1Kyrm8Gmyb4fcDesTLoCin63iUJiznCwe4/path/to/repo.git

```

Import an existing git repository to the BSV blockchain:
```sh
$ cd myrepo
$ git remote add bsv bsv://Kxu7rmqc4zRw97KmvrA7mDTkXJt3KrM1UpnTc3TReNvKBm4pUpra
$ git push bsv
# if it fails due to not enough satoshis: send coins to 1Kyrm8Gmyb4fcDesTLoCin63iUJiznCwe4 and try again
```

Clone a repo off BSV:
```
# without git-remote-bsv installed:
$ git clone https://bico.media/1Kyrm8Gmyb4fcDesTLoCin63iUJiznCwe4 myrepo

# with git-remote-bsv installed:
$ git clone bsv://1Kyrm8Gmyb4fcDesTLoCin63iUJiznCwe4 myrepo
```

Integrate changes from a BSV mirror:
```
# add remote without git-remote-bsv installed:
$ git remote add bsv https://bico.media/1Kyrm8Gmyb4fcDesTLoCin63iUJiznCwe4

# add remote with git-remote-bsv installed
$ git remote add bsv bsv://1Kyrm8Gmyb4fcDesTLoCin63iUJiznCwe4

# integrate
$ git pull bsv master
```

## How it Works

This section is a work in progress.

At the moment `git-remote-bsv` is a hacky wrapper around another piece of software called
`bsvup`.  It launches `bsvup` in a subprocess and automates providing input to it.

- [bsvup](https://github.com/monkeylord/bsvup): Put file/directory on BSV, using D protocol.

`git-remote-bsv` functions as a `gitremote-helpers(1)` remote helper program for git, offering
the `connect` functionality to spawn git services that connect to the repo via pipes.  To do
this easily, it stores a local copy of the bsv repo in `GIT_DIR/bsv/<bsvaddrr>/git`, and runs
`bsvup` in the parent folder to sync the changes upward.  When new changes come in, they are
stored using git's default mechanisms, but when a push occurs, they are all condensed into
a single packfile to preserve blockchain capacity and wallet balance.

The next advancement to `git-remote-bsv` should be to connect to a library with a programmatic
API, rather than launching a subprocess and scraping its output.  This should significantly aid
stability.

## Integrity Concerns

Although the data is stored in a decentralized blockchain, the APIs currently reference
centralized webservers by default.  This means for now, to evade real censorship, one must
check via their own BSV client and other trustworthy means that the data they uploaded
has made it properly to the real blockchain.  Once the data reaches the blockchain, it
is expected that it can never be irretrievably deleted or altered, ever, unless somebody
breaks the currency itself.  However, obviously anybody who has the private key can pretend
to be the author and submit further updates, even to the git history.  Such changes would
require investigation to find and undo, but would be very obvious when searched for.

The solution to the history rewriting problem is to advance beyond the D:// protocol
of bsvup and bico.media, to something simpler that retains the concept of an append-only
log.  Integrating the protocol into the client could also provide for the client to
ignore or undo history rewrites automatically.

## Recovering From Catastrophe

The original repo private key has been misplaced.  The original content is at:

    bsv::1K15pHmxCNBBq5NhrZXG8E7xaEV62cizs6

The new repo is at:

    bsv::1FJMa1Ac53zoKg2UrQEnafNqUFSvNRhmaL/git-remote-bsv.git

To deter future catastrophe, the new private push address is listed here:

    bsv::KzywA1GLJacYP7EBmAa4Pze2vKnTbYCdfcUkJUWuq8sLR6pHoT9h

Please do not steal this particular money.

## License
[Public Domain]()
