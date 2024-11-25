# Preparing to Develop dbt-databricks

## Install Hatch

For best experience, [Hatch](https://hatch.pypa.io/dev/)--a modern Python project manager--should be installed globally.
Find installation instructions [here](https://hatch.pypa.io/dev/install/#installers).

## Getting Hatch to Work with your IDE

The main thing to getting our project and all expected functionality to work with your IDE is create the default environment for the project, and point your IDE at the interpreter in that project.
For your sanity, it is recommended that you install your virtual environment into your dbt-databricks folder.
You can accomplish this by executing

```
hatch config set dirs.env.virtual
```

and setting to `.hatch`.
This tells Hatch to install your environments into a folder called `.hatch` inside of your project.
Once you have changed this setting, execute

```
hatch env create
```

to create the default environment, populating the `.hatch` folder.
In your IDE, you should hopefully see an interpreter in this folder recommended when you enter the set interpreter prompt.
If not, selecting `.hatch/dbt-databricks/bin/python` as the executable for your interpretor should get you IDE integration.

### VS Code Settings

If you are using VS Code, here are recommended settings (to be included in `.vscode/settings.json`):

```
{
    "mypy-type-checker.importStrategy": "fromEnvironment",
    "python.testing.unittestEnabled": false,
    "python.testing.pytestEnabled": true,
    "python.testing.pytestArgs": [
        "--color=yes",
        "-n=auto",
        "--dist=loadscope",
    ],
    "[python]": {
        "editor.insertSpaces": true,
        "editor.tabSize": 4,
        "editor.formatOnSave": true,
        "editor.formatOnType": true,
        "editor.defaultFormatter": "charliermarsh.ruff",
        "editor.codeActionsOnSave": {
            "source.organizeImports": "explicit"
        },
    },
    "logViewer.watch": [
        {
            "title": "dbt logs",
            "pattern": "${workspaceFolder}/logs/**/dbt.log"
        }
    ],
    "logViewer.showStatusBarItemOnChange": true,
    "editor.formatOnSave": true,
}
```

To get all of these features to work, you will need to install 'Log Viewer', 'Mypy Type Checker', and 'Ruff'.

When everything is working as intended you will get the following behaviors:

- [mypy](https://mypy-lang.org/) type-checking
- [ruff](https://docs.astral.sh/ruff/) formatting and linting (including import organization)
- [pytest](https://docs.pytest.org/en/stable/) test running from the Test Explorer
- The ability to quickly jump to test logs as they are produced with the Log Viewer extension

To add test debugging capabilities, add the following to `.vscode/launch.json`:

```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: Debug Tests",
            "type": "debugpy",
            "request": "launch",
            "program": "${file}",
            "purpose": ["debug-test"],
            "console": "integratedTerminal",
            "justMyCode": false,
        }
    ]
}
```

## Hatch from the CLI

There are a number of useful scripts to invoked from the CLI.
While it is useful to learn about Hatch to understand environments and all of the functionalities, you can run the commands below to accomplish common developer activities:

```
hatch run precommit-setup
```

will install our precommit hooks.
You can run the hooks without installing them with

```
hatch run code-quality
```

This runs `ruff` and `mypy` against the project.

```
hatch run unit
```

will run the unit tests against Python 3.9, while

```
hatch run test:unit
```

will run the unit tests against all supported Python versions.

```
hatch run {cluster-e2e | uc-cluster-e2e | sqlw-e2e}
```

will run the functional tests against the HMS cluster, UC cluster, or SQL Warehouse respectively.

```
hatch build
```

builds the `sdist` and `wheel` distributables.