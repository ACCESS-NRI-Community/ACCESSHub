# Contribute on GitHub

## Prerequisites
:fontawesome-regular-square-check:{: class="twemoji icon-before-text nri-green-color"} If you are new to GitHub, you will first need to [sign up](https://github.com) for a GitHub account.

:material-head-lightbulb-outline:{: class="twemoji icon-before-text nri-orange-color"} We also encourage you to complete [self-paced GitHub learning courses](#additional-github-learning-resources) for brushing up on basic GitHub skills. 

## Let's get started!

This documentation is written in Markdown format and is based on the [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) theme, which is built on top of [MkDocs](https://www.mkdocs.org) static site generator.<br>
For a quick reference on how to use Markdown syntax you can refer to the [Markdown Cheat Sheet](https://www.markdownguide.org/cheat-sheet/).

## Raise a GitHub Issue

All contributions need to have an associated [GitHub issue](https://docs.github.com/en/issues/tracking-your-work-with-issues/) that explains the content and importance of the contribution. If your contribution does not already have an associated issue, [create a new issue](https://github.com/ACCESS-NRI/access-hive.org.au/issues/new?template=simple-issue-template.md) related to it. Feel free to assign the related issue to yourself if you intend to work on it.

## Fork the ACCESS-Hive Docs GitHub repository

After raising a GitHub issue about your contribution, you need to [fork](https://docs.github.com/en/get-started/quickstart/fork-a-repo?platform=mac&tool=webui) the [ACCESS-Hive Docs GitHub repository](https://github.com/ACCESS-NRI/access-hive.org.au).

## Clone the forked ACCESS-Hive Docs GitHub repository locally
For cloning the forked repository onto your local computer, we encourage you to first add your local SSH keys to your GitHub profile. 

To start with, if you have an [existing SSH key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/checking-for-existing-ssh-keys), then simply [add this key to your github account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account), and you are all set to clone the repository. In case you don't have the SSH keys set up on your local computer, it's easy to [create a new SSH key locally](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent), and then [feel free to link this key to your github account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account).

<terminal-window>
    <terminal-line data="input">git clone git@github.com:ACCESS-NRI/access-hive.org.au.git</terminal-line>
    <terminal-line></terminal-line>
    <terminal-line>Cloning into 'access-hive.org.au'...</terminal-line>
    <terminal-line>remote: Enumerating objects: 54754, done.</terminal-line>
    <terminal-line>remote: Counting objects: 100% (4096/4096), done.</terminal-line>
    <terminal-line>remote: Compressing objects: 100% (560/560), done.</terminal-line>
    <terminal-line>remote: Total 54754 (delta 2178), reused 4053 (delta 2155), pack-reused 50658</terminal-line>
    <terminal-line>Receiving objects: 100% (54754/54754), 103.15 MiB | 6.97 MiB/s, done.</terminal-line>
    <terminal-line>Resolving deltas: 100% (28610/28610), done.</terminal-line>
</terminal-window>

## Make changes locally
After cloning the repository locally, create a new branch to make your local changes and then push it to GitHub: 

<terminal-window>
    <terminal-line data="input">git checkout -b spongebob/fix-hive-title-issue-234</terminal-line>
    <terminal-line>Switched to a new branch 'spongebob/fix-hive-title-issue-234'</terminal-line>
</terminal-window>

<terminal-window>
    <terminal-line data="input">git push --set-upstream origin spongebob/fix-hive-title-issue-234</terminal-line>
    <terminal-line>Total 0 (delta 0), reused 0 (delta 0), pack-reused 0</terminal-line>
    <terminal-line>remote: </terminal-line>
    <terminal-line>remote: Create a pull request for 'spongebob/fix-hive-title-issue-234' on GitHub by visiting:</terminal-line>
    <terminal-line>remote: https://github.com/ACCESS-NRI/access-hive.org.au/pull/new/spongebob/fix-hive-title-issue-234</terminal-line>
    <terminal-line>remote: </terminal-line>
    <terminal-line>To github.com:ACCESS-NRI/access-hive.org.au.git</terminal-line>
    <terminal-line>* [new branch]        spongebob/fix-hive-title-issue-234 -> spongebob/fix-hive-title-issue-234</terminal-line>
    <terminal-line>branch 'spongebob/fix-hive-title-issue-234' set up to track 'origin/spongebob/fix-hive-title-issue-234'.</terminal-line>
</terminal-window>

Work locally on the branch `spongebob/fix-hive-title-issue-234`, and push the commits using the commands:

<terminal-window>
    <terminal-line data="input">git add . </terminal-line>
    <terminal-line></terminal-line>
    <terminal-line data="input">git commit -m "(#234) Fixed Hive title!" </terminal-line>
    <terminal-line>[spongebob/fix-hive-title-issue-234 7afe8c11] (#234): Fixed Hive title!</terminal-line>
    <terminal-line>1 file changed, 17 insertions(+)</terminal-line>
    <terminal-line></terminal-line>
    <terminal-line data="input">git push</terminal-line>
    <terminal-line>Enumerating objects: 11, done.</terminal-line>
    <terminal-line>Counting objects: 100% (11/11), done.</terminal-line>
    <terminal-line>Delta compression using up to 10 threads</terminal-line>
    <terminal-line>Compressing objects: 100% (6/6), done.</terminal-line>
    <terminal-line>Writing objects: 100% (6/6), 637 bytes | 637.00 KiB/s, done.</terminal-line>
    <terminal-line>Total 6 (delta 5), reused 0 (delta 0), pack-reused 0</terminal-line>
    <terminal-line>remote: Resolving deltas: 100% (5/5), completed with 5 local objects.</terminal-line>
    <terminal-line>To github.com:ACCESS-NRI/access-hive.org.au.git</terminal-line>
        <terminal-line>7263feb5..7afe8c13  spongebob/fix-hive-title-issue-234 -> spongebob/fix-hive-title-issue-234</terminal-line>
</terminal-window>

While working on a local branch, please make sure to regularly pull changes from the remote `main` into your local branch, as follows:

<terminal-window>
    <terminal-line data="input">git pull origin main</terminal-line>
    <terminal-line>From github.com:ACCESS-NRI/access-hive.org.au</terminal-line>
    <terminal-line>* branch              main -> FETCH_HEAD</terminal-line>
    <terminal-line>Already up to date.</terminal-line>
</terminal-window>

This would make sure that the local branch `spongebob/fix-hive-title-issue-234` is always in sync with the latest changes in the remote `main` branch.

## Deploying Website Preview
MkDocs includes a live preview server, so you can preview your changes as you write your documentation. The server will automatically rebuild the site upon saving. 

To build the site locally, you need to install [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) and other plugins. You can find a full list in the `requirements.txt` file (in the root of the [ACCESS-Hive Docs GitHub repository](https://github.com/ACCESS-NRI/access-hive.org.au)). Please use `pip` for the installation as some of the packages are not updated or not available on conda:

<terminal-window>
    <terminal-line data="input">pip install -r requirements.txt</terminal-line>
</terminal-window>

To start the server, open terminal and navigate to your ACCESS-Hive Docs local repository and use the command:

<terminal-window>
    <terminal-line data="input">mkdocs serve</terminal-line>
    <terminal-line>INFO     -  Building documentation...</terminal-line>
    <terminal-line>INFO     -  [macros] - Macros arguments: {'module_name': 'main', 'modules': [], 'include_dir': '', 'include_yaml': [], 'j2_block_start_string': '', 'j2_block_end_string': '',
            'j2_variable_start_string': '', 'j2_variable_end_string': '', 'on_undefined': 'keep', 'on_error_fail': False, 'verbose': False}</terminal-line>
    <terminal-line>INFO     -  [macros] - Extra variables (config file): ['generator', 'social', 'analytics']</terminal-line>
    <terminal-line>INFO     -  [macros] - Extra filters (module): ['pretty']</terminal-line>
    <terminal-line>INFO     -  Cleaning site directory</terminal-line>
    <terminal-line>INFO     -  Documentation built in 12.33 seconds</terminal-line>
    <terminal-line>INFO     -  [09:19:05] Reloading browsers</terminal-line>
    <terminal-line>INFO     -  [09:19:05] Browser connected: http://127.0.0.1:8000/about/contribute/contribute_on_github/</terminal-line>
</terminal-window>

Your documentation will be built on http://127.0.0.1:8000. Open this URL in your browser to see a preview of the documentation. The URL is given in the terminal when running the `mkdocs serve` command. Make sure you keep the command running so as to see live updates on saving your modifications. 

## Create a Pull Request for the final changes

Once the changes on your local branch `spongebob/fix-hive-title-issue-234` are ready to be integrated with the `development` branch, [create a pull request](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request) and GitHub will automatically build a preview of the documentation using [GitHub Actions](https://docs.github.com/en/actions). 

![GitHub preview](/assets/github-preview-link.png){: class="example-img"  loading="lazy"}

After a PR is created and tested, feel free to ask for reviews by tagging the ACCESS-NRI/WebOps team and reply if any changes are being requested in the PR. 

## Additional GitHub learning resources 

- [Introduction to GitHub](https://github.com/skills/introduction-to-github)
- [Setting up Git](https://docs.github.com/en/get-started/quickstart/set-up-git)
- [GitHub workflow](https://docs.github.com/en/get-started/quickstart/github-flow)
- [Using Markdown](https://github.com/skills/communicate-using-markdown)