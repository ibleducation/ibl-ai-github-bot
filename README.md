# IBL GITHUB BOT

Ibl Github bot is a bot that automatically generates tests for ibl projects
Tests generated are based on `pytest`

A cli is exposed at `ibl_github_bot/__main__.py`.
You can use this cli as
```
$ python -m ibl_github_bot --help
Usage: python -m ibl_github_bot [OPTIONS]

Options:
  --repo TEXT          Repository to clone. Must be of the format
                       username/reponame. eg. ibleducation/ibl-ai-bot-app
  --branch TEXT        Branch to clone repository from.
  --github-token TEXT  Github token used to authenticate and clone repository.
                       Token must have write access to the repository.
  --cleanup            Delete cloned repository after test generation.
  --help               Show this message and exit.
```

For example
```
$ python -m  ibl_github_bot --repo ibleducation/ibl-ai-bot-app --branch slack --cleanup
```
> [!IMPORTANT]
> You may export github token as environment variable or place it in a `.env` file in the current working directory. \
> Name the environment variable `GH_TOKEN`

A new branch and related pull request will be created on the repository specified containing the tests generated.

> [!WARNING]
> **Do not blindly merge the pull requests created**


## TIPS FOR BEST RESULTS
1. Should the tests depend on some less known projects (eg. some private apps in seperate repos) it is best to manually write some sample tests from which the llm can 
learn how to generate specific fixtures and how those external dependencies are used.
2. This tool works best for mono repos, where the llm can understand the entire project scope at a go.


## LIMITATIONS
1. In situations where the project depends on a less known package that may be essential to testing the project, some tests may end up being incorrect.
2. Code generated are generated by an llm and may not be the best possible approach. It is essential to checkout the test branch created, run the tests and issue fixes where necessary. 
