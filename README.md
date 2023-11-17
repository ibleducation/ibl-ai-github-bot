# IBL GitHub Bot

Automatically generates tests based on `pytest`.

A cli is exposed at `ibl_github_bot/__main__.py`.

```shell
$ python -m ibl_github_bot --help
Usage: python -m ibl_github_bot [OPTIONS]

Options:
  --repo TEXT          Repository to clone. Must be of the format
                       username/reponame. eg. ibleducation/ibl-ai-bot-app
  --branch TEXT        Branch to clone repository from.
  --github-token TEXT  Github token used to authenticate and clone repository.
                       Token must have write access to the repository.
  -f, --file TEXT      Target file in repository to test. Defaults to all
                       files. You can pass multiple files with -f file1 -f
                       file2
  --cleanup            Delete cloned repository after test generation.
  --help               Show this message and exit.
```

For example:
```shell
$ python -m  ibl_github_bot --repo ibleducation/ibl-ai-bot-app --branch slack --cleanup -f ibl_ai_bot/views.py
```

> [!IMPORTANT]
> You may export your GitHub token as an environment variable or place it in a `.env` file in the current working directory. \
> Name the environment variable `GH_TOKEN` 

A new branch and related pull request will be created on the repository specified containing the generated tests. 

> [!WARNING]
> **Do not blindly merge the pull requests created. Always check out the pull request and run the tests.**


## Configuration

It's capable of loading configurations from the repository to alter its behaviour.

These configurations include specifying the programming language, frameworks used, testing library as well as module dependencies. Configurations are specified in `ibl_test_config.yaml`
Below is a sample configuration file:

```yaml
exclude:                    # project wide excludes
  - "tests"
test_library: pytest
frameworks: 
  - Django
  - Djangorestframework
modules:                     # configurations for specific modules/directories.
  directory1:
    depends_on:
      - directory2
      - directory3
    exclude:
      - "*.txt"
      - "tests.py"
  directory2:
    depends_on:
      - directory3
      - directory4
    exclude:
        - "templates"

```

The `exclude` entry lists files or directories to ignore for a module (or globally if it is a top level configuration).

Setting module dependencies appropriately can largely reduce LLM costs and context size leading to better performance. However, wrong dependency relationships can be detrimental.

When no configuration file is provided in the repository, the following configuration file is used instead:

```yaml
exclude:
    - .git
    - __pycache__
    - tests
    - migrations
    - requirements
test_library: pytest
frameworks:
    - django
    - djangorestframework
language: python
```

## TIPS FOR BEST RESULTS

1. Should the tests depend on some lesser known projects (eg. some private apps in separate repos) it is best to manually write sample tests from which the LLM can 
learn how to generate specific fixtures and how those external dependencies are used.
2. This tool works best for mono repos, where the LLM can understand the entire project scope at once.
3. Provide an `ibl_test_config.yaml` file at the root directory of the repository for optimal performance. Ensure that all entries provided are correct. Also make sure that all dependency relationship specified are exhaustive. 
4. For very small projects, dependency relationships can be ignored in favor of loading the entire project as context.

## LIMITATIONS

1. In situations where the project depends on a lesser known package that may be essential to testing the project, some tests may end up being incorrect.
2. MMerging the code generated by an LLM and may not be the best possible approach. It is essential to check out the test branch created, run the tests and issue fixes where necessary. 

