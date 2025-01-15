# ACCESS-Hive Docs
[![ACCESS-Hive Docs Badge](docs/assets/assets_for_readme/badge_hive_docs.svg)][hive-docs-website]
[![github-contributors](https://img.shields.io/github/contributors/ACCESS-NRI/access-hive.org.au?color=blue&style=plastic)][github-repo]
[![forum-users](https://img.shields.io/discourse/users?color=blue&label=forum&server=https%3A%2F%2Fforum.access-hive.org.au&style=plastic)][forum]
[![link-check](https://github.com/ACCESS-NRI/access-hive.org.au/actions/workflows/link_check.yml/badge.svg)](https://github.com/ACCESS-NRI/access-hive.org.au/actions/workflows/link_check.yml)

This repository is the implementation of the ACCESS-Hive Docs website accessible at the following URL:<br>
https://access-hive.org.au/

The ACCESS-Hive Docs website hosts the documentation for ACCESS users: getting set up, running models and model evaluation.

# How to Contribute
## For New Users

If you wish to add documentation to the ACCESS-Hive Docs website check the [contribution guide](https://access-hive.org.au/about/contribute/) for instructions.

## For [ACCESS-NRI GitHub Organisation](https://github.com/ACCESS-NRI) members

The ACCESS-Hive Docs website is built using [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/). For detailed information on markdown syntax and different features, please refer to [mkdocs-material documentation](https://squidfunk.github.io/mkdocs-material/reference/).
The suggested workflow for a contribution on the ACCESS-Hive Docs is detailed below:
1. **CLONE**

    On [GitHub](https://github.com/ACCESS-NRI/access-hive.org.au), click on the **< > Code** button (top-right) and copy the SSH remote URL (starts with git@github.com). 
    
    Now, run the following command in your local terminal to clone the ACCESS-Hive GitHub repository:

    ```ruby
    $ git clone git@github.com:ACCESS-NRI/access-hive.org.au.git
    ```

2. **MAKE CHANGES LOCALLY**

    For making changes locally, open your preferred IDE (ex. VSCode). 
    And in the terminal, create a new branch to make your local changes and then push it to GitHub:

    ```ruby
    # Create a new branch, name it as - <name>/<issue-summary>-<issue-no>

    $ git checkout -b john/fix-title-800
    ```

    ```ruby
    # Push the branch to upstream

    $ git push --set-upstream origin john/fix-title-800  
    ```

3. **COMMIT AND PUSH CHANGES**

    Make your desired changes to the branch `john/fix-title-800`, and push the commits to remote: 

    ```ruby
    # Stage the current local directory

    $ git add .
    ```

    ```ruby
    # Commit the changes, include linked issue ID and meaningful commit descriptions.

    # Avoid general descriptions, such as 'a fix', 'useful change', 'home page bug fixes' etc.

    $ git commit -m "(#800) Camel-cased hive title!"
    ```

    ```ruby
    # Push the changes to the remote of your branch

    $ git push origin john/fix-title-800 

    # or, if the remote is known, run 
    
    $ git push
    ```

4. **BE IN SYNC WITH THE `development` BRANCH**
    
    There are two main methods for integrating feature branch with the main branch:

    - Merge (creates a merge commit for every `git merge`)
    - Rebase (fast-forward merge, and linear history) 

    Recently, we are transitioning to using `git rebase` as one of the steps in our development workflow.
    This would give an opportunity to polish a feature branch before merging into the main, and also avoiding
    additional merge commits in the project history.

    For syncing our feature branch into the main branch, use the following 
    commands: 

    ```ruby
    # Skip this step if you are already working on feature branch 
    git checkout john/fix-title-800

    # Moves the entire feature branch on the tip of main branch
    git rebase origin main  # or git pull --rebase
    ```

5. **DEPLOYING WEBSITE PREVIEW**

    During development, preview the changes made using [MkDocs live preview server](https://access-hive.org.au/about/contribute/contribute_on_github/#deploying-website-preview).

6. **REVIEWING A FEATURE WITH THE PULL REQUEST**
    
    Once we are happy with the commits in the feature branch, [create a pull request](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request)
    and GitHub will automatically build a preview of the documentation using
    [GitHub Actions](https://docs.github.com/en/actions). 

    Feel free to assign `ACCESS-NRI/WebOps` team as reviewers.

> **Note** - The active branch is `development`. The `main` branch is only updated weekly 
with automatic merging (from `development` branch only).

> **Caution**: Use `git rebase` carefully and never use it on the commits that have already 
been merged in the main branch. It would appear that the project history is abruptly changed. 
Don't hesitate to chat with the hive docs team if unsure... :) 

> _Reference: [Atlassian Rebase tutorial](https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase)_
, _[Atlassian Merging vs Rebasing tutorial](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)_


## Styling guidelines for developers

Please follow the guidelines below to make the Hive Docs consistent among all the pages contributed by different people. 

- Always prefer Markdown syntax to HTML when possible;
- All internal URLs need to be ABSOLUTE links, starting with `/` which indicates the base website `https://access-hive.org.au/`. So, a reference to the page `https://access-hive.org.au/models/configurations/access-cm/` will be `/models/configurations/access-cm`. Also, all assets need to be linked starting with `/assets/...`. This because currently the link checker does not handle relative links properly.
- Titles/subtitles should NOT include: code lines/blocks, bold (titles are usually already bold), italic, links;
- Code lines/blocks need to be used for lines/blocks of code, terminal commands and file/directory paths/names;
- Italic needs to be used when referring to specific proper nouns (for example _Gadi_ or _payu_);
- Bold can be used to highlight some words (please do not overuse it);
- All types of admonitions (info, warning, etc.), collapsible and not, can be used as described in the [documentation](https://squidfunk.github.io/mkdocs-material/reference/admonitions/), but they are rendered slightly differently. For the HTML version of them, please refer to the [HTML/Markdown cheatsheet for the Hive Docs](#htmlmarkdown-cheatsheet-for-the-hive-docs).
- Instructions for different versions (for example different operative systems or different model versions) can be rendered using tabs (see [HTML/Markdown cheatsheet for the Hive Docs](#htmlmarkdown-cheatsheet-for-the-hive-docs));
- Terminal input/output examples can be rendered using [animated-terminal.js](https://github.com/atteggiani/animated-terminal.js)

### Styling Markdown using CSS
#### Attribute lists
To style Markdown using custom CSS, you can use [attribute lists](https://python-markdown.github.io/extensions/attr_list/) by adding the desired CSS style (in-line or adding classes as defined in the `access-nri.css` file) inside curly brackets (starting with a colon) right after the markdown syntax.

For example, to make an image 50%-wide and with rounded borders, you can write the following:
```
![image text](/image/url/){: style="width: 50%; border-radius: 0.6rem;" }
```
#### Markdown in HTML
You can use [Markdown in HTML](https://squidfunk.github.io/mkdocs-material/setup/extensions/python-markdown/?h=md+in+html#markdown-in-html) by adding the `markdown` attribute inside an HTML tag.<br>
You can then style the HTML tag using CSS (in-line or adding selectors to the `access-nri.css` file).

For example, to render a markdown section with half font-size, you can write the following:
```
<div markdown style="font-size: 0.5em;">
### Section with half font-size (This will appear in the table of content)

- First _bullet_ point
- Second bullet **point**
</div>
```

### Custom HTML tags
Some custom HTML tags were created (in the `js/custom-tags.js` file) to facilitate the rendering of some components.<br>
The following custom tags are available:

- **"Not supported by ACCESS-NRI" admonition**<br>
  The `<custom-not-supported/>` tag renders a preset "Not supported by ACCESS-NRI" admonition.

- **References**<br>
  The `<custom-references> ... </custom-references>` tag renders references to be used at the bottom of a page. Each new line (created by pressing <kbd>return</kbd>/<kbd>Enter</kbd>) inside `...` is rendered as a separate bullet point. A hyphen (`-`) can included at the beginning of a new line (for clarity) but it is stripped off at the time of rendering.<br>
  For example, to reference 2 links you can write:
  ```
  <custom-references>
    - [First reference](first-reference-url)
    - [Second reference](second-reference-url)
  </custom-references>
  ```

- **Simulated terminal info**<br>
  The `<custom-simulated-terminal-info/>` tag renders a preset info admonition on the pages where simulated terminal are used.<br>
  It should not be used manually as it is automatically embedded in the pages containing simulated terminals.

- **Admonitions**<br>
  For information on how to render an [admonition](https://squidfunk.github.io/mkdocs-material/reference/admonitions/#usage) please check the respective section in the [HTML/Markdown cheatsheet for the Hive Docs](#htmlmarkdown-cheatsheet-for-the-hive-docs).<br>
  The list of available admonition types is the following:
  - note
  - info
  - danger
  - abstract
  - tip
  - success
  - warning
  - failure
  - question
  - bug
  - example
  - quote
  - supported
  If you want a new admonition type to be created, please raise a [new issue](https://github.com/ACCESS-NRI/access-hive.org.au/issues/new?assignees=&labels=&projects=&template=simple-issue-template.md&title=) about it.

### HTML/Markdown Cheatsheet for the Hive Docs
|Style|Markdown Syntax|HTML Syntax|Rendered example|
|---|---|---|---|
|**Bold**|\*\*bold**|\<b>bold\</b>|**bold**|
|**Italic**|\_italic_|\<i>italic\</i>|_italic_|
|**Code line**|\`code line`|\<code>code line\</code>|`code line`|
|**Code block**|\```<br>&emsp;code block<br>```|\<pre><br>&emsp;\<code>code block\</code><br>\</pre>|<pre><code>code block</code></pre>|
|**[Admonition](https://squidfunk.github.io/mkdocs-material/reference/admonitions/#usage) (not collapsible)**|!!! warning<br>&emsp;this is a warning admonition|\<div class="admonition warning"><br>&emsp;this is a warning<br>&emsp;admonition<br>\<div>|![warning admonition](docs/assets/assets_for_readme/warning_admonition.png)|
|**Collapsible [Admonition](https://squidfunk.github.io/mkdocs-material/reference/admonitions/#usage)**|??? warning title<br>&emsp;this is a collapsible warning admonition|\<details class="warning"><br>&emsp;\<summary><br>&emsp;&emsp;\<p><br>&emsp;&emsp;&emsp;this is a collapsible<br>&emsp;&emsp;&emsp;warning admonition<br>&emsp;&emsp;\</p><br>&emsp;\</summary><br>\</details>|![collapsible warning admonition](docs/assets/assets_for_readme/collapsible_warning_admonition.gif)|
|**Tabs**|N/A|**Labels:**<br>\<div class="tabLabels" label="your-tab-label"><br>&emsp;\<button id="id-tab1"><br>&emsp;&emsp;Tab1<br>&emsp;\</button><br>&emsp;\<button id="id-tab2"><br>&emsp;&emsp;Tab2<br>&emsp;\</button><br>\</div><br><br>**Contents:**<br>\<div tabcontentfor="id-tab1"><br>&emsp;&emsp;Content for tab1<br>\</div><br>\<div contentfor="id-tab2"><br>&emsp;&emsp;Content for tab2<br>\</div><br><br>A content for a specific tab `id` can be included or not independently from the other contents.|![tabs](docs/assets/assets_for_readme/tabs.gif)|


# License
The ACCESS-Hive Docs website is covered by the [CC-BY 4.0 license](https://creativecommons.org/licenses/by/4.0/legalcode).

However, the material linked to from ACCESS-Hive Docs is covered by various licensing agreements. Our users should directly refer to the terms and conditions of any material they are using to understand their rights and responsibilities.

[hive-docs-website]: https://access-hive.org.au
[github-repo]: https://github.com/ACCESS-NRI/access-hive.org.au
[forum]: https://forum.access-hive.org.au