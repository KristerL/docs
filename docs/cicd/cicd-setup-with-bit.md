---
id: cicd-setup-with-bit
title: CI/CD Setup with Bit
---

Use your CI/CD tool to install Bit components on consuming apps or publish component automatically to [bit.dev](https://bit.dev) or any remote Bit scope.

- If you want to only install components on the CI using NPM or Yarn, follow the steps bellow to [get a token](#get-a-bit-token), and [configure .npmrc on CI](#configure-npmrc-on-ci). [Go here](#common-errors) for information on common errors.
- If you want to version and export components, first [get a token](#get-a-bit-token) and [follow these steps](#version-and-export-components-on-ci).

## Get a Bit token

The first thing to do is get a Bit token that has access to scopes.  
You can create a dedicated user such as `dev@company.com` for the deployment, or use an existing user's token.

> If you are using an existing user's token, follow the steps described [here](/bit-dot-dev/authentication#additional-tokens) to generate a token that does not expire on local logins.

To get your token run the following command:

```shell
bit config get user.token
```

Set `user.token` as an environment parameter on your server named `BIT_TOKEN`.

## Install Components on CI

### Configure npmrc on CI

Bit components are stored on the bit registry located in `https://node.bit.dev`.

When installing components with npm or yarn, they will try to install the components starting with `@<account-name>` by resolving the `@<account-name>` registry. This configuration is stored in an `.npmrc` configuration file. Npm and yarn respect the following file locations:

- per-project config file (`/path/to/my/project/.npmrc`)
- per-user config file (`~/.npmrc`)
- global config file (`$PREFIX/etc/npmrc`)
- npm builtin config file (`/path/to/npm/npmrc`)

When working locally, `bit login` configures this file automatically for you. You will need to set this file manually for your CI. If not done right you will get these errors:

**NPM**

```shell
failed running npm install at /Users/user/devenv/example-npm-error/components/utils/string/pad-left
npm ERR! code E404
npm ERR! 404 Not Found: @bit/bit.utils.string.pad-left@0.0.1
```

**Yarn**

```shell
failed running yarn install at /Users/user/devenv/example-npm-error/components/utils/string/pad-left
error An unexpected error occurred: "https://registry.yarnpkg.com/@bit%2fbit.utils.string.pad-left: Not found".
```

The error is solved by making sure that one of the `.npmrc` files has the configuration prior to running npm install. The solutions vary per vendor (see below), but the main methods are:

- Define `.npmrc` in the project.
- Generate `.npmrc` file for the CI user
- Extend `.npmrc` configuration with vendor's tools

### Manually create npmrc file

You can add a `.npmrc` in your project with the following:

```shell
@<account-name>:registry=https://node.bit.dev
@teambit:registry=https://node.bit.dev
//node.bit.dev/:_authToken=${BIT_TOKEN}
always-auth=true
```

Define `BIT_TOKEN` as a secret global variable on the server.

### Generate `.npmrc` on server

To generate the file dynamically, you need to run the following script (e.g. create a `bit_npm.sh` script):

```shell
echo "Adding bit.dev to npm registry"
echo "always-auth=true" >> ~/.npmrc
echo "@<account-name>:registry=https://node.bit.dev" >> ~/.npmrc
echo "@teambit:registry=https://node.bit.dev" >> ~/.npmrc
echo "//node.bit.dev/:_authToken={$BIT_TOKEN}" >> ~/.npmrc
echo "Completed adding bit.dev to npm registry"
```

### Netlify

On Netlify, [you cannot generate the file dynamically](https://community.netlify.com/t/common-issue-using-private-npm-modules-on-netlify/795/11), and you should [add `.npmrc` file in your project](#define-npmrc-in-the-project).

Add the BIT_TOKEN as [environment variable](https://www.netlify.com/docs/continuous-deployment/#environment-variables)

Then, create a secret with the contents of your `~/.npmrc`.

```shell
now secrets add my-app-npmrc "$(cat ~/.npmrc)"
```

Note that `my-app-npmrc` is the name of the secret and can be named anything you wish.

### Gitlab

In `.gitlab-ci.yml` run the script that [generates the file for the user](#generate-npmrc-on-server) as an initial step before running npm install.

Add the BIT_TOKEN as an [environment variable](https://docs.gitlab.com/ee/ci/variables/)

### GitHub actions

Add the BIT_TOKEN as a [secret](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets) in GitHub.

In the GitHub workflow file, create a step before the npm install section:

```shell
- name: init bit.dev
  run: |
    echo "Adding bit.dev to npm registry"
    npm config set @bit:registry https://node.bit.dev
    npm config set @<account-name>:registry=https://node.bit.dev
    npm config set @teambit:registry=https://node.bit.dev
    npm config set //node.bit.dev/:_authToken ${BIT_TOKEN}
    echo "Completed adding bit.dev to npm registry"
  env:
    BIT_TOKEN: ${{ secrets.BIT_TOKEN }}
```

### Heroku

To generate the `.npmrc` before installing dependencies, run a pre-build script as described [here](https://devcenter.heroku.com/articles/nodejs-support#heroku-specific-build-steps).

Add the `BIT_TOKEN` as an [environment variable](https://devcenter.heroku.com/articles/config-vars#managing-config-vars)

### Azure pipelines

Use the [npm authenticate task](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/package/npm-authenticate?view=azure-devops) to setup the `.npmrc` configuration in your pipeline.

### Common Errors

#### 'package not found' (404) when importing a component

NPM or Yarn throws 'package not found' when importing a component. This is likely because the component has a dependency on a @bit component. Make sure [npmrc is configured](#bit-installed-components).

#### Unauthorized (401) when installing a component

Possible reasons:

- npmrc is not properly [configured](#bit-installed-components)
- You do not have the right permissions on the Collection that the components are hosted in, and are therefore unable to access its components. Make sure you have at least read permissions for the collection that host the components.
- Yarn does not send an authentication token when installing packages from a `yarn.lock` file. This is a [known issue](https://github.com/yarnpkg/yarn/issues/4451). Make sure `always-auth` is [configured in `.npmrc`](#bit-installed-components).

## Version and Export Components on CI

### Install and configure Bit

Follow the instructions in [bit docker readme](https://github.com/teambit/bit/blob/master/scripts/docker-teambit-bit) to get a docker with bit installed.

To configure bit on the server, you need to run the following commands:

```shell
bit config set user.token ${BIT_TOKEN}
```

### Versioning and Publishing

The flow for versioning and publishing components work as follows:

1. On your local computer run `bit tag` with the `--soft` option to annotate that modified components should be versioned and exported.
1. Bit updates `.bitmap` with information on new versions to publish.
1. Collaborate with your peers on the soon-to-be published components, their semantic version and changelog messages.
1. Merge changes to main branch.
1. CI/CD versions all marked components and publish them.
1. CI/CD commits back to the repository the updated `.bitmap` without annotations on versions to be exported.

> **Where is the 'test' and 'build'?**
>
> The `tag` command runs the 'build pipeline' before versioning a component. This pipeline includes building and testing. if any of these tasks fails, the versioning process will be aborted.

:::info use soft tags in local workspaces
Components in local workspaces should be 'soft-tagged'.
That means they are registered in the `.bitmap` file as pending to be versioned, but not yet versioned.
The versioning process should only happen in the CI (once changes to the workspace are pushed to the remote repository).
This enables collaboration on components before they are tagged and exported.  
[**Learn more**](/building-with-bit/exporting-components).
:::