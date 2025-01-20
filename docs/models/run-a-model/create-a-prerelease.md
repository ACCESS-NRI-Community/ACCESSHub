[ACCESS models]: /models/configurations
[om2 repo]: https://github.com/ACCESS-NRI/ACCESS-OM2
[om2 config]: /models/configurations/access-om/#access-om2
[mom5 component]: /models/model_components/ocean/#mom5

!!! danger
    This page is tailored to experienced users and collaborators developing ACCESS models.<br>
    This step is *not* required if you *only* want to run a model. If you are looking for information on how to run a model, refer to the [Run a Model](/models/run-a-model) section.

# Creating Pre/Releases for an ACCESS Model

## About

The instructions below outline how to trigger automatic prerelease and release builds of an [ACCESS model][ACCESS models] by making use of the build and deployment workflows enabled by the [access-nri/build-cd repository](https://github.com/ACCESS-NRI/build-cd).

This allows, for example, to quickly test the development of a new feature in a specific ACCESS Model configuration.

As an example, in the following instructions we will show how to trigger a prerelease build of [ACCESS-OM2][om2 config], after having modified its [MOM5 component].<br>
All the other components will remain the same as the official [ACCESS-OM2 release][om2 repo].

!!! tip
    The instructions below remain valid (with simple tweaks) for all [ACCESS models].

## Prerequisites

- **Join the required projects specific to the HPC system**<br>
    For models deployed to `Gadi`, join the `vk83` project, by requesting membership on the respective [vk83](https://my.nci.org.au/mancini/project/vk83/join) NCI project pages.

    For more information on joining specific NCI projects, refer to [How to connect to a project](https://opus.nci.org.au/display/Help/How+to+connect+to+a+project).

- **_Write_ permissions to the related Model Deployment Repository**
    To request write permissions for a specific Model Deployment Repository, please [contact ACCESS-NRI](/about/contact/).

## The model deployment repository

All [ACCESS models] have their deployments on HPC systems controlled by an [ACCESS-NRI's GitHub](https://github.com/ACCESS-NRI) repository, named according to the specific model.<br>
For example, the deployment of [ACCESS-OM2][om2 config] is controlled by the [ACCESS-OM2 GitHub repository][om2 repo].

The model deployment repository also enables automatic prerelease build deployments of the model whenever commits are added to one of the repository's open Pull Requests (PR).<br>
Every commit within the same PR triggers an isolated prerelease build deployment for the same PR (every PR can be seen as a separate development configuration for the model).<br>
When a PR is merged, the related prereleases are deleted and an official release build is created.

Although the structure of different model deployment repositories can slightly differ, their top-level will always contain:

- a [`spack.yaml` file](#the-spackyaml-file)
- a [`config` directory](#the-config-directory)

### The spack.yaml file

The `spack.yaml` file defines the [_Spack_ environment](https://spack.readthedocs.io/en/latest/environments.html#environments-spack-yaml-spack-lock) that constrains the versions and features (variants) of all dependencies required to build a given ACCESS model. At build time, it is [concretized](https://spack.readthedocs.io/en/latest/environments.html#concretizing) into a single set of dependencies, creating a `spack.lock` file.

For more information on the `spack.yaml` file, refer to  [ACCESS-NRI's devdocs](https://github.com/ACCESS-NRI/dev-docs/wiki/Spack#the-spackyaml-file-spec-file).

### The config directory

The `config` directory contains a single `versions.json` file. This file allows customisation of both the version of [`access-nri/spack`](https://github.com/ACCESS-NRI/spack) used to deploy the model and the version of [`access-nri/spack-packages`](https://github.com/ACCESS-NRI/spack-packages) that will source _Spack_ packages recipes.

## Trigger a model pre/release build deployment

As mentioned above, prerelease build deployments are triggered by commits within a model deployment repository's open PR. When the PR is merged, a release build deployment is created.<br>
The sequence of tasks needed to trigger a pre/release build deployment for a new model feature is detailed below:

#### 1. Clone the repo and create a feature branch
The first step is to clone the model deployment repository and create a feature branch from its `main` branch, where the new features will be developed.

To clone the [ACCESS-OM2 deployment repository][om2 repo] and create a feature branch named `update_mom5_dev_build` run:
```
git clone https://github.com/ACCESS-NRI/ACCESS-OM2.git
cd ACCESS-OM2
git checkout -b update_mom5_dev_build
```
<terminal-window>
    <terminal-line data="input">git clone https://github.com/ACCESS-NRI/ACCESS-OM2.git</terminal-line>
    <terminal-line>Cloning into 'ACCESS-OM2'...</terminal-line>
    <terminal-line>remote: Enumerating objects: 442, done.</terminal-line>
    <terminal-line>remote: Counting objects: 100% (170/170), done.</terminal-line>
    <terminal-line>remote: Compressing objects: 100% (68/68), done.</terminal-line>
    <terminal-line>remote: Total 442 (delta 137), reused 110 (delta 98), pack-reused 272 (from 1)</terminal-line>
    <terminal-line>Receiving objects: 100% (442/442), 143.84 KiB | 3.79 MiB/s, done.</terminal-line>
    <terminal-line>Resolving deltas: 100% (219/219), done.</terminal-line>
    <terminal-line data="input">cd ACCESS-OM2</terminal-line>
    <terminal-line data="input" directory="ACCESS-OM2">git checkout -b update_mom5_dev_build</terminal-line>
    <terminal-line directory="ACCESS-OM2">Switched to a new branch 'update_mom5_dev_build'</terminal-line>
</terminal-window>

#### 2. Apply the modifications and commit the changes {: id="modifications"}
Then, modifications can be made to the `spack.yaml` or `config/versions.json` files as needed (e.g., update packages versions, add or change variants, etc.).<br>
After the modifications are made, commit the changes to the newly-created feature branch and push the changes upstream.

In this example, we will change ACCESS-OM2's [MOM5 component] (we will replace it with the version from [MOM5 `development` branch](https://github.com/ACCESS-NRI/MOM5/tree/development)).
To achieve this, we will:

1. Update the [version of the `mom5` package](https://github.com/ACCESS-NRI/ACCESS-OM2/blob/d907f3314a9956875baaaaf2b4d7b6be6fa81926/spack.yaml#L15) in the `spack.yaml` file with the new version (`@git.development`).
2. Update the [associated module projection](https://github.com/ACCESS-NRI/ACCESS-OM2/blob/d907f3314a9956875baaaaf2b4d7b6be6fa81926/spack.yaml#L53) to `{name}/development-{hash:7}`.<br>

    !!! tip
        The `{hash:7}` part is used so the module doesn't conflict with other versions.

3. It is also recommended to update the [overall ACCESS-OM2 version](https://github.com/ACCESS-NRI/ACCESS-OM2/blob/d907f3314a9956875baaaaf2b4d7b6be6fa81926/spack.yaml#L8) along with its [associated module projection](https://github.com/ACCESS-NRI/ACCESS-OM2/blob/d907f3314a9956875baaaaf2b4d7b6be6fa81926/spack.yaml#L51).<br>
   This is particularly important before a PR gets merged, as this will determine the version tag for the model new release. The format is `CALVER_YEAR.CALVER_MONTH.MINOR`.<br>
   For this example, we will update the overall version to `git.2024.03.1`.

After the modifications listed above, the output of the command
```
git diff
```
should look like the following:
<terminal-window>
    <terminal-line data="input" directory="ACCESS-OM2">git diff</terminal-line>
    <terminal-line class="git-highlighted">diff --git a/spack.yaml b/spack.yaml</terminal-line>
    <terminal-line lineDelay=0 class="git-highlighted">index bfa6df2..be80333 100644</terminal-line>
    <terminal-line lineDelay=0 class="git-highlighted">--- a/spack.yaml</terminal-line>
    <terminal-line lineDelay=0 class="git-highlighted">+++ b/spack.yaml</terminal-line>
    <terminal-line lineDelay=0 class="git-cyan">@@ -5,14 +5,14 @@</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks"> spack:</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">   # add package specs to the `specs` list</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">   specs:</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks git-red">-    - access-om2@git.2024.03.0</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks git-green">+    - access-om2@git.2024.03.1</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">   packages:</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">    cice5:</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">    require:</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">        - '@git.2023.10.19'</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">    mom5:</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">    require:</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks git-red">-        - '@git.2023.11.09'</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks git-green">+        - '@git.development'</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">    libaccessom2:</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">    require:</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">        - '@git.2023.10.26'</terminal-line>
    <terminal-line lineDelay=0 class="git-cyan">@@ -48,8 +48,8 @@ spack:</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">        - libaccessom2</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">        - oasis3-mct</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">        projections:</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks git-red">-          access-om2: '{name}/2024.03.0'</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks git-green">+          access-om2: '{name}/2024.03.1'</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">        cice5: '{name}/2023.10.19-{hash:7}'</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks git-red">-          mom5: '{name}/2023.11.09-{hash:7}'</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks git-green">+          mom5: '{name}/development-{hash:7}'</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">        libaccessom2: '{name}/2023.10.26-{hash:7}'</terminal-line>
    <terminal-line lineDelay=0 class="keep-blanks">        oasis3-mct: '{name}/2023.11.09-{hash:7}'</terminal-line>
</terminal-window>

To stage and commit the changes we can run:
```
git commit -am "Updated mom5 to development version."
```
<terminal-window>
    <terminal-line data="input" directory="ACCESS-OM2">git commit -am "Updated mom5 to development version."</terminal-line>
    <terminal-line>[update_mom5_dev_build 7cdac6f] Updated mom5 to development version.</terminal-line>
    <terminal-line class="keep-blanks"> 1 file changed, 4 insertions(+), 4 deletions(-)</terminal-line>
</terminal-window>

Finally, we can push the changes upstream by executing:
```
git push --set-upstream origin update_mom5_dev_build
```
<terminal-window>
    <terminal-line data="input" directory="ACCESS-OM2">git push --set-upstream origin update_mom5_dev_build</terminal-line>
    <terminal-line>Enumerating objects: 5, done.</terminal-line>
    <terminal-line>Counting objects: 100% (5/5), done.</terminal-line>
    <terminal-line>Delta compression using up to 8 threads</terminal-line>
    <terminal-line>Compressing objects: 100% (3/3), done.</terminal-line>
    <terminal-line>Writing objects: 100% (3/3), 565 bytes | 565.00 KiB/s, done.</terminal-line>
    <terminal-line>Total 3 (delta 2), reused 0 (delta 0), pack-reused 0</terminal-line>
    <terminal-line>remote: Resolving deltas: 100% (2/2), completed with 2 local objects.</terminal-line>
    <terminal-line>remote:</terminal-line>
    <terminal-line>remote: Create a pull request for 'update_mom5_dev_build' on GitHub by visiting:</terminal-line>
    <terminal-line class="keep-blanks">remote:      https://github.com/ACCESS-NRI/ACCESS-OM2/pull/new/update_mom5_dev_build</terminal-line>
    <terminal-line>remote:</terminal-line>
    <terminal-line>To https://github.com/ACCESS-NRI/ACCESS-OM2.git</terminal-line>
    <terminal-line class="keep-blanks"> * [new branch]      update_mom5_dev_build -> update_mom5_dev_build</terminal-line>
    <terminal-line>branch 'update_mom5_dev_build' set up to track 'origin/update_mom5_dev_build'.</terminal-line>
</terminal-window>

#### 3. Open a PR
To trigger a prerelease build, [open a PR](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request) with the `main` branch of the model deployment repository as the *base*. This will start a model build attempt by [GitHub's CI/CD](https://github.com/resources/articles/devops/ci-cd) infrastructure. As a result, a comment is added by `github-actions[bot]` saying that the build is deploying.<br>
Once the deployment is [successful](#successful-deployment), you will be able to access the prerelease on the listed HPC systems by following the instructions specified in the comment's _Details and usage instructions_.

There are three main _statuses_ for a deployment that can be identified by looking at the [GitHub Environment diaglog box](https://github.com/ACCESS-NRI/ACCESS-OM2/pull/94#:~:text=Show%20environments) in the related PR:

- **Successful**<br>
  A successful deployment is identified by a green (_Active_ deployment) or white (_Inactive_ deployment) icon, with the _Active_ one being the most recent deployment within the repository.<br>
  If a deployment is successful, it can be accessed on HPC systems.<br>
  ![Active deployment](/assets/create_a_prerelease/active_deployment.png){: style="max-width: 650px;" class="example-img" loading="lazy"}
  ![Inactive deployment](/assets/create_a_prerelease/inactive_deployment.png){: style="max-width: 650px;" class="example-img" loading="lazy"}
  {: id="successful-deployment"}

- **In Progress**<br>
  The deployment is still ongoing. It will soon become [successful](#successful-deployment) or [fail](#failed-deployment).<br>
  ![In progress deployment](/assets/create_a_prerelease/in_progress_deployment.png){: style="max-width: 650px;" class="example-img" loading="lazy"}
  {: id="in-progress-deployment"}

- **Failed**<br>
  The deployment failed and the CI/CD log can be viewed by clicking on [Show environments](https://github.com/ACCESS-NRI/ACCESS-OM2/pull/94#:~:text=Show%20environments) in the GitHub Environment diaglog box.<br>
  ![Failed deployment](/assets/create_a_prerelease/failed_deployment.png){: style="max-width: 650px;" class="example-img" loading="lazy"}

If we open a PR to the [ACCESS-OM2 deployment repository][om2 repo] with our `update_mom5_dev_build` branch as the *base*, we will get a [comment](https://github.com/ACCESS-NRI/ACCESS-OM2/pull/94#issuecomment-2594604585) and, once deployed, the prerelease build can be accessed through the module `access-om2/pr94-1`:
![GitHub bot comment](/assets/create_a_prerelease/comment.png){: class="example-img"}

!!! tip
    If further changes are required within the same PR, additional commits can be pushed onto the feature branch to create more prerelease builds. This does not remove earlier prerelease builds, allowing concurrent testing of multiple different builds.<br>
    For example, adding an additional commit to the `update_mom5_dev_build` branch (while its PR is open) will trigger another prerelease build and allow the use of the module `access-om2/pr94-2`, once deployed.

#### 4. Merge or close the PR
If the PR gets merged, an official ACCESS-NRI release build of the given model will be created, along with an associated GitHub Release.

!!! warning
    When a PR is closed (this includes merged PRs), regardless if it was merged or not, all the related prereleases deployments get removed.<br>
    You can retrieve a single prerelease build by [creating a _Spack_ environment](https://spack.readthedocs.io/en/latest/environments.html#creating-a-managed-environment) using the `spack.yaml`/`spack.lock` artifact related to the specific commit.

## Comment Commands

Another way to trigger the CI/CD pipeline is by commenting on a model deployment repository's open PR with a specific _Comment Command_.<br>
This will trigger the CI/CD pipeline and perform certain actions, depending on the specific _Comment Command_.

!!! info
    All _Comment Commands_ must be at the beginning of the comment, without any leading spaces.

The following _Comment Commands_ are available in all model deployment repositories:

#### !bump
!!! info
    Requires the commenter to have write permissions on the repository.

<h5>Usage</h5> <!-- Using HTML syntax to avoid paragraph being added in the table of contents on the right-side of the website -->

```
!bump [major|minor]
```

<h5>Description</h5> <!-- Using HTML syntax to avoid paragraph being added in the table of contents on the right-side of the website -->

Convenience function that automatically bumps the overall model version in the `spack.yaml` file  and commits the result to the PR branch.<br>
The overall model version is formatted as `YEAR.MONTH.MINOR`.<br>
`!bump major` bumps the model version to the next major version, formatted as `YEAR.MONTH.0`. Here `YEAR` and `MONTH` correspond to the current year and month when the comment is issued.<br>
`!bump minor` bumps the model version to the next minor version, formatted as `YEAR.MONTH.(MINOR+1)`. Here `YEAR`, `MONTH` and `MINOR` are the same as the previous version.

<h5>Example</h5> <!-- Using HTML syntax to avoid paragraph being added in the table of contents on the right-side of the website -->

Let's take as example an open PR that has its overall model version set to `git.2024.06.2`.<br>
If we commented the PR in Jan 2025 with `!bump major`, the overall model version would be bumped to `git.2025.01.0`.<br>
If instead, we commented the PR with `!bump minor`, the overall model version would be bumped to `git.2024.06.3` (regardless of the comment date).

#### !redeploy
!!! info
    Requires the commenter to have write permissions on the repository.

<h5>Usage</h5> <!-- Using HTML syntax to avoid paragraph being added in the table of contents on the right-side of the website -->

```
!redeploy
```

<h5>Description</h5> <!-- Using HTML syntax to avoid paragraph being added in the table of contents on the right-side of the website -->

Convenience function that triggers a new independent deployment of the `HEAD` of the model deployment repository PR branch.

This is most useful, for example, to trigger a new deployment when changes are made to a model dependency used by the model, without any changes occurring in the `HEAD` of the PR branch

<h5>Example</h5> <!-- Using HTML syntax to avoid paragraph being added in the table of contents on the right-side of the website -->

Let's say we make the [modifications](#modifications) above, updating the ACCESS-OM2 MOM5 component version to the one from MOM5 repository's `development` branch (by changing the `mom5` `require` version to `git.development` in the [`spack.yaml`](https://github.com/ACCESS-NRI/ACCESS-OM2/blob/d907f3314a9956875baaaaf2b4d7b6be6fa81926/spack.yaml#L15)).<br>
We then push the commits to the `update_mom5_dev_build` branch, open a PR to `main`, and we get a [successful deployment](#successful-deployment) of our ACCESS-OM2 _mom5-development_ build (version 1).<br>
Subsequently, we decide to make some further changes to the MOM5 `development` branch and we push the commits upstream.<br>
Now, if we want to test these new MOM5 changes, we would have to redeploy the latest commit in the `update_mom5_dev_build` branch to reflect the updates to the MOM5 `development` branch.<br>
Since the `HEAD` of the `update_mom5_dev_build` did not change, instead of having to create a new "redundant" commit only to trigger the prerelease deployment, we can comment `!redeploy` in the `update_mom5_dev_build` PR. This will force the CI/CD pipeline to redeploy the model build with the latest modifications in the MOM5 `development` branch.<br>
As a result, we will get a new separate deployment of the ACCESS-OM2 _mom5-development_ build (version 2).

!!! warning
    It is up to the user to monitor the state of each dependency's `git` branch listed in the `spack.yaml` file before using a `!redeploy` _Comment Command_. The CI/CD pipeline does not perform any checks and will simply redeploy the `HEAD` of the model deployment repository's PR branch, using the `HEAD` of all corresponding dependency `git` branches specified in the `spack.yaml` file.

## Backporting Bugfixes

The `main` branch of a model deployment repository reflects the most up-to-date changes to the model and is always at the same level or ahead of the model latest major release.<br>
However, it is also essential to provide a way to apply bug fixes to earlier major versions of the model. This is where dedicated `backport/YEAR.MONTH` branches come into play, allowing bug fixes to be incorporated into previous versions of the model.

For example, let's say there is a bug fix that needs to be incorporated to the [ACCESS-OM2 `2023.11.23` release](https://github.com/ACCESS-NRI/ACCESS-OM2/tree/2023.11.23).<br>
To do so, a developer should:

1. Create a `backport/2023.11` branch (if it doesn't already exist) from the `2023.11.23` tag:
    ```
    git checkout -b backport/2023.11 2023.11.23
    ```
    <terminal-window>
        <terminal-line data="input" directory="ACCESS-OM2">git checkout -b backport/2023.11 2023.11.23</terminal-line>
        <terminal-line>Switched to a new branch 'backport/2023.11'</terminal-line>
    </terminal-window>
2. Apply the bug fixes, commit and push the changes:
    ```
    git commit -am "Bug fix."
    git push --set-upstream origin backport/2023.11
    ```
    <terminal-window>
    <terminal-line data="input" directory="ACCESS-OM2">git commit -am "Bug fix."</terminal-line>
    <terminal-line>[backport/2023.11 cac60f0] Bug fix.</terminal-line>
    <terminal-line class="keep-blanks"> 1 file changed, 2 insertions(+), 1 deletions(-)</terminal-line>
    <terminal-line data="input" directory="ACCESS-OM2">git push --set-upstream origin backport/2023.11</terminal-line>
    <terminal-line>Enumerating objects: 5, done.</terminal-line>
    <terminal-line>Counting objects: 100% (5/5), done.</terminal-line>
    <terminal-line>Delta compression using up to 8 threads</terminal-line>
    <terminal-line>Compressing objects: 100% (3/3), done.</terminal-line>
    <terminal-line>Writing objects: 100% (3/3), 565 bytes | 565.00 KiB/s, done.</terminal-line>
    <terminal-line>Total 3 (delta 2), reused 0 (delta 0), pack-reused 0</terminal-line>
    <terminal-line>remote: Resolving deltas: 100% (2/2), completed with 2 local objects.</terminal-line>
    <terminal-line>remote:</terminal-line>
    <terminal-line>remote: Create a pull request for 'backport/2023.11' on GitHub by visiting:</terminal-line>
    <terminal-line class="keep-blanks">remote:      https://github.com/ACCESS-NRI/ACCESS-OM2/pull/new/backport/2023.11</terminal-line>
    <terminal-line>remote:</terminal-line>
    <terminal-line>To https://github.com/ACCESS-NRI/ACCESS-OM2.git</terminal-line>
    <terminal-line class="keep-blanks"> * [new branch]      backport/2023.11 -> backport/2023.11</terminal-line>
    <terminal-line>branch 'backport/2023.11' set up to track 'origin/backport/2023.11'.</terminal-line>
    </terminal-window>
3. Open a PR off the `backport/2023.11` branch with the fixes.<br>
   When the PR is merged, a release tagged as `2023.11.24` will be created.
