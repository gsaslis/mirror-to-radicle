# Mirror to Radicle

A GitHub Action that mirrors your code to the peer-to-peer Radicle code hosting
network, where Open Source Software can thrive beyond corporate agendas and
constraints.

## Why?

In case you have been feeling more and more uncomfortable with the idea that all
the world's Open Source Software is being
hosted on a single platform, there is now an alternative!

The [Radicle](https://radicle.xyz) peer-to-peer network now allows you to host
your code in a decentralized manner. Everyone participating in this network can
then download your code! For more details about Radicle, you can check out the
various [Guides](https://radicle.xyz/guides).

Start mirroring your code to Radicle!  ✊

## Usage

> [!NOTE]
> Before using this action in your repo, to continuously
> mirror your code to Radicle, you have to do a one-time setup of creating
> the Radicle repository where the code will be mirrored *to*.

Here is the bird's eye view:

1. Install Radicle CLI on your machine,
1. Create your Radicle identity,
1. Publish your project on Radicle,
1. Set up _machine account_ for continuous mirroring
1. Use this GitHub Action on your project

Here goes:

### 1. Install `rad` Command Line Interface tool.

Visit [this page](https://radicle.xyz/guides/user#installation) for
other installation instructions.

```shell
curl -sSf https://radicle.xyz/install | sh
```

### 2. Create your Radicle Identity

Feel free to skip this step if you have previously created one.

```shell
rad auth
```

Once you fill in the details, the output should look like this:

```shell
$ rad auth

Initializing your radicle 👾 identity

✓ Enter your alias: yorgos
✓ Enter a passphrase: ********
✓ Creating your Ed25519 keypair...
✓ Adding your radicle key to ssh-agent...
✓ Your Radicle DID is did:key:z6MkrnXJWPndzPBxpBUaE3L3BnMeWpaQdT1V1FvkoCPFSFS3. This identifies your device. Run `rad self` to show it at all times.
✓ You're all set.
...
```

#### (Optionally) Publish your Radicle Identity on GitHub

One way to make your new identity known to your network is to publish it
on GitHub. If your connections trust your GitHub profile, they will know
they can trust this new identity as well.

Edit your "Social Profiles" section on GitHub to add a link to your
newly-created identity:

- run `rad self --did` and use the output in place of `$your_did_key` below
- Enter the following URL:
  `https://app.radicle.xyz/seeds/seed.radicle.garden/users/$your_did_key`

This will add a an `<a rel="me">` link on your github.com/$username page,
allowing everyone to _verify_ this is indeed your own key.

This is one way that you can tell your contacts that when they see
commits signed by your key in the Radicle network, it's really you.

_Of course, this implies trust in Microsoft / GitHub._

### 3. Publish your project on Radicle !

In order to publish your project to Radicle, you'll need to start a Radicle
node on your machine, so that it can let other nodes in the network know
about your project.

```shell
# first, check whether you've already started your node
$ rad node status

# now, go ahead and start it! Don't worry, you can stop it later 
# with `rad node stop` 
$ rad node start 
```

If this doesn't work, or if you need more details on starting your node, 
see the [User Guide](https://radicle.xyz/guides/user#operate-nodes-smoothly).

Once your node is running, and it has connected to other nodes, you can go
ahead and publish your project, by announcing it to the network! 

Here is an example, publishing this very repo: 

```shell
$ git clone https://github.com/gsaslis/mirror-to-radicle
$ cd mirror-to-radicle 
$ rad init

Initializing radicle 👾 repository in <redacted>/projects/mirror-to-radicle..

✓ Name mirror-to-radicle
✓ Description A GitHub Action that mirrors your code to the peer-to-peer Radicle code hosting network, where Open Source Software can thrive beyond corporate agendas and constraints.
✓ Default branch main
✓ Visibility public
✓ Repository mirror-to-radicle created.

Your Repository ID (RID) is rad:z3pN2URJoxrZSrnwKoYtWrgfS8qLL.
You can show it any time by running `rad .` from this directory.

◣ Uploading to 2 peer(s) (8 KiB | 9 /s)✓ Repository successfully synced to z6MkscZMbsFa1b6kabLTobPpbT9sfHJGxKGzbDsMMMNNkKYb
◥ Uploading to 1 peer(s) (2 KiB | 2 /s)✓ Repository successfully synced to z6Mkeqfa5JWDhS7kqVJvc8avpEbBbGVHhWjpXj1EgE9uHMWp
✓ Repository successfully synced to z6MkqSwSCdG9KVb4HbwyU2gyGWXunWuy3T8YsvxMpnHbJX4u
◢ Uploading to 1 peer(s) (8 KiB | 5 /s)✓ Repository successfully synced to z6Mkmqogy2qEM2ummccUthFEaaHvyYmYBYh3dbe9W4ebScxo
✓ Repository successfully synced to 4 node(s).

Your repository has been synced to the network and is now discoverable by peers.
```

You should already be able to see that some permissive nodes are seeding your
project! Try visiting the below URL, replacing `$radicle-repository-id` 
for your own: 

`https://app.radicle.xyz/seeds/ash.radicle.garden/$radicle-repository-id` and
browse your repository on Radicle!

> [!TIP]
> **A Note on _Keeping_ your project online:** One of the nice
> things with Radicle is that peers can run nodes with a _permissive_
[policy](https://radicle.xyz/guides/seeder#a-permissive-seeding-policy), to
> help support the health of the network and the overall availability of
> Open Source Software (OSS) to the world at large.
> As long as a single permissive node exists on the network, you
> can rest assured that your project will be available to everyone else on the
> network. The more permissive nodes there are, the greater the resilience and
> availability of your project will be.
> 
>If you don't want / can't rely on permissive nodes, you can also [run your own
seed node](https://radicle.xyz/guides/seeder#running-your-node) to ensure
your project remains seeded.



### 4. Set up _machine account_ for continuous mirroring

**Machine Account creation**

The next step, now that your project is already part of the Radicle network,
is to create another identity that can push to this project, on your behalf.

In Radicle, all code is signed by a private key. When you push your code to
GitHub, your commits may or may not be signed by a private key. However,
for them to be pushed to Radicle, they must be signed by a Radicle identity.

> [!NOTE]
> You already created a Radicle identity to publish your project. At the time
of writing this, **all identities on Radicle are meant to be used on a
single-device at a time**. (There is a lot of work happening to improve this in
the future). For this reason, you should keep the keys of the identity
you created on your laptop/desktop alone.

> Because this GitHub action will be running somewhere other than your laptop,
you need to create a 2nd Radicle identity, to be used essentially as
a _machine account_ by GitHub Actions.

To create this new identity, you'll just need to define a new folder where
its data will be stored. We use `~/.radicle_actions` in the example below.

```shell
$ export RAD_HOME=~/.radicle_actions
$ rad auth
```

The output, once you fill in the form, should look something like this:

```shell
Initializing your radicle 👾 identity

✓ Enter your alias: yorgos_actions
✓ Enter a passphrase: ********
✓ Creating your Ed25519 keypair...
✓ Adding your radicle key to ssh-agent...
✓ Your Radicle DID is did:key:z6MkqewxVQjPk8cSNJWxjYGmwZSu8tk1cwnb3Z8mQzXNMxnF. This identifies your device. Run `rad self` to show it at all times.
✓ You're all set.

To create a Radicle repository, run `rad init` from a Git repository with at least one commit.
To clone a repository, run `rad clone <rid>`. For example, `rad clone rad:z3gqcJUoA1n9HaHKufZs5FCSGazv5` clones the Radicle 'heartwood' repository.
To get a list of all commands, run `rad`.
```

Now go ahead and seed and fork the project from the machine account, so you
can authorize it in the next step. To do that, you first need to start the 
node with the machine account identity:

```shell
# you can run this in any path, you don't have to be in a specific folder 
$ export RAD_HOME=~/.radicle_actions
# start your node so you can clone the repo and create your /storage copy/
rad node start
```

Now you can go ahead and proceed with the fork. Remember to use your own 
`REPO_ID` when copy-pasting:

```shell
$ export REPO_ID=rad:z3pN2URJoxrZSrnwKoYtWrgfS8qLL
rad clone ${REPO_ID}
rad seed ${REPO_ID}
# this creates your own namespace in the git refs and is required so you can be 
# added to the project delegates 
rad fork ${REPO_ID}
```



**Machine Account authorization**

As the last setup step, it is time to authorize the _machine account_ you
created to sign and mirror your code from GitHub -> Radicle.

In Radicle, the users who have permissions on a project are called
`delegates`. To add your machine account as a delegate of the project you
created, you need a couple more bash commands:

```shell
# Need to act as the original author of the project (so we switch RAD_HOME) 
# This needs to run in the folder of your project
export RAD_HOME=~/.radicle
rad id update \
  --title "Add machine account" \
  --description "Adds machine account used by GitHub Actions for continuous mirroring" \
  --delegate $(RAD_HOME=~/.radicle_actions rad self --did) \
  --threshold 1
rad sync 
```

You are now ready to set up this GitHub action! See next section below 👇 

### 5. Add this GitHub Action to your repo

Once the setup above has been completed, you will have all the necessary
information to set up this GitHub Action
on your repo and start mirroring to Radicle.

#### Add the necessary secrets

First, create the necessary `Environment` (e.g. a new one, called `radicle`),  
by visiting the below URL:

`https://github.com/$github_user_or_org/$project/settings/environments`

> [!WARNING]
> If you are using GitHub Free, environment secrets are only available in 
> public repositories, so if you're trying to mirror a private project to 
> Radicle and you don't have this option available, you can just create 
> ordinary Repository secrets.

Within this environment, you will need to create 4 secrets:

- `RADICLE_IDENTITY_ALIAS`: the alias you used when creating the GitHub Actions
  machine account. In the example above,
  this was `yorgos_actions`.
- `RADICLE_IDENTITY_PASSPHRASE`: the passphrase you used to protect the private
  key of the GitHub Actions machine account. In case you left the passphrase 
  empty, you can skip this secret.
- `RADICLE_IDENTITY_PRIVATE_KEY`: the **base64-encoded format** of the private
  key of the GitHub Actions machine account. You
  can get this with, e.g. `cat ~/.radicle_actions/keys/radicle | base64`
- `RADICLE_IDENTITY_PUBLIC_KEY`: the **base64-encoded format** of the public key
  of the GitHub Actions machine account. You
  can get this with, e.g. `cat ~/.radicle_actions/keys/radicle.pub | base64`

<img src="/assets/github_environment_with_secrets.png" alt="Prefer Environment secrets, so you can reuse your machine account on other projects" width="400">

> [!NOTE]
> It is also possible to create these 4 secrets as project-specific secrets, 
> but that would require you to recreate them for each project you want to 
> mirror to Radicle. You can find more documentation about Environment secrets
> [here](https://docs.github.com/en/actions/managing-workflow-runs-and-deployments/managing-deployments/managing-environments-for-deployment#environment-secrets),


Once those are set up, head on over to your project's Actions Secrets and create
the remaining 2 secrets:

- `RADICLE_PROJECT_NAME`: the project name you assigned to this project when you
  ran `rad init`. You can also find this by running `rad inspect --payload` 
  inside the folder of your radicle project. 
- `RADICLE_REPOSITORY_ID`: the repository id that you got after you created. You
  can also find this by running `rad .` inside the folder of your radicle 
  project.

<img src="/assets/github_project_actions_secrets.png" alt="The Secrets on your project settings" width="400">

#### Add the GitHub Actions workflow

As the final step, you'll need to create a new file in your project's 
`.github/workflows/` folder - say `.github/workflows/radicle.yaml` - to store
this new workflow:

```shell
# inside your project folder 
mkdir -p .github/workflows
touch .github/workflows/radicle.yaml
```

Now paste the below workflow in the file you just created:

```yaml
name: Mirror to Radicle

# Controls when the workflow will run
on:
  push:

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:

      - id: mirror
        uses: gsaslis/mirror-to-radicle@v0.1.0
        with:
          radicle-identity-alias: "${{ secrets.RADICLE_IDENTITY_ALIAS }}"
          radicle-identity-passphrase: "${{ secrets.RADICLE_IDENTITY_PASSPHRASE }}"
          radicle-identity-private-key: "${{ secrets.RADICLE_IDENTITY_PRIVATE_KEY }}"
          radicle-identity-public-key: "${{ secrets.RADICLE_IDENTITY_PUBLIC_KEY }}"
          radicle-project-name: "${{ secrets.RADICLE_PROJECT_NAME }}"
          radicle-repository-id: "${{ secrets.RADICLE_REPOSITORY_ID }}"
```

Commit and push this code in a new branch, opening a PR so you can check whether 
the workflow runs successfully: 

```shell
# in the folder of the cloned repo from github
git checkout -b feat/mirror-to-radicle
git add .github/workflows/radicle.yaml
git commit -m 'Enable mirroring to Radicle'
git push
```

Open a PR with this change from the GitHub web UI, or, if you have the CLI
installed:

```shell
# in the folder of the cloned repo from github
gh pr create --fill
```


### Verify it worked

You should now be able to open the PR, visit the Checks tab and check whether
the job has run successfully. 

If it has, you can try visiting:
https://app.radicle.xyz/seeds/ash.radicle.garden/$radicle-repository-id and you 
should be able to see even this latest 

> [!NOTE]
> `ash.radicle.garden` is just one of the permissive nodes seeding content to 
> support the Radicle network. Please feel free to run your own node to seed 
> this repo. 

> [!TIP]
> Reach out to us on Zulip (see below), if you'd like to have your repos 
> seeded by an additional node (gratis! ;) ).  

## Contributing

Please feel free to join
the [Radicle zulip chat](https://radicle.zulipchat.com/#narrow/channel/369873-support)
if you are interested in trying this out and have some questions.  