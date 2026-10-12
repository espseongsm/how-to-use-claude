# How to Use Personal and Work Claude Accounts Together on macOS

Switching logins gets tedious when you use both a personal and a work account. If you separate the CLI config folder and the Desktop login-data folder, you can use each account on the same Mac.

The setup below is for macOS and zsh. The personal account uses the default profile. The work account gets the name `work`.

| Purpose          | How to launch after setup                |
| ---------------- | ---------------------------------------- |
| Personal CLI     | `claude`                                 |
| Work CLI         | `claude-work`                            |
| Personal Desktop | Launch Claude from Finder or Spotlight   |
| Work Desktop     | `claude-work-app`                        |

## 1. Install the CLI and Desktop

If you don't have the CLI, run the official install command in a terminal.

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Download Desktop from the official download page and install it to `/Applications/Claude.app`. If it is already installed, go to the next step.

## 2. Separate the CLI accounts

Open your terminal config file. If the file doesn't exist, saving creates it.

```bash
nano "$HOME/.zshrc"
```

Add this line.

```bash
alias claude-work='CLAUDE_CONFIG_DIR="$HOME/.claude-work" claude'
```

The personal CLI uses the default `~/.claude` folder. The work CLI uses the `~/.claude-work` folder. The alias sets `CLAUDE_CONFIG_DIR` inside itself, so it applies only to the work command. See the [official environment variable reference](https://code.claude.com/docs/en/env-vars) and the [authentication guide](https://code.claude.com/docs/en/authentication).

In nano, press `Ctrl+O`, then `Enter` to save. Press `Ctrl+X` to exit. Apply the settings, then log in to each account.

```bash
source "$HOME/.zshrc"
claude auth login       # Log in with your personal account
claude-work auth login  # Log in with your work account
```

On the browser authentication screen, choose the matching account. After that, you can run `claude` and `claude-work` at the same time in separate terminals.

## 3. Separate the Desktop accounts

Open the regular Claude app and log in with your personal account. Launch the work app with a separate login-data folder.

Add the function below to the same `~/.zshrc`. In this example, `claude-work-app` is a launch command you can rename.

```bash
claude-work-app() {
  open -n -a "/Applications/Claude.app" \
    --env "CLAUDE_CONFIG_DIR=$HOME/.claude-work" \
    --args \
    --user-data-dir="$HOME/Library/Application Support/Claude-Work"
}
```

Save the file, apply the settings, and launch the work app.

```bash
source "$HOME/.zshrc"
claude-work-app
```

In the new window that opens, log in with your work account. You must log in separately for the CLI and for the app.

- `--user-data-dir` separates the work app's login data.
- `--env` passes the work config path to the work app.

This separation relies on launch options. It is not a setup guaranteed by an official multi-profile feature.

The function above opens a new app instance every time you call it. If the work app is already open, use the existing window.

## 4. Verify your accounts

In the CLI, check the login email with these commands. See the [auth command reference](https://code.claude.com/docs/en/cli-reference).

```bash
claude auth status --text
claude-work auth status --text
```

In Desktop, check the email in each window's account menu.

If the shell doesn't recognize the short command, open a new terminal or run `source "$HOME/.zshrc"`. If an alias or function with the same name already exists, check the existing definition and edit it.

Last verified: 2026-10-07. This is an example for macOS and zsh.

---

## Summary for LinkedIn

Do you use Claude with both a personal and a work account?

On macOS, you can split the config path per account and run each account separately.

- Personal CLI: `claude`
- Work CLI: `claude-work`
- Work Desktop: `claude-work-app`

The CLI uses `CLAUDE_CONFIG_DIR` to separate the config folder. Desktop uses `--user-data-dir` to separate the login data folder. Log in once to each account in the CLI and the app. After that, you can launch each one with a short command.

I wrote up the full guide for my tech blog: basic install, per-account setup, and login verification. The Desktop setup relies on launch options.

#Claude #ClaudeCode #DevEnvironment
